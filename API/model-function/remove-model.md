# remove-model

This api call will delete the specified models and anything related to it, e.g. comparisons, thumbnails, etc.

## Request

The input expects 2 things in the body, both are required.  

> ### Example request
>
>     POST /remove-model
>     Headers:
>      Content-Type: application/json
>      auth-token: [IdToken]
>     Body:
>     {
>      "selectedUser": "[activeUser]",
>      "modelList": "[hashes]"
>     }
> 
> This request assumes a few things,
> 1. ``activeUser`` = Either a username or organization name
> 2. ``hashes`` = A array of json objects that contain a hash value referring to a model you want to delete.
> 3. ``IdToken`` = This is the IdToken received from logging into the AWS    cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>      'message': 'Model(s) has been deleted'
>     }

### Error Messages

> #### ``Error 400``
> An unsuccessful response with a response body like this
>
>     {
>       'message' : [message]
>     }
> ``message`` will be the reason the request failed.

> #### ``Error 500``
> An unsuccessful response that means there was an internal server error.  
> This is likely due to an incorrect request.

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    deleteModels: builder.mutation({
      query: (model) => {
        const { userName, hashes, IdToken } = model;
        return {
          url: `remove-model`,
          method: "POST",
          body: {
            selectedUser: userName,
            modelList: hashes,
          },
          headers: {
            "Content-Type": "application/json",
            "auth-token": IdToken,
          },
        };
      },
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const hashes = [
      {
        "hash": "YourHashHere_abcdefghijklmnopqrstuvwxyz"
      },
      {
        "hash": "YourHashHere_zyxwvutsrqponmlkjihgfedcba"
      }
    ];
    const response = deleteModels({ activeUser, hashes, IdToken });