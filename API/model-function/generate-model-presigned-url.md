# generate-model-presigned-url

This api call will return a url to access a specified model's raw input file

## Request

The input expects 2 of three things in the query string parameters. You must specify a selectedUser and one of the other two parameters.  
1. ``selectedUser`` - This is the user you want to access the api through, it can be a username or organization name
2. ``hash`` - This is the hash/UID of the model you want to access.
3. ``file_name`` - This is the name of the model you want to access. In the case of naming colisions it will return the most recently uploaded model.

> ### Example request
>
>       GET https://api.secur3d.ai/model-function/generate-model-presigned-url?[type]=[identifier]&selectedUser=[activeUser]
>       Headers:
>         auth-token: [IdToken]
> 
> This request assumes a few things,
> 1. ``type`` = ``hash`` or ``file_name``
> 2. ``identifier`` = The hash/UID or file name of the model depending on the ``type``
> 3. ``activeUser`` = Either a username or organization name
> 4. ``IdToken`` = This is the IdToken recieved from logging into the AWS      cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>       'statusCode': 200,
>       'presignedUrl': [url]
>     }
> ``url`` will be the url you can access the model's raw file from, it will expire after 3600 seconds.

### Error Messages

> #### ``Error 400``
> An unsuccessful response with a response body like this
>
>     {
>       'statusCode': 400,
>       'body': 'Submit user is not a valid organization or username',
>     }
> This response means that the ``selectedUser`` you submit either isnt valid or isnt accessable from the currently logged in account.

> #### ``Error 404``
> An unsuccessful response with a response body like this
>
>     {
>       'statusCode': 404,
>       'response': [error]
>     }
> ``error`` will be the error message of what caused the function to be unsuccessful

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    generateModel: builder.mutation({
      query: (model) => {
        const { IdToken, identifier, activeUser, type } = model;

        const url = `?${type}=${identifier}&selectedUser=${activeUser}`;

        return {
          url: `generate-model-presigned-url${url}`,
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      }
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const identifier = "jacket";
    const type = "file_name";
    const response = generateModel({ IdToken, activeUser, identifier, type });