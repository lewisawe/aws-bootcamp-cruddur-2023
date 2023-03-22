# Week 3 — Decentralized Authentication

## COGNITO

AWS Cognito is a user authentication and authorization service provided by Amazon Web Services (AWS). It allows developers to add user sign-up, sign-in, and access control to their web and mobile applications quickly and easily.

Cognito offers features such as user registration and login, social identity providers (such as Facebook and Google), multi-factor authentication, and user data synchronization across devices. Additionally, it supports both JSON Web Tokens (JWTs) and OAuth 2.0 access tokens for secure access to APIs and resources.

Cognito can also be integrated with other AWS services such as AWS Lambda, Amazon API Gateway, and Amazon S3 to create fully managed serverless applications. It provides a scalable and secure solution for managing user authentication and authorization in modern applications.

## Steps to set up

Install AWS Amplify in frontend folder, 

```
npm i aws-amplify --save
```

### Create a Cognito users pool in the AWS Console

- Located in Amazon Cognito
- Create a news user pool
- Use default Cognito User pool
- For signin options use user name and email
- next
- Password policies leave as default
- For Multi-factor Authentication choose no MFA, 
- User account recovery leave as default, self service recovery
- Delivery method of account recovery use email, other options may incur cost
- Configure sign up experience leave default, self service sign up
- Allow cognito to verify
- use email
- Required attributes
  - name 
  - preffered_username
- Configure message delivery check send email with cognito
- Intergrate your app
- User pool name - cruddur-user-pool
- dont use cognito hosted ui
- app type select public client
- app client name - cruddur
- review and create user pool

### CONFIGURE AMPLIFY

Go into your Frontend-react-js >> src >> App.js

Paste at the end of the import codes the following

```.js
import { Amplify } from 'aws-amplify';

```

After the imports paste the code:

```
Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_APP_AWS_PROJECT_REGION,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_APP_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});
```

In the docker-compose.yml file add the code at the frontend-react-js enviroment variables
-user pool and client ID are found in the AWS Conginto Console


```
REACT_APP_AWS_PROJECT_REGION: "${AWS_DEFAULT_REGION}"
REACT_APP_AWS_COGNITO_REGION:"${AWS_DEFAULT_REGION}"
REACT_APP_AWS_USER_POOLS_ID:"us-east-1_8UnSKONjw"
REACT_APP_CLIENT_ID:"35a1pqum0qrq9liej8kloq3ufb"

```

### Conditionally show components based on logged in or logged out

Navigate to the frontend-react-js >> src >> pages >> HomeFeedPage.js

Import Auth for AWS Amplify

```

import { Auth } from 'aws-amplify';

```

Replace the check auth function located at around code no 40 with

```
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};

// check when the page loads if we are authenicated
React.useEffect(()=>{
  loadData();
  checkAuth();
}, [])

```

Navigate to the frontend-react-js >> src >> components >> DesktopNavigation.js

You may clean up the code from line 19 - 31  with this

```
  let trending;
  let suggested;
  let join;
  if (props.user) {
    trending = <TrendingSection trendings={trendings} />
    suggested = <SuggestedUsersSection users={users} />
  } else {
    join = <JoinSection />
  }
```

Navigate to the frontend-react-js >> src >> components >> ProfielInfo.js

Replace line 6 with: 
```
import { Auth } from 'aws-amplify';
```

replace line 15 - 25 with
```
const signOut = async () => {
  try {
      await Auth.signOut({ global: true });
      window.location.href = "/"
  } catch (error) {
      console.log('error signing out: ', error);
  }
}
```

Navigate to the frontend-react-js >> src >> pages >> SigninPage.js

replace line 7 with - the line with cookies
```
import { Auth } from 'aws-amplify';
```

at line 15 - 26 for the onSubmit function

```
const onsubmit = async (event) => {
  setErrors('')
  event.preventDefault();
  try {
    Auth.signIn(email, password)
    .then(user => {
      localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
      window.location.href = "/"
    })
    .catch(error => { 
      if (error.code == 'UserNotConfirmedException') {
        window.location.href = "/confirm"
      }
      setErrors(error.message)
    });
  return false
}

let errors;
if (cognitoErrors){
  errors = <div className='errors'>{cognitoErrors}</div>;
}

// just before submit component
{errors}

```

### return to AWS Cognito console

- Create a new user
- enter new username
- enter email address
- set a password according to policies




## Congito JWT Server side Verify

Add to frontend-react >> src >> pages >> homefeedpage.js line 27 after const res = await and before method 'get'

```
  headers: {
    Authorization: `Bearer ${localStorage.getItem("access_token")}`
  }
  
 ```
 
 
