# update-organization-settings

This api call will update the settings of the specified organization.  

## Request

The input expects 2 things in the body, they are both required.  

> ### Example request
>
>     
>     POST /update-organization-settings
>     Headers:
>       auth-token: [IdToken]
>     Body:
>     {
>       "selectedUser": "[activeUser]",
>       "webhookUrl": "[webhookUrl]"
>     }
> This request assumes a few things,
> 1. ``activeUser`` = The name of the organization you want to add the user too.  
> 2. ``webhookUrl`` = The url of your webhook that will receive updates related to the organizations models.  
> 3. ``IdToken`` = This is the IdToken received from logging into the AWS     cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response will contain the api key
>
>     {
>       'statusCode': 200,
>       'message': 'Organization was updated successfully'
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
    updateOrganizationSettings: builder.mutation({
      query: (body) => {
        const { IdToken, activeUser, webhookUrl } = body;

        return {
          url: `update-organization-settings`,
          method: "POST",
          body: {
            selectedUser: activeUser,
            webhookUrl,
          },
          headers: {
            "auth-token": IdToken,
          }
        };
      },
      invalidatesTags: ["OrganizationSettings"]
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D Organization";
    const webhookUrl = "UrlOfYourWebhook";
    const response = updateOrganizationSettings({ IdToken, activeUser, webhookUrl });