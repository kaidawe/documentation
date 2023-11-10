# get-organizations

This api call will return a list of organizations the logged in user is a part of.

## Request

There is no input for this function.

> ### Example request
>
>     GET /get-organizations
>     Headers:
>       auth-token: [IdToken]
> This request assumes that,  
>``IdToken`` = The IdToken received from logging into the AWS cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>       'userGroups': [organizations]
>     }
> ``organizations`` will be a list of each organizations name that is associated with your account.

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
    getGroups: builder.query({
      query: (body) => {
        const { IdToken } = body;
        return {
          url: "get-organizations",
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      },
      providesTags: ["Organization"]
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const response = getGroups({ IdToken });