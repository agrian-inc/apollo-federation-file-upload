# Archived 7/18/23

We are moving away from supporting file uploads in GraphQL requests, based on our experiences and [best practices recommended by Apollo](https://www.apollographql.com/blog/backend/file-uploads/file-upload-best-practices/).

# Apollo Federation file upload

This is a minimally changed fork of [`apollo-federation-file-upload`](https://github.com/profusion/apollo-federation-file-upload) configured to use [`graphql-upload-minimal`](https://github.com/flash-oss/graphql-upload-minimal) instead of [`graphql-upload`](https://github.com/jaydenseric/graphql-upload).

This library makes it easier to support file uploads to your federated
micro-services. It uses the [Apollo](https://www.apollographql.com/docs/apollo-server/data/file-uploads/) server's solution.
It works by simply redirecting the file uploaded stream to the micro-service.
This package does not use third-party services to send the package to your
micro-services.

## Apollo Federation V2 Support

Please, use version 4.0.0+. For Federation V1 use 3.0.0 or earlier.

## Using HTTP Transfer-Encoding: chunked

By default, the `FileUploadDataSource` uses chunked transfers; we
advise that you do not change this setup. However, for some reason
you can't support this kind of transfer, one can provide the `useChunkedTransfer`
option to the `FileUploadDataSource` constructor as `false` to not
use chunked transfer (See the example below on setting this property).
Be advised once again that this can lead to DDOS attacks.

## Example

On your Gateway, you must add the `FileUploadDataSource` in order
to the micro-service be able to receive the uploaded file(s).


```javascript
import { ApolloServer } from 'apollo-server';
import { ApolloGateway } from '@apollo/gateway';
import FileUploadDataSource from '@profusion/apollo-federation-upload';

const runServer = async () => {
  const server = new ApolloServer({
    gateway: new ApolloGateway({
      // Add this line in order to support file uploads.
      buildService: ({ url }) => new FileUploadDataSource({ url, useChunkedTransfer: true }),
      serviceList: [
        /* The services ... */
      ],
    })
  });

  const { url } = await server.listen();

  console.log(`🚀  Server ready at ${url}`);
};

runServer().catch(error => {
  console.error('💥  Failed to start server:', error);
  process.exit(1);
});
```
