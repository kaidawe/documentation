# Introduction

In this guide you will learn how to setup an account, login to the account, upload a model from the account through a custom API request, then finally retrieve the results from the upload.  

## Making an account

The first step of using the Secur3D service is to create an account on our website. You can find the account creation page [here](https://www.app.secur3d.ai/auth/signup). Please go through the steps and then verify the account with the MFA code in your email. Once that is done you will now have access to all the services.

## Creating a organization

Now that you have an account you will be able to create an organization to access the endpoints from. To create an organization you can go to our website or follow [this](https://www.staging.secur3d.ai/dashboard/organization/create) link. Then just follow the prompts to create your organization. Once it has been created you can select it from the dropdown in the top right of the dashboard by clicking your profile picture.

Now with this organization account selected you will be able to access your API token to make requests to our system. To get your API key you can navigate to [this](https://www.staging.secur3d.ai/dashboard/organization/webhook) page and copy the code that is available.

## Making an upload request

Now that you have your Api Key you can proceed with any of our API calls. To start though we will cover the [upload-model endpoint](https://www.app.secur3d.ai/documentation/API/organizations/upload-model) that allows you to submit a model to be scanned by our services.

This function is separated into 2 parts. The first is the request for a presigned-url. The following is an example of how to make this request.

```
//Declare Request Presigned URL API function
requestPresignedURL: builder.mutation({
    query: (model) => {
    const { ApiKey, assetId, file, activeUser, encodedHash } = model;

    return {
        url: `https://api.secur3d.ai/service-resources/upload-model?selectedUser=${activeUser}`,
        method: "POST",
        body: {
            object_key: file.name,
            asset_id: file.name.substring(0, file.name.lastIndexOf(".")),
            shaHash: encodedHash,
        },
        headers: {
            "X-Api-Key": ApiKey,
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
`activeUser` is the organization name associated with your ApiKey.  
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
const ApiKey = "YourApiKeyFromTheWebsite";
const file = [FILE_TO_UPLOAD]; //The actual file, not path
const activeUser = "Secur3D_Organization";

//Upload selected file
const fileUpload = async () => {
    const encodedHash = useEncodedHash(file);
    const response = await requestPresignedURL({ ApiKey, assetId, file, activeUser, encodedHash });
    if (response) {
        uploadModel({ file, ...response });
    }
};
```

## Retrieving the results

Now that you have uploaded a model, you will want to retrieve the results from that upload.
For this you will need a hash, this is a unique id for each model that gets generated when you upload a model, it will also be returned by the first upload model request.
To get the results you will need to make a request to the request-file-data endpoint. Here is an example of how to make a request to it.

    //Declare Api function
    modelData: builder.mutation({
        query: (model) => {
            const { ApiKey, hash, activeUser } = model;
            const url = `https://api.secur3d.ai/service-resources/request-file-data?$hash=${hash}&selectedUser=${activeUser}`;
            return {
                url: `${url}`,
                method: "GET",
                headers: {
                    "X-Api-Key": ApiKey,
                }
            };
        }
    })

//Call Api Function
const ApiKey = "YourApiKeyFromTheWebsite";
const activeUser = "Secur3D_Organization";
const identifier = "HashFromModelUploaded";
const response = generateModel({ ApiKey, activeUser, identifier });

## Final notes

Thank you for reading through the quick start guide! Since this only covers the basics please check out the individual documentation pages of each endpoint to get a better understanding of what they offer and what else you can do with this service.  
