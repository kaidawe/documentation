# request-file-data

This api call will return all data related to a selected model.

## Request

The input expects 2 things in the query string parameters. You must specify a selectedUser and hash.

1. `selectedUser` - This is the user you want to access the api through, it can be a username or organization name
   
3. `hash` - This is the hash/UID of the model you want to get the data of.

> ### Example request
>
>       GET https://api.secur3d.ai/model-function/request-file-data?hash=[identifier]&selectedUser=[activeUser]
>       Headers:
>         X-Api-Token: [ApiKey]
>
> This request assumes a few things,
>
> 1. `identifier` = The hash/UID or file name of the model depending on the `type`

> 2. `activeUser` = Organization name

> 3. `ApiKey` = This is the Api Key received from the Secur3D portal as a organization.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### `Success 200`
>
> A successful response with a response body like this
>
> ```json
>  {
>    "statusCode": 200,
>    "file_name": "string: name of the model",
>    "file_hash": "string: hash/UID of the model",
>    "upload_timestamp": "int: unix timestamp of when the model was uploaded",
>    "creator": "string: name of the user who uploaded the model", -
>    "file_owner": "string: user or organization that the model belongs to",
>    "extract_progress": "boolean: model can be used for comparisons",
>    "thumbnail_url": "string: url to the thumbnail of the model",
>    "texture_count": "int: number of scannable textures that a model has",
>    "description": "string: provided description of model", -
>    "valid": "boolean: model is valid",
>    "noAI": "boolean: model cant be used to train AI", -
>    "createdWithAI": "boolean: model was created with AI", -
>    "accreditations": "string: field for other people or groups that helped make the model", -
>    "labels": {
>       "labels": "json array: list of labels and their related data",
>       "moderation_labels": "json array: list of explicit labels and their related data",
>       "brand_labels": "json array: list of brand labels and their related data",
>       "categories": "json array: list of user submit labels and their related data" -
>    },
>    "tags": "string array: list of tags submit by the user", -
>    "distributions": "json array: list of distributors and related data", -
>    "texture_matches": "array of json arrays: there is one json array for each texture in the model, these will contain json's with related data to the match",
>    "total_vertices": "int: total vertices in the model",
>    "total_triangles": "int: total triangles in the model",
>    "customUID": "string: a custom uid that the user can set", -
>    "customKVP": "json array: a list of key-value pairs set by both the user and system" -
>  }
> ```

### Error Messages

> #### `Error 404`
>
> An unsuccessful response with a response body like this
>
>     {
>       "statusCode": 404,
>       "response": [error]
>     }
>
> `error` will be the error message of what caused the function to be unsuccessful

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    modelData: builder.mutation({
      query: (model) => {
        const { ApiKey, hash, activeUser } = model;
        const url = `?$hash=${hash}&selectedUser=${activeUser}`;
        return {
          url: `request-file-data${url}`,
          method: "GET",
          headers: {
            "X-Api-Token": ApiKey,
          }
        };
      }
    })

    //Call Api Function
    const ApiKey = "YourApiKeyFromTheWebsite";
    const activeUser = "Secur3D_Organization";
    const identifier = "YourModelHashHere";
    const response = generateModel({ ApiKey, activeUser, identifier });

## Sandbox Endpoint

This is an example of how to use our Sandbox API endpoint to test a GET request and receive a dummy response. This request will return data formatted as mentioned above.

  ```
  GET https://sandbox.secur3d.ai/sandbox/file-data?hash=[identifier]&selectedUser=[activeUser]
    Headers:
      X-Api-Key: [ApiKey]
  ```
