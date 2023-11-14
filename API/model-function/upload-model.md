# upload-model

This API call will upload a model in 2 parts. The first will reserve a spot for the file on our servers and create a database entry, the second part will upload the file to our servers.

## Request

The input expects 4 things in the body, all of them are required.  

> ### Example request
>
>>     // Request Presigned URL (PART ONE)
>>     POST /upload-model
>>     Headers:
>>       auth-token: [IdToken]
>>       Content-Type: application/json
>>     Body:
>>       {
>>         "object_key": "[file name]",
>>         "asset_id": "[assetId]",
>>         "shaHash": "[encodedHash]",
>>         "selectedUser": "[activeUser]"
>>       }
>
> This request assumes that,  
> 1. ``file name`` = The full file name including the file extension.  
> 2. ``assetID`` = The file name excluding the file extension.  
> 3. ``encodedHash`` = The sha256 hash of the whole file.  
> 4. ``activeUser`` = Either a username or organization name.  
> 5. ``IdToken`` = This is the IdToken received from logging into the AWS cognito backend with a user account.  
>
>>     // UPLOAD MODEL (PART TWO WITH PRESIGNED URL)
>>     POST [preSignedUrl]
>>     Headers:
>>       (form data headers)
>>     Body (form data):
>>       {
>>         "key": [values from response.url.fields],
>>         "file": [upload_file]
>>       }
>
> This request assumes that,  
> 1. ``preSignedUrl`` = The url received from the first request inside the response.  
> 2. ``values from response.url.fields`` = The fields from the part one response url fields.  
> 3. ``upload_file`` = The file you are uploading.  

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200`` From Request Presigned URL
> A successful response with a response body like this means the upload request was approved.
>
>     {
>       'url' : [pre_signed_post_url],
>       'file_hash' : [UID]
>     }
> ``pre_signed_post_url`` will be a JSON containing many things that will allow you to upload a model to our servers, one of which will be a url to make a request to.  
> ``UID`` will be the hash/UID of the model you are uploading.  

### Error Messages

> #### ``Error 400`` From Request Presigned URL
> An unsuccessful response with a response body like this
>
>     {
>       'message' : [message]
>     }
> ``message`` will be the reason the request failed.

> #### ``Error 500`` From Request Presigned URL
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