# get-model-comparisons

This api call will return a list of comparisons that the selected model has been in.

## Request

The input expects 4 things in the query string parameters. selectedUser and hash are required fields.  
1. ``selectedUser`` - This is the user you want to access the api through, it can be a username or organization name
2. ``hash`` - This is the hash/UID of the model you want to access.
3. ``limit`` - This is the amount of comparisons to request, this can range from ``1 - 50``, the default is 10.
4. ``page`` - This is the current page you want to view, it will range from ``1 - ceil(total_comparisons / limit)``.

> ### Example request
>
>       GET https://api.secur3d.ai/model-function/get-model-comparisons?selectedUser=[activeUser]&hash=[hash]&limit=[limit]&page=[page]
>       Headers:
>        auth-token: [IdToken]
> 
> This request assumes a few things,
> 1. ``activeUser`` = Either a username or organization name
> 2. ``IdToken`` = This is the IdToken recieved from logging into the AWS    cognito backend with a user account.

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
> ``results`` will be a list of comparisons in json format.
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
    getModelComparisons: builder.mutation({
      query: ({ IdToken, activeUser, hash, limit, page }) => ({
        url: `get-account-comparisons?selectedUser=${activeUser}&hash={hash}&limit=${limit}&page=${page}`,
        method: "GET",
        headers: {
          "auth-token": IdToken,
        }
      })
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const hash = "YourModelsHash/UID";
    const limit = 25;
    const page = 2;
    const response = getModelComparisons({ IdToken, activeUser, hash, limit, page });