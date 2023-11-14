# request-file-data

This api call will return all data related to a selected model.

## Request

The input expects 2 of three things in the query string parameters. You must specify a selectedUser and one of the other two parameters.  
1. ``selectedUser`` - This is the user you want to access the api through, it can be a username or organization name
2. ``hash`` - This is the hash/UID of the model you want to get the data of.
3. ``file_name`` - This is the name of the model you want to get the data of. In the case of naming collisions it will return the most recently uploaded model.

> ### Example request
>
>       GET https://api.secur3d.ai/model-function/request-file-data?[type]=[identifier]&selectedUser=[activeUser]
>       Headers:
>         auth-token: [IdToken]
> 
> This request assumes a few things,
> 1. ``type`` = ``hash`` or ``file_name``
> 2. ``identifier`` = The hash/UID or file name of the model depending on the ``type``
> 3. ``activeUser`` = Either a username or organization name
> 4. ``IdToken`` = This is the IdToken received from logging into the AWS      cognito backend with a user account.

## Response

The response will have a status code which will represent what the response was. This is what each code represents.

### Success Messages

> #### ``Success 200``
> A successful response with a response body like this
>
>     {
>       'statusCode': 200,
>       'file_name': [file_name],
>       'file_hash': [hash],
>       'upload_timestamp': [timestamp],
>       'creator': [creator],
>       'file_owner': [file_owner],
>       'extract_progress': [extract],
>       'thumbnail_url': [thumbnail_url],
>       'tags': [tags]
>     }
> ``file_name`` will be the name of the model.  
> ``hash`` will be the hash/UID of the model.  
> ``upload_timestamp`` will be the unix timestamp of when the model was uploaded.  
> ``creator`` will be the name of the user who uploaded the model.  
> ``file_owner`` will be the user or organization that the model belongs to.  
> ``extract`` will be a boolean value representing if the model can be used for comparisons.  
> ``thumbnail_url`` will be a url to the thumbnail of the model.  
> ``tags`` will be a list of tags related to the model, this will be a JSON with each tag being separated into a category.  

### Error Messages

> #### ``Error 400``
> An unsuccessful response with a response body like this
>
>     {
>       'statusCode': 400,
>       'body': 'Submit user is not a valid organization or username',
>     }
> This response means that the ``selectedUser`` you submit either isn't valid or isn't accessible from the currently logged in account.

> #### ``Error 404``
> An unsuccessful response with a response body like this
>
>     {
>       'statusCode': 404,
>       'response': [error]
>     }
> ``error`` will be the error message of what caused the function to be unsuccessful

## Example Usage

This is an example of how to make a request using the Redux Toolkit in Next.js

    //Declare Api function
    modelData: builder.mutation({
      query: (model) => {
        const { IdToken, hash, activeUser, type } = model;
        const url = `?${type}=${hash}&selectedUser=${activeUser}`;
        return {
          url: `request-file-data${url}`,
          method: "GET",
          headers: {
            "auth-token": IdToken,
          }
        };
      }
    })

    //Call Api Function
    const IdToken = "YourIdTokenFromCognito";
    const activeUser = "Secur3D_User";
    const identifier = "jacket";
    const type = "file_name";
    const response = generateModel({ IdToken, activeUser, identifier, type });