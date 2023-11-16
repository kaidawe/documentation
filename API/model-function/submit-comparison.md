# submit-comparison

This api call will start a comparison or return the comparison results of 2 models if they have already been compared.

## Request

The input expects 3 things in the body, all of them are required.  

> ### Example request
>
>     POST /submit-comparison
>     Headers:
>       Content-Type: application/json
>       auth-token: [IdToken]
>     Body:
>       {
>         "hash1": "[encodeURIComponent(hash1)]",
>         "hash2": "[encodeURIComponent(hash2)]",
>         "selectedUser": [activeUser]
>       }
>
> This request assumes that,  
> 1. ``encodeURIComponent(hash1)`` and ``encodeURIComponent(hash2)`` = The hashes of each of the 2 models you want to compare, they must be URI encoded.
> 2. ``activeUser`` = Either a username or organization name
> 3. ``IdToken`` = This is the IdToken received from logging into the AWS      cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this means the comparison results already exist.
>
>     {
>       'statusCode': 200,
>       'results': [file_content]
>     }
> ``fileContent`` will be a JSON, the JSON will be the results of the comparison between the 2 models.  

> #### ``Success 201``
> A successful response with a response body like this means the comparison has started
>
>     {
>       'statusCode' : 201,
>       'response' : [response]
>     }
> ``response`` will be a success message that the comparison has started  

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
    modelComparison: builder.mutation({
      query: (model) => {
        const { IdToken, hash1, hash2 } = model;

        return {
          url: `submit-comparison`,
          method: "POST",
          body: {
            hash1: encodeURIComponent(hash1),
            hash2: encodeURIComponent(hash2),
          },
          headers: {
            "Content-Type": "application/json",
            "auth-token": IdToken,
          }
        };
      }
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const hash1 = "Hash/UidOfFirstModel";
    const hash2 = "Hash/UidOfSecondModel";
    const response = modelComparison({ IdToken, hash1, hash2 });