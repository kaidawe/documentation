# get-organization-key

This api call will return the api key of the organization, this can be used to access certain api functions such as /organizations/upload-model/

## Request

The input expects 1 thing in the query string parameters, It is required.  

> ### Example request
>
>     GET /get-organization-key?selectedUser=[encodeURIComponent(activeUser)]
>     Headers:
>       auth-token: [IdToken]
> This request assumes a few things,
> 1. ``activeUser`` = The name of the organization you want to get the api key of.  
> 2. ``IdToken`` = This is the IdToken recieved from logging into the AWS     cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response will contain the api key
>
>     {
>       'apiKey': [key]
>     }
> ``key`` will be the api key that you can use in other requests.  

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
    getOrganizationApiKey: builder.query({
      query: (body) => {
        const { IdToken, activeUser } = body;
        const url = `?selectedUser=${encodeURIComponent(activeUser)}`;
        return {
          url: `get-organization-key${url}`,
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      },
      providesTags: ["ApiKey"],
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D Organization";
    const response = getOrganizationApiKey({ IdToken, activeUser });