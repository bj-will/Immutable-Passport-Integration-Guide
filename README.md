# Immutable-Passport-Integration-Guide
A comprehensive guide for integrating Immutable Passport seamlessly into your applications.

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Getting Started](#getting-started)
    - [1. Creating a Simple Application](#1-creating-a-simple-application)
    - [2. Registering the Application on Immutable Developer Hub](#2-registering-the-application-on-immutable-developer-hub)
    - [3. Installing and Initializing the Passport Client](#3-installing-and-initializing-the-passport-client)
4. [User Authentication](#user-authentication)
    - [1. Logging in a User with Passport](#1-logging-in-a-user-with-passport)
    - [2. Displaying User Information](#2-displaying-user-information)
    - [3. Logging Out a User](#3-logging-out-a-user)
5. [Transactions](#transactions)
    - [1. Initiating a Transaction from Passport](#1-initiating-a-transaction-from-passport)
6. [Sample Application](#sample-application)
7. [License](#license)

## Introduction
Immutable Passport is a blockchain-powered identity and wallet system tailored to meet the demands of Web3 gaming. It provides users with a persistent identity that seamlessly spans across various Web3 games, ensuring uniform settings and configurations across all applications.

Additionally, Immutable Passport includes a non-custodial wallet as the default choice for every user, guaranteeing a transaction experience that aligns with the familiar standards of Web2, all within the Web3 environment.

This guide will walk you through integrating Immutable Passport into your application, offering a secure and efficient means of user authentication and blockchain transactions. We will create a simple application to showcase the integration.
We'll use JavaScript and Node.js in this guide.

## Prerequisites
Before getting started, ensure you have the following prerequisites in place:

- [Node.js and npm](https://nodejs.org/en/download/): Make sure you have Node.js and npm installed on your system.
- A code editor of your choice (e.g., [Visual Studio Code](https://code.visualstudio.com/)): You'll need a code editor for development.
- [Git](https://github.com/git-guides/install-git): Install Git for cloning a repository of the sample application.
- A [Immutable Passport](https://www.immutable.com/gamers/passport): Register for an account on the Immutable Platform, which you'll need for the integration process.

## Getting Started
### 1. Creating a Simple Application
In this step, you have two options to choose from for creating your Immutable Passport integration application:

**Creating a New Node.js Application:** You have the flexibility to create a new application tailored to your specific needs. This option allows you to design and customize your application as desired.

```shell
mkdir my-immutable-passport-app
cd my-immutable-passport-app
```

- **Cloning the Immutable Passport Integration Project:** Alternatively, you can clone a pre-built project repository specifically designed for seamless Immutable Passport integration. This option can save you time and effort, making it a quick and efficient way to get started.

```shell
git clone <repository_URL>
cd <repository_directory>
```

Before proceeding, make sure to initialize your npm project:

```shell
npm init -y
```

This command initializes a new npm project with default settings. The `-y` flag automatically accepts all default options, saving you from having to manually answer prompts.

### 2. Registering Your Application on Immutable Developer Hub

To register your application with the Immutable Developer Hub, follow these steps:

**Sign In or Create an Account:**

1. Visit the [Immutable Developer Hub](https://hub.immutable.com/).
2. Click on the "Log In" option or "Create Passport" to create a new account.
3. Follow the on-screen instructions to complete the registration process.

**Register Your Application:**

4. Click on the "Add Project" option or select an existing one if you have one.
5. Provide a unique name for your project in the "Project Name" field and select "Immutable zkEVM" as the rollup.
6. Choose the "Testnet" environment in the next step.
7. Review the Passport configuration for your project. Locate the section labeled "Application type: Website" and make any necessary edits.
8. In the "Logout URLs" section, enter valid URLs where users can securely log out, directing them to your application's logout functionality.
9. In the "Redirect URLs" section, ensure that your application's callback URL matches the one you provided during the Passport client initialization.
10. Make sure to copy the "Client ID" string from the last section, as it will be required for the next steps.

### 3. Installing and Initializing the Passport Client

3.1 **Prerequisites**
Install the required packages and development dependencies

```shell
npm install -D @imtbl/sdk
npm install -D typescript ts-node
```

- `npm install -D @imtbl/sdk`: This installs the Immutable SDK as a development dependency. It includes the tools you need for integrating Immutable Passport into your application.

- `npm install -D typescript ts-node`: These dependencies are for TypeScript, a popular language for building JavaScript applications. `ts-node` is used to execute TypeScript code directly.

3.2 **Initializing the Passport Client**
initialize Immutable Passport with the following configuration. This simple code sets up the necessary parameters for Passport to work correctly.

```javascript
window.passport = new window.immutable.passport.Passport({
  baseConfig: new window.immutable.config.ImmutableConfiguration({
    environment: window.immutable.config.Environment.SANDBOX,
  }),
  clientId: "<project-client-id>",
  redirectUri: "<project-redirect-uri>",
  logoutRedirectUri: "<project-logout-redirect-uri>",
  audience: "platform_api",
  scope: "openid offline_access email transact",
});
```

**Explanation:**

- `baseConfig`: Here, you set the environment for your application. Use `SANDBOX` for a testing environment and `PRODUCTION` when you're ready for a live environment.

- `clientId`: Replace `"<project-client-id>"` with the actual client ID you received after registering your application on the Immutable Developer Hub. This is a crucial identifier for your application.

- `redirectUri`: Specify the URL where users will be redirected after authentication. Make sure it matches the URL you registered during application setup.

- `logoutRedirectUri`: Provide the URL where users should be directed after logging out. Ensure it corresponds to your application's logout functionality.

- `audience`: Set this to "platform_api" for Immutable Passport.

- `scope`: Define the scope of permissions for your application, such as "openid," "offline_access," "email," and "transact."

If you want to learn more about initializing Passport, please visit the [official documentation](https://docs.immutable.com/docs/zkEVM/products/passport/install).

Once you've filled in the appropriate values, your Immutable Passport is ready to authenticate users and perform transactions within your application.

## User Authentication

### 1. Logging in a User with Passport

To implement user login logic, follow these steps:

1. Add event listeners to the "Login" and "Logout" buttons:

```javascript
window.addEventListener('load', function() {
  const loginButton = this.document.getElementById('btn_login');
  const logoutButton = this.document.getElementById('btn_logout');
  loginButton.onclick = function() {
    connectUser();
  }
  logoutButton.onclick = disconnectUser;
  window.passport.loginCallback();
});
```

2. Below is the code for the user login part:

```javascript
window.provider = window.passport.connectEvm();

const connectUser = async function() {
  try {
    window.accounts = await window.provider.request({ method: "eth_requestAccounts" });
    if (window.accounts) {
      await initUserProfile();
    };
  } catch (error) {
    console.error("Error connecting user:", error);
  }
};

const initUserProfile = async function() {
  try {
    window.userProfile = await window.passport.getUserInfo();
  } catch (error) {
    console.error("Error initializing user profile:", error);
  }
};
```

This code allows users to log in and retrieves user information after a successful login. In case of any errors during the process, error messages will be displayed in the console for debugging.

### 2. Displaying User Information

To display user information in your game, you need to check whether the user is logged in and adjust the game behavior accordingly. For example, if you have a function called `process()` where you handle the game logic, you can add a check like this:

```javascript
function process() {
  if (window?.userProfile?.email) {
    // User is authenticated, perform main game logic
  } else {
    // User is not logged in, show a start screen
  }
}
```

This code checks if the user's email is available in the user profile, and based on this, you can customize the behavior of your game.

### 3. Logging Out a User

Logging out a user is a simple procedure:

```javascript
const disconnectUser = async function() {
  try {
    await window.passport.logout();
    window.userProfile = {};
  } catch (error) {
    console.error("Error disconnecting the user:", error);
  }
}
```

This function allows the user to log out of their session, clearing their profile data in the process.

## Transactions

### 1. Initiating a Transaction from Passport

To initiate a transaction and obtain the transaction hash, you can use the following example function:

```javascript
async function handleTransaction(data) {
  try {
      // Initiate the transaction
      const transactionHash = await window.passport.initiateTransaction({
          to: "project_contract_address",
          data: data,
      });

      // Handle the transaction hash
      console.log('Transaction Hash:', transactionHash);
  } catch (error) {
      console.error('Error initiating the transaction:', error);
  }
}
```

This function sends data in a transaction and logs the resulting transaction hash.

## Sample Application

You can find a sample application in this repository.

## License
This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.