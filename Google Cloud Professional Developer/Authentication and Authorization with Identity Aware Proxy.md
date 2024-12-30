# Authentication and Authorization with Identity Aware Proxy
## Getting started with OAuth 2.0
- Authentication and Authorization
- Authentication: Who is using
- Authorization: What the user can do
OAuth is an Authorization Framework. Allows users to grant access to server resources to another entity WITHOUT sharing credentials.
**Example**: Give access to your Google Drive to your Photo Editing Application.

**Resource owner**: you
**Resource**: your Google Drive
**Client**: your Photo Editing application
**Authorization Server**: Google OAuth Service
**Client IDs and Client Secrets**: How does your Photo Editing application authenticate itself to google?
**Scope**: What is it allowed to do?

## Playing with OAuth 2.0
You can access [OAuth Playground](https://developers.google.com/oauthplayground/) to play with Google's OAuth

## OpenID Connect (OIDC)
OAuth is ONLY about authorization. Does **not** care about how authentication is implemented.
*What is you want to allow users to login with Google Id into your application?*
- Use **OpenID Connect (OIDC)**, which extends OAuth 2.0

## Identity Aware Proxy (IAP)
- It is the easiest way to implement authentication and authorization for apps deployed in App Engine, Cloud Run and GKE.
- It simplifies the implementation of authentication and authorization for your cloud-based and on-premises applications.
- Use user *identity* and *context* to make your authentication/authorization decisions.
- Simplified integration with App Engine, Cloud Run and GKE.
- Does not need a VPN.
### How does IAP work?
#### Execution Flow:
1. When enabled, IAP intercepts calls to applications
2. If user is unauthenticated
	1. User is redirected to OAuth 2.0 Google Account sign-in flow
3. If user is authenticated (Valid OAuth token is present with the request)
	1. User authorization is verified
	2. If successful, user is allowed access to the resource

- User identity is **passed to backend service** using HTTP headers
	- X-Goog-Authenticated-User-Email - User's email address
	- X-Goog-Authenticated-User-Id - Unique user identifier

### IAP with Kubernetes
In a GKE cluster, traffic usually comes via HTTP(S) Load Balancing (typically configured using ingress)
#### Steps to set up IAP with GKE
1. Create Client Secret ID and Key
`kubectl create secret generic my-client-secret --from-literal=client_id=CLIENT_ID_KEY --from-literal=client_secret=CLIENT_SECRET_KEY`
2. Configure BackendConfig using Secret. This is typically used to customize your Kubernetes Ingress
3. Configure Kubernetes Service to use the BackendConfig
	1. Alternative: Configure IAP with Anthos Service Mesh (also uses BackendConfig)