# upload-model

This API call will upload a model in 2 parts. The first will reserve a spot for the file on our servers and create a database entry, the second part will upload the file to our servers. The difference between this and `model-function/upload-model` is that this uses an `ApiKey` instead of a `IdToken`.

## Request

The input expects 3 things in the body, `object_key`, `asset_id`, and `shaHash` are required. `selectedUser` also must be passed into the query string parameters and is the name of the organization. `ApiKey` must be passed into the headers via `X-Api-Key` and is obtained via our website.

All of the other parameters are optional.

> ### Example request
>
> The first request of upload-model gets a presigned url to upload the file too
>
> ```{r, tidy=FALSE, eval=FALSE, highlight=FALSE }
>  // Request Presigned URL (PART ONE)
>  POST https://api.secur3d.ai/model-function/upload-model?selectedUser=[orgName]
>  Headers:
>    X-Api-Key: [ApiKey]
>    Content-Type: application/json
>  Body:
>    {
>      "object_key": "[The full file name including the file extension]",
>      "asset_id": "[The file name excluding the file extension]",
>      "shaHash": "[The sha256 hash of the whole file base64 encoded]",
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

## File Limitations

Currently this endpoint only supports files meeting certain conditions. They are as follows;

### File types

- `glb`  
- `fbx`  
- `obj`  

We also accept `zip` files with any combination of the above file types. Note that if there is a nested zip in your first zip file it will not be processed.  
Also if any filepath when uncompressed becomes larger than 4096 characters long it will not be able to get processed (the filepath uncompressed would be `/tmp/{filepath in zip}`)  

### File size

Any file uploaded must be at least `1500 bytes` and at most `1 GiB`

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

#### Request Presigned URL

> #### `Success 200`
>
> A successful response with a response body like this means the upload request was approved.
>
>     {
>       'url' : [pre_signed_post_url],
>       'file_hash' : [UID],
>       'secur3d_model_url': [url for model on Secur3D portal]
>     }
>
> `pre_signed_post_url` will be a JSON containing many things that will allow you to upload a model to our servers, one of which will be a url to make a request to.  
> `UID` will be the hash/UID of the model you are uploading.

#### Presigned URL Upload

> #### `Success 204`
> 
> This is returned when you have successfully uploaded your file.

### Error Messages

#### Request Presigned URL

> #### `Error 400`
>
> An unsuccessful response with a response body like this
>
>     {
>       'message' : [message]
>     }
>
> `message` will be the reason the request failed.

#### Presigned URL Upload

> #### `Error 400`
>
> The request was malformed or contained invalid parameters. Common causes include an incorrect signature, missing required fields, or an improperly formatted request. 
>
> One cause that is very common is the following `Value for x-amz-checksum-sha256 header is invalid.`, if you have this check that your shaHash in Request Presigned URL is a base64 encoded sha256 hash of the file you are uploading.

> #### `Error 403`
>
> Access to the specified resource is denied. This can happen if the presigned URL has expired, the credentials used to sign the URL are invalid, or the permissions do not allow the requested operation.

> #### `Error 404`
>
> The specified bucket or object key does not exist. This may occur if the bucket name or object key in the URL is incorrect.

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Request Presigned URL API function
    requestPresignedURL: builder.mutation({
      query: (model) => {
        const { ApiKey, assetId, file, activeUser, encodedHash } = model;

        const url = `upload-model`;
        return {
          url: `/${url}?selectedUser={activeUser}`,
          method: "POST",
          body: {
            object_key: file.name,
            asset_id: file.name.substring(0, file.name.lastIndexOf(".")),
            shaHash: encodedHash,
          },
          headers: {
            "X-Api-Key": ApiKey,
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
    const ApiKey = "YourApiKeyFromSecur3DWebsite";
    const file = [FILE_TO_UPLOAD]; //The file itself, not path
    const activeUser = "Secur3D_User";

    //Upload selected file
    const fileUpload = async () => {
        const encodedHash = useEncodedHash(file);
        const response = await requestPresignedURL({ ApiKey, assetId, file, activeUser, encodedHash });
        if (response) {
            uploadModel({ file, ...response });
        }
    };

## Sandbox Endpoints

This is an example of how to use our Sandbox API endpoint to test a POST request and receive a dummy response. A successful request will return data formatted in the same way as above.

    POST https://sandbox.secur3d.ai/sandbox/upload-model?selectedUser=[selectedUser]
    Headers:
      x-api-key: [ApiKey]
      Content-Type: application/json
    Body:
      {
        "object_key": "example.glb",
        "asset_id": "example",
        "shaHash": "shaHash12",
        "selectedUser": [selectedUser]
      }
