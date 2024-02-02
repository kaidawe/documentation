# get-account-comparisons

This api call will return a list of comparisons that the selectedUser has started

## Request

The input expects 3 things in the query string parameters. selectedUser is the only required field.  
1. ``selectedUser`` - This is the user you want to access the api through, it can be a username or organization name
2. ``limit`` - This is the amount of comparisons to request, this can range from ``1 - 50``, the default is 10.
3. ``page`` - This is the current page you want to view, it will range from ``1 - ceil(total_comparisons / limit)``.

> ### Example request
>
>       GET https://api.secur3d.ai/model-function/get-account-comparisons?selectedUser=[activeUser]&limit=[limit]&page=[page]
>       Headers:
>         auth-token: [IdToken]
> 
> This request assumes a few things,
> 1. ``activeUser`` = Either a username or organization name
> 2. ``IdToken`` = This is the IdToken received from logging into the AWS cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>       'all_comparisons': [results],
>       'total_pages': [pages]
>     }
> ``results`` will be a list of comparisons in json format, comparisons with a score of -1 are still in process.
> ``pages`` will be the upper limit of page i.e. ``ceil(total_comparisons / limit)``

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
    getAccountComparisons: builder.mutation({
      query: ({ IdToken, activeUser, limit, page }) => ({
        url: `get-account-comparisons?selectedUser=${activeUser}&limit=${limit}&page=${page}`,
        method: "GET",
        headers: {
          "auth-token": IdToken,
        }
      })
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const limit = 25;
    const page = 2;
    const response = getAccountComparisons({ IdToken, activeUser, limit, page });