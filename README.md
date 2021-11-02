# Grafana with iframe / in the iframe

These instructions are only recommendations. They are not ultimate solutions, 
which will work for any use cases. There can be usually some components in the 
middle (reverse proxy, WAF, IdP brokering, ...), which can make final solution 
more complex.

## I want to have some iframe in my Grafana

Problem: I want to see some other sites (e.g. camera view, another monitoring tool,
youtube, ...) inside my Grafana.

Solution:

1.) Configure Grafana
```
[panels]
disable_sanitize_html = true
```

You can write any html in panels. You need to sacrifice default security, 
so make sure you know what are doing.

2.) Use `Text` panel type and write HTML which will create iframe:
![](https://raw.githubusercontent.com/jangaraj/grafana-iframe-sso/main/images/text-panel-iframe.png)

Troubleshooting:

Use browser console to find any problems, why iframe in Grafana can't be loaded.
E.g.: 
- `Blocked loading mixed active content` - so your Grafana is running on 
https, but iframe is loading via http protocol, which is blocked by the browser - 
you can't configure anything in this case. Just load also iframe via https 
protocol, so browser security will be satisfied.
- `The loading of "https://domain.com/" in a frame is denied by "X-Frame-Options" 
directive set to "SAMEORIGIN"` - again you can't configure anything in the 
Grafana to solve. Owner of iframed paged has specified in the page header, that
his page can't be iframed. It is due to security, because iframes are used 
for clickjacking attacks. So expectation, e.g. I will load https://www.google.com 
into iframe is wrong, because Google is aware of security problem and it isn't 
allowing that.


## I want to have Grafana in my app as an iframe and I would like to have seamless login experience

Problem: I have my app written in my favourite language/framework (e.g. React, 
Vue, Angular, Next, Nuxt, Ember, Svetle, ...) and I would like to iframe my cool
Grafana into that app in the safe and user convenient way.

Solution:

1.) Use Single Sign On (SSO) protocol (usually Open ID Connect - OIDC) for your 
app with your favorite Identity Provider (IdP) - e.g. Keycloak, Auth0, Okta, Azure, 
OneLogin, Ping, Cognito, social providers (Facebook, Twitter, Google, LinkedIn, 
...). Make sure your app initializes SSO session before any iframe with Grafana
is loaded. Make sure also you are using correct OIDC flow suitable for your app
case - see https://developer.okta.com/docs/concepts/oauth-openid/#what-kind-of-client-are-you-building
(for SPA devs: don't use first Stackoverflow result; and no direct grant flow/
resource owner password flow doesn't provide SSO and it is insecure - spend more
time to understand how Authorization Code flow with PKCE works)

2.) Use Single Sign On (SSO) protocol (usually Open ID Connect - OIDC) for your
Grafana - https://grafana.com/docs/grafana/latest/auth/generic-oauth/ with the 
same IdP as you have used for your app.

Configure Grafana properly - it must be working with used IdP in your browser.
Yes, Grafana cookies must be configured properly (typical problem 
`login.OAuthLogin(missing saved state)`) and also Grafana must be exposed via 
https protocol (requirement from OIDC SSO protocol).

Don't forget:
```
[security]
allow_embedding = true

[auth]
oauth_auto_login = true
```
So Grafana will initialize OAuth login automatically.

3.) Test it
Load your app - that initiliazes SSO session, which will be used when iframe 
with Grafana will be loaded.

## Contact

Insructions are provided with my best effort. Please don't contact me if you want
to ask for free help/support. Feel free to reach me if you need commercial paid 
help/support.
