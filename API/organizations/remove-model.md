# remove-model

This api call will delete the specified model and anything related to it, e.g. comparisons, thumbnails, etc. The difference between this and ``model-function/remove-model`` is that this uses an ``ApiKey`` instead of a ``IdToken``.

## Request

The input expects 1 thing in the body and 1 thing in the query string parameters, both are required.  

> ### Example request
>
>     POST /remove-model?selectedUser=[orgName]
>     Headers:
>      Content-Type: application/json
>      X-Api-Key: [ApiKey]
>     Body:
>     {
>      "hash": "[hash]"
>     }
> 
> This request assumes a few things,
> 1. ``orgName`` = The organization name that relates to the ``ApiKey`` you received
> 2. ``hash`` = The hash/UID of the model you want to delete.
> 3. ``ApiKey`` = This is the api key received from requesting your key through the ``get-organization-key`` api call.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>      'message': 'Model has been deleted'
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
    deleteModel: builder.mutation({
      query: (model) => {
        const { orgName, hash, ApiKey } = model;

        const url = `?$selectedUser=${orgName}`;

        return {
          url: `remove-model${url}`,
          method: "POST",
          body: {
            hash,
          },
          headers: {
            "Content-Type": "application/json",
            "X-Api-Key": ApiKey,
          },
        };
      },
      invalidatesTags: ["Models"],
    })

    //Call Api Function
    const ApiToken = "YourApiKeyFromOrganizationRequest";
    const orgName = "Secur3D Organization";
    const hash = "ModelToDeleteHash";
    const response = deleteModel({ orgName, hash, ApiKey });