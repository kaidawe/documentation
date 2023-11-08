# remove-model

This api call will delete the specified model and anything related to it, e.g. comparisons, thumbnails, etc.

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
>      "hash": "[hash]"
>     }
> 
> This request assumes a few things,
> 1. ``activeUser`` = Either a username or organization name
> 2. ``hash`` = The hash/UID of the model you want to delete.
> 3. ``IdToken`` = This is the IdToken recieved from logging into the AWS    cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>      'message': 'Model has been deleted'
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
    deleteModel: builder.mutation({
      query: (model) => {
        const { userName, hash, IdToken } = model;
        return {
          url: `remove-model`,
          method: "POST",
          body: {
            selectedUser: userName,
            hash,
          },
          headers: {
            "Content-Type": "application/json",
            "auth-token": IdToken,
          },
        };
      },
      invalidatesTags: ["Models"],
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const hash = "ModelToDeleteHash";
    const response = deleteModel({ activeUser, hash, IdToken });