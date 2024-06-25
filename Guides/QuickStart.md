# Introduction

In this guide you will learn how to setup an account, login to the account, upload a model from the account through a custom API request, then finally retrieve the results from the upload.  

## Making an account

The first step of using the Secur3D service is to create an account on our website. You can find the account creation page [here](https://www.app.secur3d.ai/auth/signup). Please go through the steps and then verify the account with the MFA code in your email. Once that is done you will now have access to all the services.

## Creating a login request

Now that you have an account you will be able to login to the account using a custom API call. To make requests to all of our services you will either need an IdToken which you can get from the following login request or an API Key. 

The latter is only needed if you intend to integrate as a organization account, If you do plan to use this feature though, please continue this guide through the organization quick start guide.  

To get your IdToken you can create a API call similar to this one

```
// Declare Api function
authenticate: builder.mutation({
query: (credentials) => {
    const { username, password } = credentials;
    const url = `https://api.secur3d.ai/authentication/login`;
    const body = JSON.stringify({ username, password });
    return {
    url: url,
    method: "POST",
    headers: {
        "User-Agent": "Secur3D Quick Start Guide",
        "Content-Type": "application/json"
    },
    body: body
    };
}
});

// Call Api function
const username = "Secur3D_User";
const password = "SuperStrongPassword";
const response = authenticate({ username, password });

response.then((data) => {
if (data.idToken) {
    console.log('ID Token:', data.idToken);
} else {
    console.log('Authentication failed');
}
});
```

## Making an upload request

Now that you have your IdToken you can proceed with any of our API calls. To start though we will cover the [upload-model endpoint](https://www.app.secur3d.ai/documentation/API/model-function/upload-model) that allows you to submit a model to be scanned by our services.

This function is separated into 2 parts. The first is the request for a presigned-url. The following is an example of how to make this request.

```
//Declare Request Presigned URL API function
requestPresignedURL: builder.mutation({
    query: (model) => {
    const { IdToken, assetId, file, activeUser, encodedHash } = model;

    return {
        url: `https://api.secur3d.ai/model-function/upload-model`,
        method: "POST",
        body: {
        object_key: file.name,
        asset_id: file.name.substring(0, file.name.lastIndexOf(".")),
        shaHash: encodedHash,
        selectedUser: activeUser,
        },
        headers: {
        "auth-token": IdToken,
        "Content-Type": "application/json",
        }
    };
    },
    invalidatesTags: ["Models"]
})
```

There are a few values passed in here, for a full list of parameters please visit the upload-model documentation page but for now this will only cover the necessary parameters.

`assetId` is the file name excluding the file extension.  
`file` is the file of the model you want to upload.  
`activeUser` is either a username or organization name. This would either be the username of the user you logged in with or an organization that the user has access too.  
`encodedHash` is the sha256 hash of the whole file. This is used to ensure the file has been uploaded correctly.  

Now after you sent that request you will receive a presigned-url from the request which you can upload your file to.  
Here is an example of how to make the second request and also how to call the first request and use its response in the second request.

```
//Declare Upload Model API function
uploadModel: builder.mutation({
    query: (response) => {
    const preSignedUrl = response.url.url;

    if (!preSignedUrl) {
        throw new Error("pre-signed url request failed, is null");
    }

    const formData = new FormData();

    Object.entries(response.url.fields).forEach(([key, value]: any) => {
        formData.append(key, value);
    });

    formData.append("file", response.file);

    return {
        url: preSignedUrl,
        method: "POST",
        body: formData,
    };
    }
})

//Declare required variables
const IdToken = "YourIdTokenFromLoginRequest";
const file = [FILE_TO_UPLOAD]; //The actual file, not path
const activeUser = "Secur3D_User";

//Upload selected file
const fileUpload = async () => {
    const encodedHash = useEncodedHash(file);
    const response = await requestPresignedURL({ IdToken, assetId, file, activeUser, encodedHash });
    if (response) {
        uploadModel({ file, ...response });
    }
};
```

## Retrieving the results

Now that you have uploaded a model, you will want to retrieve the results from that upload.  
For this you will need a `hash`, this is a unique id for each model that gets generated when you upload a model, it will also be returned by the first upload model request.  
To get the results you will need to make a request to the [request-file-data endpoint](https://www.app.secur3d.ai/documentation/API/model-function/request-file-data). Here is an example of how to make a request to it.

```
//Declare Api function
modelData: builder.mutation({
    query: (model) => {
        const { IdToken, hash, activeUser } = model;
        const url = `https://api.secur3d.ai/model-function/request-file-data?$hash=${hash}&selectedUser=${activeUser}`;
        return {
            url: `${url}`,
            method: "GET",
            headers: {
            "auth-token": IdToken,
            }
        };
    }
})

//Call Api Function
const IdToken = "YourIdTokenFromLoginRequest";
const activeUser = "Secur3D_User";
const identifier = "HashFromModelUploaded";
const response = generateModel({ IdToken, activeUser, identifier });
```

## Final notes

Thank you for reading through the quick start guide! Since this only covers the basics please check out the individual documentation pages of each endpoint to get a better understanding of what they offer and what else you can do with this service.  
