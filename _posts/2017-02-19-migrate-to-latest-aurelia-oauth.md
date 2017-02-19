---
layout: post
title: "Migrate aurelia-oauth plugin to version 0.3.0"
categories: [OAuth, Aurelia, Plugins, JS, HttpClient, FetchClient]
disqus: true
---

![Aurelia + OAuth2](/images/2017-02-19/aurelia-oauth.png)

Recently, I found a little of spare time to work on my [aurelia-oauth](https://github.com/matik12/aurelia-oauth) plugin to implement fixes, improvements and add new features. In this post I want to present latest plugin changes and describe a few steps that are required to migrate your project to the latest plugin version - 0.3.0.

<!--more-->

### Recent changes

This is a list of changes in chronological order introduced after version 0.1.5 has been released:
- Changed plugin skeleton work-flow, updated lint rules and code clean up
- Changed names of interfaces to remove letter 'I' and removed additional interfaces for service classes
- Added new export for OAuthInterceptor in main plugin module
- Added **Travis CI** to perform build on each commit/merge
- Updated gulp task for transpilation of typescript file during build using latest Typescript version
- Added generation of typescript definition files (d.ts) by transpiler - no more ambient definitions, only module
- Released new plugin version 0.1.6
- Decoupled plugin from aurelia-http-client and **added supported for both fetch and http clients**
- Simplified OAuthInterceptor logic for adding authorization header and handle request errors
- Released new plugin version 0.2.0
- Added new feature for **automatic token renewal** (this is enabled by default :-))
- Released new plugin version 0.3.0
- Updated ReadMe documentation file to reflect crucial changes

### Migrating from 0.1.5 and below

#### Typescript definitions

If you are using Typescript, remove already installed global definition via typings manager and then install new d.ts with the following command:

{% highlight shell %}
typings install github:matik12/aurelia-oauth --save
{% endhighlight %}

Later on, if you use any interfaces removed them in your code and if you want use strongly typed definitions then just import them from main plugin module

{% highlight typescript %}
import { OAuthService, OAuthTokenService } from 'aurelia-oauth';
{% endhighlight %}

#### Plugin configuration

After adding support for both fetch and http clients you need to manually invoke configureClient() method, which is passed as new parameter in plugin configuration function. With addition to that, you need to provide client, which you want to configure - fetch or http (of course you need to import it first and get its instance via container). 

{% highlight typescript %}
function configureOauth(oauthService: OAuthService, oauthTokenService: OAuthTokenService, configureClient: (client: any) => void, client: any) {
  oauthService.configure(
    {
      loginUrl: 'https://login.microsoftonline.com/{tenantId}/oauth2/authorize',
      logoutUrl: 'https://login.microsoftonline.com/{tenantId}/oauth2/authorize',
      clientId: '{clientId}',
      alwaysRequireLogin: true
    });

  configureClient(client);
}
{% endhighlight %}

### Summary

I hope you like changes, new features and the plugin itself. Feel free to leave your feedback in the comments below.
