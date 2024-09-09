# request-file-data

This api call will allow you to edit all user metadata related to a selected model.

## Request

The input expects 2 things in the body. You must specify a selectedUser and hash. It will also accept any number of the body parameters.

1. `selectedUser` - This is the user you want to access the api through, it can be a username or organization name
2. `hash` - This is the hash/UID of the model you want to get the data of.

> ### Example request
>
> >     PUT https://api.secur3d.ai/model-function/request-file-data?selectedUser=[activeUser]
> >     Headers:
> >       X-Api-Token: [ApiKey]
> >     Body:
> >     {
> >       "selectedUser": [activeUser],
> >       "hash": [identifier],
> >       "file_name": "string: name of the model",
> >       "creator": "string: name of the user who uploaded the model",
> >       "noAI": "boolean: model cant be used to train AI",
> >       "createdWithAI": "boolean: model was created with AI",
> >       "accreditations": "string: field for other people or groups that helped make the model",
> >       "tags": "string array: list of tags submit by the user",
> >       "distributions": "json array: list of distributors and related data",
> >     }
>
> This request assumes a few things,
>
> 1. `identifier` = The hash/UID of the model
> 2. `activeUser` = Either a username or organization name
> 3. `ApiKey` = This is the Api Key received from the Secur3D portal as a organization.  

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### `Success 200`
>
> A successful response with a response body like this
>
> ```json
> {
>   "message": "Success"
> }
> ```

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    modelData: builder.mutation({
      query: (model) => {
        const { ApiKey, hash, activeUser } = model;
        return {
          url: `request-file-data?selectedUser=${activeUser}`,
          method: "PUT",
          headers: {
            "X-Api-Key": ApiKey,
          },
          body: {
            "selectedUser": activeUser,
            "hash": hash,
            "file_name": "new name",
            "creator": "new creator",
            "noAI": false,
            "createdWithAI": true,
            "accreditations": "Secur3d.ai",
            "tags": [
              "tag1",
              "tag2",
              "tag3"
            ],
            "distributions": [
              {
                "source": "secur3d",
                "url": "https://secur3d.ai",
                "license": "MIT"
              }
            ],
          }
        };
      }
    })

    //Call Api Function
    const ApiKey = "YourApiKeyFromTheWebsite";
    const activeUser = "Secur3D_Organization";
    const identifier = "YourHashHere_abcdefghijklmnopqrstuvwxyz";
    const response = generateModel({ IdToken, activeUser, identifier });

## Sandbox Endpoints

This is an example of how to use our Sandbox API endpoint to test a PUT request and receive a dummy response. This request will return data formatted as mentioned above.

  ```
  PUT https://sandbox.secur3d.ai/sandbox/file-data?hash=[identifier]&selectedUser=[activeUser]
  Headers:
    X-Api-Key: [ApiKey]
  Body: {
    "hash": "exampleHash",
    "file_name": "example.glb",
    "model_creator": "exampleCreator",
    "distributions": [{"source": "onlinemarket", "url": "https://onlinemarket.com"}],
    "accreditations": "Example Accreditation",
    "customUID": "exampleUID",
    "customKVP": {"key1": "value1", "key2": "value2"},
    "selectedUser": "exampleUser"
  }
  ```
