# AWS Serverless Application with Web Identity Federation

### Project Overview:

This project demonstrates the implementation of a serverless application using Web Identity Federation. The application leverages several AWS services and integrates with Google as an Identity Provider.

### Technologies Used:

* S3: Front-end application hosting

* Google API Project: ID Provider

* Cognito & IAM Roles: To exchange Google Token for AWS credentials

### Application Workflow: 

* The application runs in a browser.

* Users log in using their Google ID.

* The application retrieves and displays images from a private S3 bucket using presigned URLs.

## Stages

### STAGE 1: Provision the Environment

Description:
In this stage, you will provision the necessary AWS environment and review the tasks to be completed.

Instructions:

1. Login to an AWS Account

2. Set the region to **us-east-1 N. Virginia**

3. Click the Deployment link: [CLICK HERE](https://learn-cantrill-labs.s3.amazonaws.com/aws-cognito-web-identity-federation/WEBIDF.yaml)

4. Verify S3 Bucket

*  [Open the console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)

    ![alt text](Stage1(1).png)
* Open the bucket: **webidf-appbucket** | Objects within:

    * Index.html

    * Scripts.js
    ![alt text](image-1.png)

5. Open private bucket: **webidf-patchesprivatebucket-**
    * Verify the images 3 images
![alt text](image-2.png)

6. Move to the **CloudFront Distribution**

    * [Open the CloudFront consle](https://us-east-1.console.aws.amazon.com/cloudfront/v3/home?region=us-east-1#/distributions)
    *  Open the distribution pointing at **webidf-appbucket-**
    ![alt text](image-3.png)
    * Copy the **Distribution domain name**
    ![alt text](image-6.png)



### STAGE 2: Create Google API Project & Client ID

Description:
Set up a Google API Project and generate a Client ID.

Instructions:

1. Move to the Google Credentials page [HERE](https://console.developers.google.com/apis/credentials)

    Requirements: 
        * Valid Google account

        If you don't have one, create a new valid google account
2. Create Google API Project

    1. Click **Select a Project** ![alt text](image-8.png)
    2. Click **NEW PROJECT**
    ![alt text](image-9.png)
    3. Enter Project name: **PetIDF**
    ![alt text](image-10.png)
    4. Click **CREATE**


3. Configure Cosnsent Screen
    1. Click the credentials page 
    ![alt text](image-11.png)
    1. Click **CONFIGURE CONSENT SCREEN**
    ![alt text](image-12.png)
    2. Click **External** 
    ![alt text](image-13.png)
    3. Click **Create**
    4. Enter **App Name**, **User support email**, and **Developer contact information**
    ![alt text](image-14.png)
    ![alt text](image-15.png)
    5. Click SAVE AND CONTINUE
    6. Click SAVE AND CONTINUE
    7. Click SAVE AND CONTINUE
    8. Click BACK TO DASHBOARD


4. Create Google API PROJECT CREDENTIALS

    1. Click **CREDENTIALS** and **OAuth client ID** ![alt text](image-7.png)
    ![alt text](image-5.png)
    2. In Applicaiton type choose **Web Application** and  Under Name enter **PetIDFServerlessApp**
    ![alt text](image-16.png)
    3.  Click A**DD URI** under Authorized JavaScript origins 
    
        *This is the distribution domain name of the cloudfront distribution with https:// before it*
    ![alt text](image-17.png)
    4. Click **CREATE** 
    * It will be prompt with two pieces of information

        * Client ID
        * Client Secret

        Note down the Client ID you will need it later. 
        You wont need the Client Secret again.
        
        Once noted down safely, click OK 

### STAGE 3: Create Cognito Identity Pool

Description:
Configure a Cognito Identity Pool to enable Web Identity Federation.

Instructions:

1.  Move to the Cognito Console. [HERE](https://console.aws.amazon.com/cognito/home?region=us-east-1#)
2. Click **Create identity pool**
![alt text](image-18.png)
    1. Expand **Authentication Providers**, click on **Google+**, and click **Next**
    ![alt text](image-19.png)
    2. Enter **PetIDPoolAuth_Role** under *IAM role name*, click **Next**
    ![alt text](image-22.png)
    3. Enter Client ID from Google, and click **Next**
    ![alt text](image-23.png)
    4. Enter **PetIDFIDPool** under *Name*, and click **Next**
    ![alt text](image-24.png)
    5. Click **Create Identity Pool**
    6. Note down *Identity Pool ID*
    ![alt text](image-25.png)
3. Update Permissions
    1. Move to IAM console. [HERE](https://console.aws.amazon.com/iam/home?region=us-east-1#/home)
    2. Under *Roles*, Look for **Cognito_PetIDFPoolAuth_Role**
    ![alt text](image-26.png)
    3. Under *Permissions*, click *Attach Policies*
    ![alt text](image-27.png)
    4. Select *PrivatePatchesPermissions*, click **Add Permissions**
    ![alt text](image-28.png)



### STAGE 4: Update App Bucket & Test Application

Description:
Update your S3 bucket for the front-end application and test the complete setup.

Instructions:

1. Move to S3 console
    * Under *General Purpose Buckets*, click *webidf-appbucket*
    2. Select *index.html*, and click **Download**
    3. Select *scripts.js*, and click **Download**
    ![alt text](image-29.png)


2. Open index.html on text editor   
    * Specify Google client ID
    * Save
    ![alt text](image-30.png) 

3. Open sripts.js on text editor   
    * Paste cognito identity pool id
    ![alt text](image-31.png) 
    * Paste the whole bucket name
    * Save
    ![alt text](image-33.png)
    ![alt text](image-32.png)

4. Upload updated files on S3
    * Click Upload
    ![alt text](image-34.png)  
    * Click Add files  
    ![alt text](image-35.png)
    * Click Upload

5. Test Applications
    1. Move to CloudFront
    2. Copy Distribution domain name, and open in a new browser tab
    ![alt text](image-36.png)
    3. Open your browser web developer tools (firefox tool->browser tools-> web developer tools)
    With the browser console open, Click **Sign In**
    When you click the Sign In button a few things happen:- (watch the console)

    You authenticate with the Google IDP
    a Google Access token is returned
    This token is provided as part of the API Call to Cognito
    If successful this exchanges this for Temporary AWS credentials
    These are used to list objects in the private bucket
    for all objects, presignedURLs are generated and used to load the images in the browser.
    ![alt text](image-38.png)


## Finish

At this point you have a fully functional simple serverless application, complete with :-

* template front end app bucket
* Configured Google API Project
* Credentials to access it
* Cognito ID Pool
* IAM Roles for the ID Pool
* HTML and JS configured to access the Google IDP and Cognito



## Diagram:
![alt text](image-39.png)





1-Click Deployment

To quickly deploy the environment, use the 1-Click Deployment link below:
[CLICK HERE](https://learn-cantrill-labs.s3.amazonaws.com/aws-cognito-web-identity-federation/WEBIDF.yaml)

Disclaimer

This project includes a 1-Click Deployment link provided by Adrian Cantrill. The link is used with permission and is subject to the terms and conditions set by the author. The author and contributors are not responsible for any issues or changes arising from the use of this link. Ensure you review and understand all steps before deployment.

Credits

Adrian Cantrill: For the 1-Click Deployment link and related resources.