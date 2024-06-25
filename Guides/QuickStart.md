# Introduction

In this guide you will learn how to setup an account, login to the account, then upload a model from the account through a custom API request. 

## Making an account

The first step of using the Secur3D service is to create an account on our website. You can find the account creation page [here](https://www.app.secur3d.ai/auth/signup). Please go through the steps and then verify the account with the MFA code in your email. Once that is done you will now have access to all the services.

## Creating a login request

Now that you have an account you will be able to login to the account using a custom API call. To make requests to all of our services you will either need an IdToken which you can get from the following login request or an API Key. 

The latter is only needed if you intend to integrate as a organization account and can be found [here](https://www.app.secur3d.ai/dashboard/organization/webhook) when signed in as a organization. For more details into how to use this you can visit the organization section of the API documentation.  

To do this you can create a API call similar to this one

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

## Making a upload request

Now that you have your IdToken you can proceed with any of our API calls. To start though we will cover the upload-model endpoint that allows you to submit a model to be scanned by our services.

This function is separated into 2 parts. The first is the request for a presigned-url. The following is an example of how to make this request.

```
//Declare Request Presigned URL API function
requestPresignedURL: builder.mutation({
    query: (model) => {
    const { IdToken, assetId, file, activeUser, encodedHash } = model;

    const url = `upload-model`;
    return {
        url: `/${url}`,
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