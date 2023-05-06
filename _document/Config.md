# NEXT 13 : Config

- `basePath` : basePath allows you to set a path prefix for the application.

  ```javascript
  module.exports = {
    basePath: "/",
  };
  ```

- `env` : env allows you to expose environment variables to the browser.

  ```javascript
  module.exports = {
    env: {
      customKey: "my-value",
    },
  };
  ```

  Now you can access `process.env.customKey` in your code.

- `distDir` : distDir allows you to configure the output (build) directory.

  ```javascript
  module.exports = {
    distDir: "build",
  };
  ```

- `reactStrictMode` : reactStrictMode allows you to enable React strict mode for the application.

  ```javascript
  module.exports = {
    reactStrictMode: true,
  };
  ```

- `eslint` : eslint allows you to configure ESLint for the application.

  ```javascript
  module.exports = {
    eslint: {
      // Warning: Dangerously allow production builds to successfully complete even if
      // your project has ESLint errors.
      ignoreDuringBuilds: true,
    },
  };
  ```

- `images` : images allows you to configure Image Optimization for the application.

  ```javascript
  module.exports = {
    images: {
      domains: ["example.com"],
    },
  };
  ```
