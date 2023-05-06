# NEXT 13 :Data Fetching

- [NEXT 13 :Data Fetching](#next-13-data-fetching)
  - [**Overview**](#overview)
  - [**`async`/`await` in Server Components**](#asyncawait-in-server-components)
  - [**Static \& Dynamic Data Fetching**](#static--dynamic-data-fetching)
  - [**Data Fetching Patterns**](#data-fetching-patterns)
    - [**Parallel Data Fetching**](#parallel-data-fetching)
    - [**Sequential Data Fetching**](#sequential-data-fetching)
  - [**Blocking Rendering in a Rout**](#blocking-rendering-in-a-rout)
  - [**Caching Data**](#caching-data)
    - [**Segment-level Cache**](#segment-level-cache)
    - [**Per-Request Cache**](#per-request-cache)
  - [**Preload pattern with `cache()`**](#preload-pattern-with-cache)
  - [**Revalidating Data**](#revalidating-data)

## **Overview**

- `fetch()`
  - `cache: <string>`:
    - `force-cache` default
    - `no-store` : To fetch fresh data on every fetch request
  - `next:{options}`:
    - `revalidate: <number>`
      - `false`: Cache the resource indefinitely.
      - `0`: Prevent the resource from being cached.
      - `<number>`: Revalidate the resource every 10 seconds.

`fetch()` is a Web API used to fetch remote resources that returns a promise. React extends `fetch` to provide automatic request deduping, and Next.js extends the `fetch` options object to allow each request to set its own caching and revalidating.

## **`async`/`await` in Server Components**

```javascript
async function getData() {
  const res = await fetch("https://api.example.com/...");
  if (!res.ok) {
    throw new Error("Failed to fetch data");
  }
  return res.json();
}
export default async function Page() {
  const data = await getData();

  return <main></main>;
}
```

## **Static & Dynamic Data Fetching**

By default, `fetch` will automatically fetch and cache data indefinitely.

```javascript
fetch("https://..."); // cache: 'force-cache' is the default
```

To revalidate cached data at a timed interval, you can use the `next.revalidate` option in `fetch()` to set the `cache` lifetime of a resource (in seconds).

```javascript
fetch("https://...", { next: { revalidate: 10 } });
```

To fetch fresh data on every fetch request, use the cache: 'no-store' option.

```javascript
fetch("https://...", { cache: "no-store" });
```

## **Data Fetching Patterns**

### **Parallel Data Fetching**

```javascript
import Albums from "./albums";

async function getArtist(username) {
  const res = await fetch(`https://api.example.com/artist/${username}`);
  return res.json();
}
async function getArtistAlbums(username) {
  const res = await fetch(`https://api.example.com/artist/${username}/albums`);
  return res.json();
}

export default async function Page({ params: { username } }) {
  // Initiate both requests in parallel
  const artistData = getArtist(username);
  const albumsData = getArtistAlbums(username);
  // Wait for the promises to resolve
  const [artist, albums] = await Promise.all([artistData, albumsData]);
  return (
    <>
      <h1>{artist.name}</h1>
      <Albums list={albums}></Albums>
    </>
  );
}
```

By starting the fetch prior to calling await in the Server Component, each request can eagerly start to fetch requests at the same time. This sets the components up so you can avoid waterfalls.

To improve the user experience, you can add a **suspense boundary** to break up the rendering work and show part of the result as soon as possible:

```javascript
export default async function Page({ params: { username } }) {
  // Initiate both requests in parallel
  const artistData = getArtist(username);
  const albumData = getArtistAlbums(username);

  // Wait for the artist's promise to resolve first
  const artist = await artistData;

  return (
    <>
      <h1>{artist.name}</h1>
      {/* Send the artist information first, and wrap albums in a suspense boundary */}
      <Suspense fallback={<Loading />}>
        <Albums promise={albumData} />
      </Suspense>
    </>
  );
}

// Albums Component
async function Albums({ promise }) {
  // Wait for the albums promise to resolve
  const albums = await promise;

  return (
    <ul>
      {albums.map((album) => (
        <li key={album.id}>{album.name}</li>
      ))}
    </ul>
  );
}
```

### **Sequential Data Fetching**

To fetch data sequentially, you can fetch directly inside the component that needs it, or you can await the result of fetch inside the component that needs it:

```javascript
async function Playlists({ artistID }) {
  // Wait for the playlists
  const playlists = await getArtistPlaylists(artistID);

  return (
    <ul>
      {playlists.map((playlist) => (
        <li key={playlist.id}>{playlist.name}</li>
      ))}
    </ul>
  );
}

export default async function Page({ params: { username } }) {
  // Wait for the artist
  const artist = await getArtist(username);

  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<Loading />}>
        <Playlists artistID={artist.id} />
      </Suspense>
    </>
  );
}
```

By fetching data inside the component, each fetch request and nested segment in the route cannot start fetching data and rendering until the previous request or segment has completed.

## **Blocking Rendering in a Rout**

By fetching data in a layout, rendering for all route segments beneath it can only start once the data has finished loading.

In the `app` directory, you have additional options to explore:

1. First, you can use loading.js to show an instant loading state from the server while streaming in the result from your data fetching function.
2. Second, you can move data fetching lower in the component tree to only block rendering for the parts of the page that need it. For example, moving data fetching to a specific component rather than fetching it at the root layout.

_Whenever possible, it's best to fetch data in the segment that uses it. This also allows you to show a loading state for only the part of the page that is loading, and not the entire page_

---

## **Caching Data**

There are two ways of caching in Next.js: Segment-level and Per-Request Cache.

### **Segment-level Cache**

Segment-level caching allows you to cache and revalidate data used in route segments.

This mechanism allows different segments of a path to control the cache lifetime of a route.Each `page.tsx` and `layout.tsx` in the route hierarchy can export a revalidate value that sets the revalidation time for the route.

```javascript
// app/page.tsx
export const revalidate = 60; // revalidate this page every 60 seconds
```

```javascript
// revalidate this page every 10 seconds, since the getData's fetch
// request has `revalidate: 10`.
async function getData() {
  const res = await fetch("https://...", { next: { revalidate: 10 } });
  return res.json();
}

export default async function Page() {
  const data = await getData();
  // ...
}
```

If a page, layout, and fetch request all specify a revalidation frequency then the lowest value of the three will be used.

### **Per-Request Cache**

Per-request caching allows you to cache and deduplicate requests on a per-request basis.

React exposes a new function, `cache()`, that memoizes the result of a wrapped function. The same function called with the same arguments will reuse a cached value instead of re-running the function. This `cache()` function was introduced in the first-class support for promises RFC.

You can use `cache()` to deduplicate data fetches on a per-request basis. If a function instance with the same arguments has been called before, anywhere in the server request, then we can return a cached value.

```javascript
// utils/getUser.ts
import { cache } from 'react';

export const getUser = cache(async (id: string) => {
  const user = await db.user.findUnique({ id });
  return user;
});

// app/user/[id]/layout.tsx
import { getUser } from '@utils/getUser';

export default async function UserLayout({ params: { id } }) {
  const user = await getUser(id);
  // ...
}

// app/user/[id]/page.tsx
import { getUser } from '@utils/getUser';

export default async function Page({
  params: { id },
}: {
  params: { id: string };
}) {
const user = await getUser(id);
```

Although the getUser() function is called twice in the example above, only one query will be made to the database. This is because getUser() is wrapped in cache(), so the second request can reuse the result from the first request.

_NOTE:_

- `fetch()` has already been patched to include support for `cache()` automatically, so you don't need to wrap functions that use `fetch()` with `cache()`. See automatic request deduping for more information.
- In this new model, we recommend fetching data directly in the component that needs it, even if you're requesting the same data in multiple components instead of prop drilling
- You can wrap functions you don't control such as third-party libraries with `cache` for the same per-request caching behavior.
- Using `cache` allows shared data fetching without having to know if a parent layout was rendered during this request.
- We recommend using the `server-only` package to make sure server data fetching functions are not accidentally used on the client.

## **Preload pattern with `cache()`**

As a pattern, we suggest optionally exposing a `preload()` export in utilities or components that do data fetching

---

## **Revalidating Data**
