---
layout: post
title: UW Spring Security
tags: 
- spring
- security
- java
- authentication
- authorization
---

Here are slides from a talk I am giving on Friday, March 4 2016:

[uw-spring-security](https://docs.google.com/presentation/d/1brbaYboPGcBnEaeYoDZAl7O2cO9RKMqPgXqIOjODmj0/edit?usp=sharing)

This talk is focused on a project my colleagues and I have been developing over the past year called [uw-spring-security](https://git.doit.wisc.edu/adi-ia/uw-spring-security). [uw-spring-security](https://git.doit.wisc.edu/adi-ia/uw-spring-security) is a multi-module Java project that University of Wisconsin applications can use to secure web applications in a way that is compatible not only with our single-sign-on technology, [Shibboleth](https://shibboleth.net/), but how we deploy it.

I have used [Spring Security](http://projects.spring.io/spring-security/) for many years, and in my opinion, it is **exceptional**. It has comprehensive support for all things authentication and authorization. The only drawback: it is _terribly difficult_ to get started with. The [reference manual](http://docs.spring.io/spring-security/site/docs/4.0.4.RELEASE/reference/htmlsingle/) is 36 chapters. Just the getting started chapter (chapter 3) is a long read, and the topics covered aren't relevant to what University of Wisconsin Java developers need to know.

Pairing Spring Security with the way we deploy Shibboleth is not intuitive. [uw-spring-security](https://git.doit.wisc.edu/adi-ia/uw-spring-security) addresses that by replacing the boilerplate configuration classes described in the Spring Security reference manual with:

```java
@Configuration
@Import(UWSpringSecurityConfiguration.class)
public class MyWebSecurityConfiguration {
  @Bean
  public HttpSecurityAmender httpSecurityAmender() throws Exception {
    return new HttpSecurityAmender() {
      @Override
      public void amend(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/protected").authenticated();
      }
    };
  }
}
```

Starting a web application with that `@Configuration` properly registered will result in seeing a login form when hitting the `/protected` URL.

If the entire application requires uniform authentication, there's an even simpler example:

```java
@Configuration
@Import(EverythingRequiresAuthenticationConfiguration.class)
public class MyWebSecurityConfiguration {
}
```

Determining whether Shibboleth ([pre-authentication](http://docs.spring.io/spring-security/site/docs/4.0.4.RELEASE/reference/htmlsingle/#preauth)) is enabled or a local login form is used is accomplished by activating one (or both) of the 2 Spring `@Profile`s the library supports: `preauth` or `local-users`, respectively. Both profiles present the exact same data model for a User:

```json
{
  pvi: "UW000A000",
  username: "admin",
  password: null,
  fullName: "Amy Administrator",
  emailAddress: "amy.administrator@demo.wisc.edu",
  uddsMembership: [
    "A535900"
  ],
  authorities: [ ],
  accountNonExpired: true,
  accountNonLocked: true,
  credentialsNonExpired: true,
  enabled: true,
  eppn: null,
  emailAddressHash: "b09ed4fa2272feede8b472d1184829dd",
  source: "local-users",
  customLogoutUrl: null,
  isisEmplid: null,
  firstName: null,
  lastName: null
}
```

This library has been immensely useful within my group, and now it is time to share with a wider audience.

This library paired with [uw-frame](https://github.com/UW-Madison-DoIT/uw-frame) and [rest-proxy](https://github.com/UW-Madison-DoIT/rest-proxy) gives us all we need to create business applications in [My UW Madison](https://my.wisc.edu). These 3 components provide all of the application middleware, and allow us to write user interfaces simply with HTML, JavaScript and AngularJS.
