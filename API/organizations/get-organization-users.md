# get-organization-users

This api call will return a list of users that are in the specified organization.  

## Request

The input expects 3 things in the query string parameters. selectedUser is the only required field.  
1. ``selectedUser`` - This is the name of the organization you want to get the api key of.  
2. ``limit`` - This is the amount of users to request, this can range from ``1 - 50``, the default is 10.
3. ``page`` - This is the current page you want to view, it will range from ``1 - ceil(total_users / limit)``.

> ### Example request
>
>     GET /get-organization-users?selectedUser=[encodeURIComponent(activeUser)]&limit=[limit]&page=[page]
>     Headers:
>       auth-token: [IdToken]
> This request assumes a few things,
> 1. ``activeUser`` = The name of the organization you want to get the api key of.  
> 2. ``IdToken`` = This is the IdToken received from logging into the AWS     cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response will contain the api key
>
>     {
>       'users': [results],
>       'total_pages': [pages]
>     }
> ``results`` will be a list of users names in JSON array format.  
> ``pages`` will be the upper limit of page i.e. ``ceil(total_users / limit)``

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
    getOrganizationUsers: builder.query({
      query: (body) => {
        const { IdToken, activeUser, limit, page } = body;
        const url = `?selectedUser=${encodeURIComponent(activeUser)}&limit={limit}&page={page}`;
        return {
          url: `get-organization-users${url}`,
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      },
      providesTags: ["OrganizationUser"]
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D Organization";
    const limit = 25;
    const page = 2;
    const response = getOrganizationUsers({ IdToken, activeUser, limit, page });