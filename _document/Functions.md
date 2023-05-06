# NEXT 13 :Functions

- [NEXT 13 :Functions](#next-13-functions)
  - [Overview](#overview)
  - [**cookies**](#cookies)
    - [`cookies().get(<name>)`](#cookiesgetname)
    - [`cookies().getAll()`](#cookiesgetall)
    - [`cookies().has(<name>)`](#cookieshasname)
  - [**headers**](#headers)
  - [imageResponse](#imageresponse)
  - [NextRequest](#nextrequest)
    - [Instance properties](#instance-properties)
    - [Instance methods](#instance-methods)
    - [cookies](#cookies-1)
    - [`nextUrl`](#nexturl)
  - [NextResponse](#nextresponse)
    - [Constructor](#constructor)
    - [Instance properties](#instance-properties-1)
    - [Static methods](#static-methods)
    - [Instance methods](#instance-methods-1)
    - [`NextResponse.json()`](#nextresponsejson)
    - [`NextResponse.redirect()`](#nextresponseredirect)
    - [`NextResponse.rewrite()`](#nextresponserewrite)
    - [`NextResponse.next()`](#nextresponsenext)
    - [`notFound()`](#notfound)
    - [`redirect()`](#redirect)
  - [`revalidatePath()`](#revalidatepath)
  - [`revalidateTag()`](#revalidatetag)
  - [`useParams()`](#useparams)
  - [`usePathname()`](#usepathname)
  - [`useRouter()`](#userouter)
    - [`useRouter()` Methods](#userouter-methods)
  - [`useSearchParams()`](#usesearchparams)
    - [`useSearchParams()` Methods](#usesearchparams-methods)
  - [`useSelectedLayoutSegment()`](#useselectedlayoutsegment)
  - [`useSelectedLayoutSegments()`](#useselectedlayoutsegments)

## Overview

- `cookies()`
  - `cookies().get(<name>)`
  - `cookies().getAll()`
  - `cookies().has(<name>)`
- `headers()`
  - `headers.entries()`
  - `headers.get(<name>)`
  - `headers.forEach()`
  - `headers.keys()`
  - `headers.values()`
  - `headers.has(<name>)`
- `NextRequest`
  - `request.cookies.get(<name>)`
  - `request.cookies.getAll()`
  - `request.cookies.has(<name>)`
  - `request.cookies.set(<name>, <value>, <options>)`
  - `request.nextUrl` : `URL` Object
- `NextResponse`
  - `NextResponse.json(<body>)`
  - `NextResponse.redirect(<url>, <status>)`
  - `NextResponse.rewrite(<url>)`
  - `NextResponse.next()` : For Middleware
- `revalidatePath(path: string)`
- `revalidateTag(tag: string)`
- `useParams()`
- `usePathname()`
- `useRouter()`
  - `router.push(<url>)`
  - `router.replace(<url>)`
  - `router.refresh()`
  - `router.back()`
  - `router.forward()`
- `useSearchParams()`
  - `searchParams.get(<name>)`
  - `searchParams.getAll()`
  - `searchParams.has(<name>)`
  - `searchParams.set(<name>, <value>)`
  - `searchParams.delete(<name>)`

## **cookies**

The cookies function allows you to read the HTTP incoming request cookies from a server component. It is read-only, meaning you cannot set / delete the outgoing request cookies.

`cookies()` is a Dynamic Function whose returned values cannot be known ahead of time. Using it in a layout or page will opt a route into dynamic rendering at request time.

### `cookies().get(<name>)`

```javascript
import { cookies } from "next/headers";

export default function Page() {
  const cookieStore = cookies();
  const theme = cookieStore.get("theme");
  return "...";
```

### `cookies().getAll()`

```javascript
import { cookies } from "next/headers";

export default function Page() {
  const cookieStore = cookies();
  return cookieStore.getAll().map((cookie) => (
    <div key={cookie.name}>
      <p>Name: {cookie.name}</p>
      <p>Value: {cookie.value}</p>
    </div>
  ));
}
```

### `cookies().has(<name>)`

```javascript
import { cookies } from "next/headers";

export default function Page() {
  const cookiesList = cookies();
  const hasCookie = cookiesList.has("theme");
  return "...";
}
```

## **headers**

It is read-only, meaning you cannot `set` / `delete` the outgoing request headers.

```javascript
import { headers } from "next/headers";

export default function Page() {
  const headersList = headers();
  const referer = headersList.get("referer");
  return <div>Referer: {referer}</div>;
}
```

- `Headers.entries()`: Returns an `iterator` allowing to go through all key/value pairs contained in this object.
- `Headers.forEach()`: Executes a provided function once for each key/value pair in this `Headers` object.
- `Headers.get()`: Returns a `String` sequence of all the values of a header within a `Headers` object with a given name.
- `Headers.has()`: Returns a `boolean` stating whether a `Headers` object contains a certain header.
- `Headers.keys()`: Returns an `iterator` allowing you to go through all keys of the key/value pairs contained in this object.
- `Headers.values()`: Returns an `iterator` allowing you to go through all values of the key/value pairs contained in this object.

```javascript
import { headers } from "next/headers";
async function getUser() {
  const headersInstance = headers();
  const authorization = headersInstance.get("authorization");
  // Forward the authorization header
  const res = await fetch("...", {
    headers: { authorization },
  });
  return res.json();
}
export default async function UserPage() {
  const user = await getUser();
  return <h1>{user.name}</h1>;
}
```

## imageResponse

## NextRequest

NextRequest extends the Web Request API with additional convenience method

### Instance properties

- `body`: The body of the request as a string.
- `bodyUsed`: A boolean indicating whether this request has been used yet.
- `cache`: The cache mode of the request (e.g., `default`, `reload`, `no-cache`).
- `credentials`: The request credentials of the request (e.g., `omit`, `same-origin`, `include`).
- `destination`: The destination of the request (e.g., `document` or `script`).
- `headers`: A Headers object containing the request's headers.
- `integrity`: The subresource integrity value of the request (e.g., `sha256-AAAA`).
- `method`: The request's method (e.g., `GET`, `POST`).
- `mode`: The mode of the request (e.g., `cors`, `no-cors`, `same-origin`, `navigate`).
- `redirect`: The mode for how redirects are handled. It may be one of `follow`, `error`, or `manual`.
- `referrer`: A string indicating the referrer of the request (e.g., `client`).
- `referrerPolicy`: A referrer policy to use when fetching the request (e.g., `no-referrer`).
- `signal`: An AbortSignal object instance; allows you to communicate with a fetch request and abort it if desired via an AbortController.
- `url`: The URL of the request.

### Instance methods

- `json()`: Returns a promise that resolves with the result of parsing the body text as JSON.
- `text()`: Returns a promise that resolves with the result of parsing the body text as text.
- `arrayBuffer()`: Returns a promise that resolves with the result of parsing the body text as an ArrayBuffer.
- `formData()`: Returns a promise that resolves with the result of parsing the body text as form data (includes files).
- `blob()`: Returns a promise that resolves with the result of parsing the body text as a Blob.
- `clone()`: Creates a clone of a `Request` object.

### cookies

```javascript
// Given incoming request /home
// Set a cookie to hide the banner
// request will have a `Set-Cookie:show-banner=false;path=/home` header
request.cookies.set("show-banner", "false");
// Given incoming request /home
// { name: 'show-banner', value: 'false', Path: '/home' }
request.cookies.get("show-banner");
// Given incoming request /home
// [
//   { name: 'experiments', value: 'new-pricing-page', Path: '/home' },
//   { name: 'experiments', value: 'winter-launch', Path: '/home' },
// ]
request.cookies.getAll("experiments");
// Alternatively, get all cookies for the request
request.cookies.getAll();
// Returns true for deleted, false is nothing is deleted
request.cookies.delete("experiments");
// Returns true if cookie exists, false if it does not
request.cookies.has("experiments");
request.cookies.clear();
```

### `nextUrl`

Extends the native `URL` API with additional convenience methods, including Next.js specific properties.

```javascript
// Given a request to /home, pathname is /home
request.nextUrl.pathname;
// Given a request to /home?name=lee, searchParams is { 'name': 'lee' }
request.nextUrl.searchParams;
```

## NextResponse

### Constructor

- `Response()`: Creates a new Response object.

### Instance properties

- `body`: A simple getter used to expose a `ReadableStream` of the body contents.
- `bodyUsed`: A boolean indicating whether this response has been used yet.
- `headers`: A Headers object containing the associated headers of the response.
- `ok`: A boolean indicating whether the response was successful (status in the range 200–299) or not.
- `redirected`: A boolean indicating whether this response is the result of a redirect (that is, its URL list has more than one entry).
- `status`: The status code of the response. (This will be 200 for a success).
- `statusText`: The status message corresponding to the status code. (e.g., OK for 200).
- `type`: The type of the response (e.g., `basic`, `cors`).
- `url`: The URL of the response.

### Static methods

- `error()`: Creates a new response with a different status code and status message.
- `redirect()`: Creates a new response with a different URL.

### Instance methods

- `arrayBuffer()`: Returns a promise that resolves with an ArrayBuffer.
- `blob()`: Returns a promise that resolves with a Blob.
- `formData()`: Returns a promise that resolves with a FormData object.
- `json()`: Returns a promise that resolves with a JSON object.
- `text()`: Returns a promise that resolves with a string.`
- `clone()`: Creates a clone of a `Response` object.

### `NextResponse.json()`

```javascript
import { NextResponse } from "next/server";

export async function GET(request: Request) {
  return NextResponse.json({ hello: "Next.js" });
}
```

### `NextResponse.redirect()`

```javascript
import { NextResponse } from "next/server";

export async function GET(request: Request) {
  return NextResponse.redirect(new URL("/new", request.url));
}
```

The `URL` can be created and modified before being used in the `NextResponse.redirect()` method. For example, you can use the `request.nextUrl` property to get the current URL, and then modify it to redirect to a different URL.

```javascript
import { NextResponse } from "next/server";

// Given an incoming request...
const loginUrl = new URL("/login", request.url);
// Add ?from=/incoming-url to the /login URL
loginUrl.searchParams.set("from", request.nextUrl.pathname);
// And redirect to the new URL
return NextResponse.redirect(loginUrl);
```

### `NextResponse.rewrite()`

Produce a response that rewrites (proxies) the given `URL` while preserving showing the original URL.

```javascript
import { NextResponse } from "next/server";

// Incoming request: /about, browser shows /about
// Rewritten request: /proxy, browser shows /about
return NextResponse.rewrite(new URL("/proxy", request.url));
```

### `NextResponse.next()`

The `next()` method is useful for **Middleware**, as it allows you to return early and continue routing.

```javascript
import { NextResponse } from "next/server";

return NextResponse.next();
```

You can also forward headers when producing the response:

```javascript
import { NextResponse } from "next/server";

// Given an incoming request...
const newHeaders = new Headers(request.headers);
// Add a new header
newHeaders.set("x-version", "123");
// And produce a response with the new headers
return NextResponse.next({
  request: {
    // New request headers
    headers: newHeaders,
  },
});
```

### `notFound()`

Invoking the `notFound()` function throws a `NEXT_NOT_FOUND` error and terminates rendering of the route segment in which it was thrown. Specifying a not-found file allows you to gracefully handle such errors by rendering a Not Found UI within the segment.

```javascript
// app/user/[id]/page.js
import { notFound } from "next/navigation";

async function fetchUsers(id) {
  const res = await fetch("https://...");
  if (!res.ok) return undefined;
  return res.json();
}

export default async function Profile({ params }) {
  const user = await fetchUser(params.id);

  if (!user) {
    notFound();
  }

  // ...
}
```

### `redirect()`

Invoking the `redirect()` function throws a `NEXT_REDIRECT` error and terminates rendering of the route segment in which it was thrown. `redirect()` can be called with a relative or an absolute URL.

```javascript
// app/team/[id]/page.js
import { redirect } from "next/navigation";

async function fetchTeam(id) {
  const res = await fetch("https://...");
  if (!res.ok) return undefined;
  return res.json();
}
export default async function Profile({ params }) {
  const team = await fetchTeam(params.id);
  if (!team) {
    redirect("/login");
  }
  // ...
}
```

_`redirect()` does not require you to use `return redirect()` due to using the TypeScript `never` type._

## `revalidatePath()`

`revalidatePath` allows you to revalidate data associated with a specific path. This is useful for scenarios where you want to update your cached data without waiting for a revalidation period to expire.

```javascript
// app/api/revalidate/route.ts
import { NextRequest, NextResponse } from "next/server";
import { revalidatePath } from "next/cache";

export async function GET(request: NextRequest) {
  const path = request.nextUrl.searchParams.get("path") || "/";
  revalidatePath(path);
  return NextResponse.json({ revalidated: true, now: Date.now() });
}
```

- `revalidatePath(path: string): void;` is available in both Node.js and Edge runtimes.
- `path`: A string representing the path associated with the data you want to revalidate.
- `revalidatePath` does not return any value.

## `revalidateTag()`

`revalidateTag` allows you to revalidate data associated with a specific cache tag. This is useful for scenarios where you want to update your cached data without waiting for a revalidation period to expire.

## `useParams()`

`useParams` is a **Client Component** hook that lets you read a route's dynamic params filled in by the current URL.

```javascript
"use client";
import { useParams } from "next/navigation";
export default function ExampleClientComponent() {
  const params = useParams();
  // Route -> /shop/[tag]/[item]
  // URL -> /shop/shoes/nike-air-max-97
  // `params` -> { tag: 'shoes', item: 'nike-air-max-97' }
  console.log(params);
}
```

## `usePathname()`

`usePathname` is a **Client Component** hook that lets you read the current URL's pathname.

```javascript
"use client";

import { usePathname } from "next/navigation";

export default function ExampleClientComponent() {
  const pathname = usePathname();
  return <>Current pathname: {pathname}</>;
}
```

## `useRouter()`

The `useRouter` hook allows you to programmatically change routes inside **Client Components**.

```javascript
"use client";

import { useRouter } from "next/navigation";
export default function Page() {
  const router = useRouter();
  return (
    <button type="button" onClick={() => router.push("/dashboard")}>
      Dashboard
    </button>
  );
}
```

### `useRouter()` Methods

- `push(url: string, options?: RouterOptions): void;` : Perform a client-side navigation to the provided route. Adds a new entry into the browser’s history stack.
- `replace(url: string, options?: RouterOptions): void;` : Perform a client-side navigation to the provided route without adding a new entry into the browser’s history stack.
- `refresh(): void;` : Refresh the current route. Making a new request to the server, re-fetching data requests, and re-rendering Server Components.
- `back(): void;` : Go back one entry in the browser’s history stack.
- `forward(): void;` : Go forward one entry in the browser’s history stack.
- `prefetch(url: string): void;` : Prefetch the provided route for faster client-side transitions.

**NOTE:**

- The `push()` and `replace()` methods will perform a soft navigation if the new route has been prefetched, and either, doesn't include dynamic segments or has the same dynamic parameters as the current route.
- `next/link` automatically prefetch routes as they become visible in the viewport.

## `useSearchParams()`

`useSearchParams` is a **Client Component** hook that lets you read the current URL's query string.

```javascript
"use client";
import { useSearchParams } from "next/navigation";
export default function SearchBar() {
  const searchParams = useSearchParams();
  const search = searchParams.get("search");
  // URL -> `/dashboard?search=my-project`
  // `search` -> 'my-project'
  return <>Search: {search}</>;
}
```

### `useSearchParams()` Methods

- `get(name: string): string | null;` : Returns the value of the first search parameter with the provided name.
- `getAll(name: string): string[];` : Returns all search parameters with the provided name.
- `has(name: string): boolean;` : Returns true if the search parameters contain a parameter with the provided name.
- `keys(): string[];` : Returns an iterator allowing iteration through all keys of the key/value pairs contained in the search parameters.
- `values(): string[];` : Returns an iterator allowing iteration through all values of the key/value pairs contained in the search parameters.
- `entries(): [string, string][];` : Returns an iterator allowing iteration through all key/value pairs contained in the search parameters.
- `forEach(callback: (value: string, name: string, searchParams: URLSearchParams) => void): void;` : Executes the provided callback function for each search parameter.
- `toString(): string;` : Returns a string containing a query string suitable for use in a URL.

## `useSelectedLayoutSegment()`

## `useSelectedLayoutSegments()`
