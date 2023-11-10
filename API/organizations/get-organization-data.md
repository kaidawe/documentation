# get-organization-data

This api call will return all the settings the organization currently has

## Request

The input expects 1 thing in the query string parameters, It is required.  

> ### Example request
>
>     GET /get-organization-data?selectedUser=[encodeURIComponent(activeUser)]
>     Headers:
>       auth-token: [IdToken]
> This request assumes a few things,
> 1. ``activeUser`` = The name of the organization you want to access the api through.  
> 2. ``IdToken`` = This is the IdToken recieved from logging into the AWS     cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response will contain all editable settings for the specified organization
>
>     {
>       'webhookUrl': [webhookUrl]
>     }
> ``webhookUrl`` will be the webhook url that will receive any updates when certain processes finish, for example when a model finishes getting uploaded.  

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
    getOrganizationEditableData: builder.query({
      query: (body) => {
        const { IdToken, activeUser } = body;
        const url = `?selectedUser=${encodeURIComponent(activeUser)}`;
        return {
          url: `get-organization-data${url}`,
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      },
      providesTags: ["OrganizationSettings"],
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D Organization";
    const response = getOrganizationEditableData({ IdToken, activeUser });