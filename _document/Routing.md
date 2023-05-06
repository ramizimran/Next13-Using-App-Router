# NEXT 13 : Routing

- [NEXT 13 : Routing](#next-13--routing)
  - [Features Overview](#features-overview)
  - [**Routing**](#routing)
  - [**Routing Fundamentals**](#routing-fundamentals)
    - [**The app Directory**](#the-app-directory)
    - [**Nested Routes**](#nested-routes)
    - [**File Conventions**](#file-conventions)
    - [**Component Hierarchy**](#component-hierarchy)
    - [**Defining Routes**](#defining-routes)
    - [**Route Groups**](#route-groups)
    - [**Creating multiple root layouts**](#creating-multiple-root-layouts)
    - [**Dynamic Segments**](#dynamic-segments)
    - [**Nested Dynamic Segments**](#nested-dynamic-segments)
    - [**Catch-all Segments**](#catch-all-segments)
    - [**Optional Catch-all Segments**](#optional-catch-all-segments)
  - [**Pages and Layouts**](#pages-and-layouts)
    - [**Pages**](#pages)
    - [**Layouts**](#layouts)
    - [**Root Layout (Required)**](#root-layout-required)
    - [**Nested Layouts**](#nested-layouts)
    - [**Linking and Navigating**](#linking-and-navigating)
    - [**`<Link>` Component**](#link-component)
    - [**Linking to Dynamic Segments**](#linking-to-dynamic-segments)
    - [`useRouter() Hook`](#userouter-hook)
    - [**Invalidating the Cache**](#invalidating-the-cache)
    - [**Prefetching**](#prefetching)
  - [**Loading UI**](#loading-ui)
  - [**Error Handling**](#error-handling)
    - [Recovering From Errors](#recovering-from-errors)
  - [**Route Handlers**](#route-handlers)
    - [**Extended `NextRequest` and `NextResponse` APIs**](#extended-nextrequest-and-nextresponse-apis)
    - [**Revalidating Static Data**](#revalidating-static-data)
    - [**Dynamic Functions**](#dynamic-functions)

## Features Overview

- **[Routing](#)**
- **[Data Fetching]()**
- **Rendering**
- **Caching**
- **Optimizing**
- **Transpilation**
- **API**

## **Routing**

## **Routing Fundamentals**

![Fundamentals](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568302/nextjs-docs/darkmode/terminology-component-tree.png)

- Tree: A convention for visualizing a hierarchical structure. For example, a component tree with parent and children components, a folder structure, etc.
- Subtree: Part of a tree, starting at a new root (first) and ending at the leaves (last).
- Root: The first node in a tree or subtree, such as a root layout.
- Leaf: Nodes in a subtree that have no children, such as the last segment in a URL path.

![Fundamentals](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568301/nextjs-docs/darkmode/terminology-url-anatomy.png)

- URL Segment: Part of the URL path delimited by slashes.
- URL Path: Part of the URL that comes after the domain (composed of segments).

### **The app Directory**

The new App Router works in a new directory named `app`. The `app` directory works alongside the `pages` directory to allow for incremental adoption. This allows you to opt some routes of your application into the new behavior while keeping other routes in the `pages` directory for previous behavior.

**In the `app` directory:**

- Folders are used to define routes. A route is a single path of nested folders, following the hierarchy from the root folder down to a final leaf folder that includes a page.js file.

- Files are used to create UI that is shown for the route segment. See special files.

### **Nested Routes**

To create a nested route, you can nest folders inside each other. For example, you can add a new `/dashboard/settings` route by nesting two new folders in the app directory.

- `/` (Root segment)
- `dashboard` (Segment)
- `settings` (Leaf segment)

### **File Conventions**

- **page.js:** Create the unique UI of a route and make the path publicly accessible.
- **route.js:** Create server-side API endpoints for a route.
- **layout.js:** Create shared UI for a segment and its children. A layout wraps a page or child segment.
- **template.js:** Similar to **layout.js**, except a new component instance is mounted on navigation. Use layouts unless you need this behavior.
- **loading.js**: Create loading UI for a segment and its children. **loading.js** wraps a page or child segment in a React Suspense Boundary, showing the loading UI while they load.
- **error.js:** Create error UI for a segment and its children. **error.js** wraps a page or child segment in a React Error Boundary, showing the error UI if an error is caught.
- **global-error.js:** Similar to **error.js**, but specifically for catching errors in the root **layout.js**.
- **not-found.js:** Create UI to show when the **notFound** function is thrown within a route segment or when a URL is not matched by any route.

### **Component Hierarchy**

- `layout.js`
- `template.js`
- `error.js` (React error boundary)
- `loading.js` (React suspense boundary)
- `not-found.js` (React error boundary)
- `page.js` or nested `layout.js`

![Component Hierarchy](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1675248777/nextjs-docs/darkmode/file-conventions-component-hierarchy.png)

### **Defining Routes**

Inside the `app` directory, folders are used to define routes.
Each folder represents a route segment that maps to a `URL` segment. To create a nested route, you can nest folders inside each other.

![Defining Routes](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1667553431/nextjs-docs/darkmode/route-segments-to-path-segments.png)

A special `page.js` file is used to make route segments publicly accessible.

![Defining Routes](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568301/nextjs-docs/darkmode/defining-routes-page.js.png)

### **Route Groups**

A route group can be created by wrapping a folder’s name in parenthesis: `(folderName)`

![Route Groups](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568349/nextjs-docs/darkmode/route-group-organisation.png)

This organize routes without affecting the URL, create a group to keep related routes together. The folders in parenthesis will be omitted from the URL (e.g. (marketing) or (shop)).

Even though routes inside (marketing) and (shop) share the same URL hierarchy, you can create a different layout for each group by adding a layout.js file inside their folders.

![Route Groups](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568349/nextjs-docs/darkmode/route-group-multiple-layouts.png)

### **Creating multiple root layouts**

To create multiple root layouts, remove the top-level `layout.js` file, and add a `layout.js` file inside each route groups. This is useful for partitioning an application into sections that have a completely different UI or experience. The `<html>` and `<body>` tags need to be added to each root layout.

![Creating multiple root layouts](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568348/nextjs-docs/darkmode/route-group-multiple-root-layouts.png)

_NOTE_

- The naming of route groups has no special significance other than for organization. They do not affect the URL path.

- Routes inside route groups should not resolve to the same URL path. For example, since route groups don't affect URL structure, `(marketing)/about/page.js` and (shop)/about/page.js would both resolve to `/about` and cause an error.

- Navigating across multiple root layouts will cause a full page load (as opposed to a client-side navigation). For example, navigating from `/cart` that uses `app/(shop)/layout.js` to `/blog` that uses `app/(marketing)/layout.js` will cause a full page load. This only applies to multiple root layouts.Dynamic Segments

### **Dynamic Segments**

Dynamic segments are supported in the new App Router. To create a dynamic segment, add square brackets around the segment name in the folder name. For example, `[productId]`.

Dynamic Segments are passed as the `params` prop to `layout`, `page`, `route`, and `generateMetadata` functions.

```javascript
// app/products/[productId]/page.js
export default function ProductPage({ params }) {
  return <h1>Product {params.productId}</h1>;
}
```

### **Nested Dynamic Segments**

To create a nested dynamic segment, nest folders inside each other. For example, `/products/[productId]/reviews/[reviewId]`.

```javascript
// app/products/[productId]/reviews/[reviewId]/page.js
export default function ProductReviewPage({ params }) {
  return (
    <h1>
      Review {params.reviewId} for Product {params.productId}
    </h1>
  );
}
```

### **Catch-all Segments**

To create a catch-all segment, add three dots (`...`) before the segment name in the folder name. For example, `app/shop/[...slug]/page.js` will match `/shop/clothes`, but also `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`

```javascript
// app/shop/[...slug]/page.js => /shop/a/b/c
{
  slug: ["a", "b", "c"];
}
```

### **Optional Catch-all Segments**

Catch-all Segments can be made optional by including the parameter in double square brackets: `[[...folderName]]`.For example, `app/shop/[[...slug]]/page.js` will also match `/shop`, in addition to `/shop/clothes`, `/shop/clothes/tops`, `/shop/clothes/tops/t-shirts`.

```javascript
// app/shop/[[...slug]]/page.js => /shop
{
  slug: [];
}

// app/shop/[[...slug]]/page.js => /shop/a/b/c
{
  slug: ["a", "b", "c"];
}
```

## **Pages and Layouts**

### **Pages**

A page is UI that is unique to a route. You can define pages by exporting a component from a `page.js` file. Use nested folders to define a route and a `page.js` file to make the route publicly accessible.

_NOTE_

- A page is always the leaf of the route subtree.
- Pages are Server Components by default but can be set to a Client Component.

### **Layouts**

A layout is UI that is shared between multiple pages. On navigation, layouts preserve state, remain interactive, and do not re-render. Layouts can also be nested.

You can define a layout by `default` exporting a React component from a `layout.js` file. The component should accept a `children` prop that will be populated with a child layout (if it exists) or a child page during rendering.

```javascript
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode,
}) {
  return (
    <section>
      <nav></nav>
      {children}
    </section>
  );
}
```

_NOTE_

- The top-most layout is called the Root Layout. This required layout is shared across all pages in an application. Root layouts must contain `html` and `body` tags.
- Any route segment can optionally define its own Layout. These layouts will be shared across all pages in that segment.
- Layouts in a route are nested by default. Each parent layout wraps child layouts below it using the React `children` prop.
- You can use Route Groups to opt specific route segments in and out of shared layouts.
- Layouts are Server Components by default but can be set to a Client Component.
- Passing data between a parent layout and its children is not possible.

### **Root Layout (Required)**

The root layout is defined at the top level of the app directory and applies to all routes. This layout enables you to modify the initial HTML returned from the server.

```javascript
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode,
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

_NOTE_

- The `app` directory must include a root layout.
- The root layout must define `<html>` and `<body>` tags since Next.js does not automatically create them.

### **Nested Layouts**

Layouts defined inside a folder (e.g. `app/dashboard/layout.js`) apply to specific route segments (e.g. `acme.com/dashboard`) and render when those segments are active. By default, layouts in the file hierarchy are nested, which means they wrap child layouts via their `children` prop.

![Nested Layouts](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1666568352/nextjs-docs/darkmode/nested-layout.png)

If you were to combine the two layouts above, the root layout (`app/layout.js`) would wrap the dashboard layout (`app/dashboard/layout.js`), which would wrap route segments inside `app/dashboard/*`.

![Nested Layouts](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1671641894/nextjs-docs/darkmode/nested-layouts.png)

### **Linking and Navigating**

- `<Link>` <== `next/link`

  - href : string

- `useRouter` <== `next/router`
  - pathname : string
  - query : object
  - asPath : string
  - push : function
  - replace : function
  - reload : function
  - back : function
  - prefetch : function
  - beforePopState : function
  - events : object
  - isFallback : boolean

There are two ways to navigate between routes:

- `<Link>` component.
- `useRouter` hook.

### **`<Link>` Component**

To use `<Link>`, import it from `next/link`, and pass a `href` prop to the component:

```javascript
import Link from "next/link";

export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>;
}
```

### **Linking to Dynamic Segments**

```javascript
<Link href={`/products/${productId}`}>
  <a>Product {productId}</a>
</Link>
```

### `useRouter() Hook`

The `useRouter` hook allows you to programmatically change routes inside Client Components.

To use `useRouter`, import it from `next/navigation`, and call the hook inside your Client Component:

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

**How Navigation Works**

- A route transition is initiated using <Link> or calling router.push().
- The router updates the URL in the browser’s address bar.
- The router avoids unnecessary work by re-using segments that haven't changed (e.g. shared layouts) from the client-side cache. This is also referred to as partial rendering.
- If the conditions of soft navigation are met, the router fetches the new segment from the cache rather than the server. If not, the router performs a hard navigation and fetches the Server Component payload from the server.
- If created, loading UI is shown from the server while the payload is being fetched.
- The router uses the cached or fresh payload to render the new segments on the client.

### **Invalidating the Cache**

You can refresh a route using `router.refresh()`.

### **Prefetching**

Prefetching is a way to preload a route in the background before it's visited. The rendered result of prefetched routes is added to the router's client-side cache. This makes navigating to a prefetched route near-instant.

By default, routes are prefetched as they become visible in the viewport when using the `<Link>` component. This can happen when the page first loads or through scrolling. Routes can also be programmatically prefetched using the `prefetch` method of the `useRouter()` hook.

**Static and Dynamic Routes:**

- If the route is static, all the Server Component payloads for the route segments will be prefetched.
- If the route is dynamic, the payload from the first shared layout down until the first `loading.js` file is prefetched.

_NOTE_

- Prefetching is only enabled in production.
- Prefetching can be disabled by passing `prefetch={false}` to `<Link>`

**Hard Navigation** : On navigation, the cache is invalidated and the server refetches data and re-renders the changed segments.

**Soft Navigation** : On navigation, the cache for changed segments is reused (if it exists), and no new requests are made to the server for data..

- Navigating from `/dashboard/team-red/*` to `/dashboard/team-red/*` will be a soft navigation.

- Navigating from `/dashboard/team-red/*` to `/dashboard/team-blue/*` will be a hard navigation.

## **Loading UI**

Next.js 13 introduced a new file convention, `loading.js`, to help you create meaningful Loading UI with React Suspense. With this convention, you can show an instant loading state from the server while the content of a route segment loads, the new content is automatically swapped in once rendering is complete.

![Loading Ui](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1673552977/nextjs-docs/darkmode/loading-folder.png)

```javascript
// app/dashboard/loading.js
export default function Loading() {
  return <LoadingSkeleton />;
}
```

In the same folder, `loading.js` will be nested inside `layout.js`. It'll wrap the `page.js` file and any children below in a `<Suspense>` boundary.

## **Error Handling**

The `error.js` file convention allows you to gracefully handle runtime errors in nested routes.

- Automatically wrap a route segment and its nested children in a React Error Boundary.
- Create error UI tailored to specific segments using the file-system hierarchy to adjust granularity.
- Isolate errors to affected segments while keeping the rest of the app functional.
- Add functionality to attempt to recover from an error without a full page reload.

Create error UI by adding an error.js file inside a route segment and exporting a React component:

![Error Handling](https://assets.vercel.com/image/upload/f_auto,q_100,w_1600/v1673552977/nextjs-docs/darkmode/error-folder.png)

### Recovering From Errors

An error component can use the `reset()` function to prompt the user to attempt to recover from the error

```javascript
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error,
  reset: () => void,
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

## **Route Handlers**

Route Handlers allow you to create custom request handlers for a given route using the Web Request and Response APIs.

[Request](https://developer.mozilla.org/en-US/docs/Web/API/Request)
[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)

Route Handlers are defined in a `route.js|ts` file inside the app directory:

```javascript
// app/api/route.ts
export async function GET(request: Request) {}
export async function POST(request: Request) {}
export async function PUT(request: Request) {}
export async function PATCH(request: Request) {}
export async function DELETE(request: Request) {}
export async function HEAD(request: Request) {}
```

The following HTTP methods are supported: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, and `OPTIONS`. If an unsupported method is called, Next.js will return a `405 Method Not Allowed` response.

### **Extended `NextRequest` and `NextResponse` APIs**

In addition to supporting native Request and Response. Next.js extends them with `NextRequest` and `NextResponse` to provide convenient helpers for advanced use cases.

- ### **Static Route Handlers**

  Route Handlers are statically evaluated by default when using the `GET` method with the Response object. This means that the response is generated at build time and served from the cache at runtime.

  ```javascript
  import { NextResponse } from "next/server";

  export async function GET() {
    const res = await fetch("https://data.mongodb-api.com/...", {
      headers: {
        "Content-Type": "application/json",
        "API-Key": process.env.DATA_API_KEY,
      },
    });
    const data = await res.json();

    return NextResponse.json({ data });
  }
  ```

- ### **Dynamic Route Handlers**

  Route handlers are evaluated dynamically when:

  - Using the `Request` object with the `GET` method.
  - Using any of the other `HTTP` methods.
  - Using Dynamic Functions like `cookies` and `headers`.

  ```javascript
  // app/api/teams/[teamId]/route.ts
  import { NextResponse } from "next/server";

  export async function GET(
    request: Request,
    { params }: { params: { teamId: string } }
  ) {
    const { teamId } = params;
    const res = await fetch(`http://localhost:3001/teams/${teamId}`, {
      headers: {
        "Content-Type": "application/json",
      },
    });
    const data = await res.json();
    return NextResponse.json(data);
  }
  ```

  Similarly, the POST method will cause the Route Handler to be evaluated dynamically.

  ```javascript
  // app/api/teams/[teamId]/route.ts
  import { NextResponse } from "next/server";

  export async function POST(
    request: Request,
    { params }: { params: { teamId: string } }
  ) {
    const { teamId } = params;
    const res = await fetch(`http://localhost:3001/teams/${teamId}`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ ...request.body, teamId }),
    });
    const data = await res.json();
    return NextResponse.json(data);
  }
  ```

### **Revalidating Static Data**

You can revalidate static data fetches using the `next.revalidate` option:

```javascript
import { NextResponse } from "next/server";

export async function GET() {
  const res = await fetch("https://data.mongodb-api.com/...", {
    next: { revalidate: 60 }, // Revalidate every 60 seconds
  });
  const data = await res.json();

  return NextResponse.json(data);
}
```

### **Dynamic Functions**

- **Cookies**
  You can read cookies with `cookies` from `next/headers`. This server function can be called directly in a Route Handler, or nested inside of another function.

  This `cookies` instance is `read-only`. To set cookies, you need to return a new `Response` using the `Set-Cookie` header.

  ```javascript
  import { cookies } from "next/headers";

  export async function GET(request: Request) {
    const cookieStore = cookies();
    const token = cookieStore.get("token");

    return new Response("Hello, Next.js!", {
      status: 200,
      headers: { "Set-Cookie": `token=${token}` },
    });
  }
  ```

  Alternatively, you can use abstractions on top of the underlying Web APIs to read cookies (`NextRequest`):

  ```javascript
  import { type NextRequest } from "next/server";

  export async function GET(request: NextRequest) {
    const token = request.cookies.get("token");
  }
  ```

- **Headers**

  You can read headers with headers from next/headers. This server function can be called directly in a Route Handler, or nested inside of another function.

  This headers instance is read-only. To set headers, you need to return a new Response with new headers.

  ```javascript
  // app/api/route.ts
  import { headers } from "next/headers";

  export async function GET(request: Request) {
    const headersList = headers();
    const referer = headersList.get("referer");

    return new Response("Hello, Next.js!", {
      status: 200,
      headers: { referer: referer },
    });
  }
  ```

- **Redirects**

  ```javascript
  // app/api/route.ts
  import { redirect } from "next/navigation";
  export async function GET(request: Request) {
    redirect("https://nextjs.org/");
  }
  ```
