# create-organization

This api call will create an organization with the specified name and description and assign the user who created it as the owner.

## Request

The input expects 2 things in the body, only ``name`` is required.  

> ### Example request
>
>     POST /create-organization
>     Headers:
>       auth-token: [IdToken]
>     Body:
>     {
>       "Name": "[name]",
>       "Description": "[description]"
>     }
> This request assumes a few things,
> 1. ``name`` = The name of the new organization
> 2. ``description`` = The description of the new organization
> 3. ``IdToken`` = This is the IdToken received from logging into the AWS    cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>       'statusCode': 200,
>       'message': 'Organization was created successfully'
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
    createGroup: builder.mutation({
      query: (body) => {
        const { IdToken, name, description } = body;
        return {
          url: `create-organization`,
          method: "POST",
          body: {
            Name: name,
            Description: description,
          },

          headers: {
            "auth-token": IdToken,
          },
        };
      },
      invalidatesTags: ["Organization"],
    }),

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const name = "Secur3D Organization";
    const description = "My First Organization";
    const response = createGroup({ IdToken, name, description });