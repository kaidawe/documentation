# request-results

This api call will return the comparison results of 2 models.

## Request

The input expects 2 things in the query string parameters. You must specify both hash1 and hash2, they dont have to be in any specific order.  
1. ``hash1`` - This is the hash/UID of the first model in the comparison.
2. ``hash2`` - This is the hash/UID of the second model in the comparison.

> ### Example request
>
>     GET /request-results?hash1=[hash1]&hash2=[hash2]
>     Headers:
>       auth-token: [IdToken]
>
> This request assumes that,  
> ``IdToken`` = This is the IdToken recieved from logging into the AWS      cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>       'statusCode': 200,
>       'fileContent': [file_content]
>     }
> ``fileContent`` will be a base64(string of bytes) that represent a json file, the decoded json will be the results of the comparison between the 2 models.  

### Error Messages

> #### ``Error 404``
> An unsuccessful response with a response body like this
>
>     {
>       'statusCode': 404, 
>       'error': [error]
>     }
> ``error`` will be the error message of what caused the function to be unsuccessful

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    modelComparisonResults: builder.mutation({
      query: (model) => {
        const { IdToken, hash1, hash2 } = model;
        const url = `?hash1=${hash1}&hash2=${hash2}`;
        return {
          url: `request-results${url}`,
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      }
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const hash1 = "Hash/UidOfFirstModel";
    const hash2 = "Hash/UidOfSecondModel";
    const response = modelComparisonResults({ IdToken, hash1, hash2 });