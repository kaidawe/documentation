# invite-to-organization

This api call will remove a specified user from the specified organization.  

## Request

The input expects 2 things in the body, they are both required.  

> ### Example request
>
>     POST /invite-to-organization
>     Headers:
>       auth-token: [IdToken]
>     Body:
>     {
>       "username": "[encodeURIComponent(organization)]",
>       "selectedUser": "[userToRemove]"
>     }
> This request assumes a few things,
> 1. ``organization`` = The name of the organization you want to add the user too.  
> 2. ``userToRemove`` = The name of the user you want to add too the organization.  
> 3. ``IdToken`` = This is the IdToken received from logging into the AWS     cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response will contain the api key
>
>     {
>       'statusCode': 200,
>       'message': 'User was removed successfully'
>     }

### Error Messages

> #### ``Error 400``
> An unsuccessful response with a response body like this
>
>     [response]
> ``response`` will be the reason the request failed as a string.

> #### ``Error 500``
> An unsuccessful response that means there was an internal server error.  
> This is likely due to an incorrect request.

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    removeUserFromOrganization: builder.mutation({
      query: (body) => {
        const { IdToken, organization, userToRemove } = body;

        return {
          url: `remove-from-organization`,
          method: "POST",
          body: {
            username: encodeURIComponent(activeUser),
            selectedUser: userToRemove,
          },
          headers: {
            "auth-token": IdToken,
          }
        };
      },
      invalidatesTags: ["OrganizationUser"]
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const organization = "Secur3D Organization";
    const userToRemove = "Secur3D_User";
    const response = removeUserFromOrganization({ IdToken, organization, userToRemove });