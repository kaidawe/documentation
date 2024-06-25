# upload-model

This API call will upload a model in 2 parts. The first will reserve a spot for the file on our servers and create a database entry, the second part will upload the file to our servers.

## Request

The input expects `object_key`, `asset_id`, `shaHash`, and `selectedUser` in the body, all of them are required.

The rest of the parameters in the body are optional.

> ### Example request
>
> The first request of upload-model gets a presigned url to upload the file too
>
> ```{r, tidy=FALSE, eval=FALSE, highlight=FALSE }
>  // Request Presigned URL (PART ONE)
>  POST https://api.secur3d.ai/model-function/upload-model
>  Headers:
>    auth-token: [The IdToken received from logging into the AWS cognito backend with a user account]
>    Content-Type: application/json
>  Body:
>    {
>      "object_key": "[The full file name including the file extension]",
>      "asset_id": "[The file name excluding the file extension]",
>      "shaHash": "[The sha256 hash of the whole file]",
>      "selectedUser": "[Either a username or organization name]"
>
>      //Optional Parameters
>      "model_creator": "[string: name of the user who uploaded the model]"
>      "description": "[string: description of model]"
>      "noAI": "[boolean: model cant be used to train AI]"
>      "createdWithAI": "[boolean: model was created with AI]"
>      "accreditations": "[string: field for other people or groups that helped make the model]"
>      "categories": "[json array: list of user submit labels and their related data]"
>      "tags": "[string array: list of tags submit by the user]"
>      "customUID": "[string: a custom uid that the user can set]"
>      "customKVP": "[json array: a list of key-value pairs set by both the user and system]"
>    }
> ```
>
> The second part of the upload-model request uses the presigned url received from the previous request.
>
> ```
>   // UPLOAD MODEL (PART TWO WITH PRESIGNED URL)
>   POST [The url received from the first request inside the response]
>   Headers:
>     (form data headers)
>   Body (form data):
>     {
>       "key": [The fields from the part one response url fields],
>       "file": [The file you are uploading]
>     }
> ```

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### `Success 200` From Request Presigned URL
>
> A successful response with a response body like this means the upload request was approved.
>
>     {
>       'url' : [pre_signed_post_url],
>       'file_hash' : [UID]
>     }
>
> `pre_signed_post_url` will be a JSON containing many things that will allow you to upload a model to our servers, one of which will be a url to make a request to.  
> `UID` will be the hash/UID of the model you are uploading.

### Error Messages

> #### `Error 400` From Request Presigned URL
>
> An unsuccessful response with a response body like this
>
>     {
>       'message' : [message]
>     }
>
> `message` will be the reason the request failed.

> #### `Error 500` From Request Presigned URL
>
> An unsuccessful response that means there was an internal server error.  
> This is likely due to an incorrect request.

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Request Presigned URL API function
    requestPresignedURL: builder.mutation({
      query: (model) => {
        const { IdToken, assetId, file, activeUser, encodedHash } = model;

        const url = `upload-model`;
        return {
          url: `/${url}`,
          method: "POST",
          body: {
            object_key: file.name,
            asset_id: file.name.substring(0, file.name.lastIndexOf(".")),
            shaHash: encodedHash,
            selectedUser: activeUser,
          },
          headers: {
            "auth-token": IdToken,
            "Content-Type": "application/json",
          }
        };
      },
      invalidatesTags: ["Models"]
    })

    //Declare Upload Model API function
    uploadModel: builder.mutation({
      query: (response) => {
        const preSignedUrl = response.url.url;

        if (!preSignedUrl) {
          throw new Error("pre-signed url request failed, is null");
        }

        const formData = new FormData();

        Object.entries(response.url.fields).forEach(([key, value]: any) => {
          formData.append(key, value);
        });

        formData.append("file", response.file);

        return {
          url: preSignedUrl,
          method: "POST",
          body: formData,
        };
      }
    })

    //Declare required variables
    const IdToken = "YourIdTokenFromCognito";
    const file = [FILE_TO_UPLOAD]; //The actual file, not path
    const activeUser = "Secur3D_User";

    //Upload selected file
    const fileUpload = async () => {
        const encodedHash = useEncodedHash(file);
        const response = await requestPresignedURL({ IdToken, assetId, file, activeUser, encodedHash });
        if (response) {
            uploadModel({ file, ...response });
        }
    };

## Sandbox Endpoints

This is an example of how to use our Sandbox API endpoint to test a POST request and receive a dummy response for testing. A successful request will return data formatted in the same way as above.

    POST https://o1s8pnc024.execute-api.us-west-2.amazonaws.com/Sandbox/sandbox/upload-model?selectedUser=[selectedUser]
    Headers:
      auth-token: [IdToken]
      Content-Type: application/json
    Body:
      {
        "object_key": "example.glb",
        "asset_id": "example",
        "shaHash": "shaHash12",
        "selectedUser": [selectedUser]
      }
