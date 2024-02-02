# request-file-data

This api call will allow you to edit all user metadata related to a selected model.

## Request

The input expects 2 things in the body. You must specify a selectedUser and hash. It will also accept any number of the body parameters.  
1. ``selectedUser`` - This is the user you want to access the api through, it can be a username or organization name
2. ``hash`` - This is the hash/UID of the model you want to get the data of.

> ### Example request
>
>>     PUT https://api.secur3d.ai/model-function/request-file-data
>>     Headers:
>>       auth-token: [IdToken]
>>     Body:
>>     {
>>       "selectedUser": [activeUser],
>>       "hash": [identifier],
>>       "file_name": "string: name of the model",
>>       "creator": "string: name of the user who uploaded the model",
>>       "noAI": "boolean: model cant be used to train AI",
>>       "createdWithAI": "boolean: model was created with AI",
>>       "accreditations": "string: field for other people or groups that helped make the model",
>>       "tags": "string array: list of tags submit by the user",
>>       "distributions": "json array: list of distributors and related data",
>>     }
> 
> This request assumes a few things,
> 1. ``identifier`` = The hash/UID of the model
> 2. ``activeUser`` = Either a username or organization name
> 3. ``IdToken`` = This is the IdToken received from logging into the AWS cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>  ```json
>  {
>    "message": "Success"
>  }
>  ```

### Error Messages

> #### ``Error 400``
> An unsuccessful response with a response body like this
>
>     {
>       'statusCode': 400,
>       'body': 'Submit user is not a valid organization or username',
>     }
> This response means that the ``selectedUser`` you submit either isn't valid or isn't accessible from the currently logged in account.

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    modelData: builder.mutation({
      query: (model) => {
        const { IdToken, hash, activeUser } = model;
        return {
          url: `request-file-data`,
          method: "PUT",
          headers: {
            "auth-token": IdToken,
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
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const identifier = "YourHashHere_abcdefghijklmnopqrstuvwxyz";
    const response = generateModel({ IdToken, activeUser, identifier });