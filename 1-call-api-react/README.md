# React SPA - Microsoft Entra Authentication and Authorization Test

This project is a local test application for Microsoft Entra External ID authentication and authorization using a React single-page application and a protected ASP.NET Core Web API.

The SPA signs users in with MSAL React, requests access tokens, and sends those tokens to the API. The API validates the JWT and authorizes the request before returning the ToDo list data.

## Overview

- Frontend: React SPA using MSAL React
- Backend: ASP.NET Core Web API using Microsoft.Identity.Web
- Authentication: Microsoft Entra External ID
- Authorization model: delegated access token scopes
- Local test URLs:
  - SPA: http://localhost:3000
  - API: https://localhost:44351/api/todolist
- Tenant authority used in this project: https://IsuruTrader.ciamlogin.com/

## Project structure

```text
1-call-api-react/
├── API/
│   └── ToDoListAPI/
│       ├── appsettings.json
│       ├── Program.cs
│       └── Controllers/
├── SPA/
│   ├── package.json
│   ├── public/
│   └── src/
│       ├── authConfig.js
│       ├── App.jsx
│       └── ...
├── AppCreationScripts/
├── README.md
├── ReadmeFiles/
└── ...
```

## Prerequisites

Before running the application, make sure you have:

- Node.js and npm installed
- .NET SDK installed
- A Microsoft Entra External ID tenant
- App registrations for the React SPA and ASP.NET Core API
- A valid user account for sign-in and testing

## Configuration

This project is configured to use local environment files instead of committing tenant-specific values to source control.

### 1. Configure the SPA environment

Create or update the file:

```text
SPA/.env
```

Use values like:

```env
REACT_APP_CLIENT_ID=44a5a941-61cf-4bd2-9a84-1d0bb489193b
REACT_APP_TENANT_AUTHORITY=https://IsuruTrader.ciamlogin.com/
REACT_APP_API_CLIENT_ID=44a5a941-61cf-4bd2-9a84-1d0bb489193b
REACT_APP_API_ENDPOINT=https://localhost:44351/api/todolist
```

A template is available here:

```text
SPA/.env.example
```

The React app reads these values in `SPA/src/authConfig.js`.

### 2. Configure the API environment

Create or update the file:

```text
API/ToDoListAPI/.env
```

Use values like:

```env
AZURE_AD_INSTANCE=https://IsuruTrader.ciamlogin.com/
AZURE_AD_TENANT_ID=df66a7fd-b2b8-4281-b943-6dbad5679154
AZURE_AD_CLIENT_ID=6220d630-c821-4624-8049-01f6f084faf3
```

A template is available here:

```text
API/ToDoListAPI/.env.example
```

The API reads these values in `Program.cs` before falling back to `appsettings.json`.

## Required app registrations and permissions

### API app registration

- Expose an API
- Add delegated scopes:
  - `ToDoList.Read`
  - `ToDoList.ReadWrite`
- Enable token validation on the backend

### SPA app registration

- Add redirect URIs:
  - `http://localhost:3000`
  - `http://localhost:3000/redirect`
- Add delegated API permissions:
  - `ToDoList.Read`
  - `ToDoList.ReadWrite`
- Grant admin consent for the tenant

## Run the application locally

### 1. Start the API

```bash
cd 2-Authorization/1-call-api-react/API/ToDoListAPI
dotnet restore
dotnet run
```

### 2. Start the React app

Open a second terminal and run:

```bash
cd 2-Authorization/1-call-api-react/SPA
npm install
npm start
```

Then open the browser at:

```text
http://localhost:3000
```

## Authentication flow

1. The user signs in to the SPA using Microsoft Entra.
2. MSAL requests an ID token and access token.
3. The SPA calls the protected API with the bearer token.
4. The API validates the token and checks the required scopes.
5. The ToDo data is returned only if authorization succeeds.

## Testing checklist

Use this project to validate:

- successful Microsoft Entra sign-in
- token acquisition for the protected API
- bearer-token calls to the ASP.NET Core API
- delegated permission validation
- correct redirect handling after login and logout
- API authorization in a local development environment

## Troubleshooting

### Sign-in fails

- Verify the `authority` in `SPA/src/authConfig.js`
- Confirm the redirect URIs were registered in the app registration
- Check that the correct SPA client ID is configured

### API returns unauthorized

- Ensure the access token is included in the Authorization header
- Confirm the API app registration has the correct scopes exposed
- Check that the signed-in user has the required delegated permissions
- Confirm the API is listening on the expected HTTPS port

### CORS issues

- Make sure the API allows requests from the SPA origin
- Review the CORS settings in `API/ToDoListAPI/Program.cs`

## Notes

This project is intended for local testing and validation of Microsoft Entra authentication and API authorization. For production deployment, review security settings, CORS configuration, token handling, and authorization rules before exposing the app publicly.

## License

This project is provided for demonstration and testing purposes under the existing repository license.
