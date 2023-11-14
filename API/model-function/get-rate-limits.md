# get-rate-limits

This api call will return the current limits for your account as well as the current usage.

## Request

The input expects 1 thing in the query string parameters, it is a required field.   
1. ``selectedUser`` - This is the user you want to access the api through, it can be a username or organization name

> ### Example request
>
>       GET https://api.secur3d.ai/model-function/get-rate-limits?selectedUser=[activeUser]
>       Headers:
>        auth-token: [IdToken]
> 
> This request assumes a few things,
> 1. ``activeUser`` = Either a username or organization name
> 2. ``IdToken`` = This is the IdToken received from logging into the AWS    cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>      'statusCode': 200,
>      'rate_data': [rates]
>     }
> ``rates`` will be a list of each limit type with current usage and account limit.

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
    getUsageLimits: builder.mutation({
      query: ({ IdToken, activeUser }) => ({
        url: `get-rate-limits?selectedUser=${activeUser}`,
        method: "GET",
        headers: {
          "auth-token": IdToken,
        },
      })
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const response = getUsageLimits({ IdToken, activeUser });