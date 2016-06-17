[![Build Status](https://travis-ci.org/jwtk/jjwt.svg?branch=master)](https://travis-ci.org/jwtk/jjwt)
[![Coverage Status](https://coveralls.io/repos/jwtk/jjwt/badge.svg?branch=master)](https://coveralls.io/r/jwtk/jjwt?branch=master)

# Java JWT: JSON Web Token for Java and Android

JJWT aims to be the easiest to use and understand library for creating and verifying JSON Web Tokens (JWTs) on the JVM.

JJWT is an implementation based on the [JWT](https://tools.ietf.org/html/rfc7519), [JWS](https://tools.ietf.org/html/rfc7515), [JWE](https://tools.ietf.org/html/rfc7516), [JWK](https://tools.ietf.org/html/rfc7517) and [JWA](https://tools.ietf.org/html/rfc7518) RFC specifications.

We've also added some convenience extensions that are not part of the specification, such as JWT compression.

In the documentation that follows, we will clearly identify enhanced functionality that we've added beyond the specification. 

**Note**: In all cases, default usages *are* specification compliant.

## Installation

Use your favorite Maven-compatible build tool to pull the dependency (and its transitive dependencies) from Maven Central:

Maven:

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.6.0</version>
</dependency>
```

Gradle:

```groovy
dependencies {
    compile 'io.jsonwebtoken:jjwt:0.6.0'
}
```

Note: JJWT depends on Jackson 2.x.  If you're already using an older version of Jackson in your app, [read this](#olderJackson)

## Usage

Most complexity is hidden behind a convenient and readable builder-based [fluent interface](http://en.wikipedia.org/wiki/Fluent_interface), great for relying on IDE auto-completion to write code quickly.  Here's an example:

```java
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.impl.crypto.MacProvider;
import java.security.Key;

// We need a signing key, so we'll create one just for this example. Usually
// the key would be read from your application configuration instead.
Key key = MacProvider.generateKey();

String jws = Jwts.builder()
  .setSubject("Joe")
  .signWith(SignatureAlgorithm.HS512, key)
  .compact();
```

How easy was that!?

In this case, we are *building* a JWT that will have the []registered claim]() `sub` (subject) set to `Joe`. We are signing the JWT using the HMAC using SHA-512 algorithm. finally, we are compacting it into its `String` form.

Now let's verify the JWT (you should always discard JWTs that don't match an expected signature):

```java
assert Jwts.parser().setSigningKey(key).parseClaimsJws(jws).getBody().getSubject().equals("Joe");
```

It's subtle, but there are two things going on here. The `key` from before is being used to validate the signature of the JWT. If it fails to verify the JWT, a `SignatureException` is thrown. Assuming the JWT is validated, we parse out the claims and assert that that subject is set to `Joe`.

You have to love one-line code snippets!

But what if signature validation failed?  You can catch `SignatureException` and react accordingly:

```java
try {

    Jwts.parser().setSigningKey(key).parseClaimsJws(compactJwt);

    //OK, we can trust this JWT

} catch (SignatureException e) {

    //don't trust the JWT!
}
```

## Supported Features

### Specification Compliant:

* Creating and parsing plaintext compact JWTs

* Creating, parsing and verifying digitally signed compact JWTs (aka JWSs) with all standard JWS algorithms:
    * HS256: HMAC using SHA-256
    * HS384: HMAC using SHA-384
    * HS512: HMAC using SHA-512
    * RS256: RSASSA-PKCS-v1_5 using SHA-256
    * RS384: RSASSA-PKCS-v1_5 using SHA-384
    * RS512: RSASSA-PKCS-v1_5 using SHA-512
    * PS256: RSASSA-PSS using SHA-256 and MGF1 with SHA-256
    * PS384: RSASSA-PSS using SHA-384 and MGF1 with SHA-384
    * PS512: RSASSA-PSS using SHA-512 and MGF1 with SHA-512
    * ES256: ECDSA using P-256 and SHA-256
    * ES384: ECDSA using P-384 and SHA-384
    * ES512: ECDSA using P-512 and SHA-512
    
### Enhancements Outside the Specification:

* Body compression. If the JWT body is large, you can use a `CompressionCodec` to compress it. Best of all, the JJWT library will automtically decompress and parse the JWT without additional coding.

## Currently Unsupported Features

* [Non-compact](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-31#section-7.2) serialization and parsing.
* JWE (Encryption for JWT)

These feature sets will be implemented in a future release.  Community contributions are welcome!

## Detailed Docs

## Learn More

- [JSON Web Token for Java and Android](https://stormpath.com/blog/jjwt-how-it-works-why/)
- [How to Create and Verify JWTs in Java](https://stormpath.com/blog/jwt-java-create-verify/)
- [Where to Store Your JWTs - Cookies vs HTML5 Web Storage](https://stormpath.com/blog/where-to-store-your-jwts-cookies-vs-html5-web-storage/)
- [Use JWT the Right Way!](https://stormpath.com/blog/jwt-the-right-way/)
- [Token Authentication for Java Applications](https://stormpath.com/blog/token-auth-for-java/)
- [JJWT Changelog](CHANGELOG.md)

<a name="olderJackson"></a>
#### Already using an older Jackson dependency?

JJWT depends on Jackson 2.4.x (or later).  If you are already using a Jackson version in your own application less than 2.x, for example 1.9.x, you will likely see [runtime errors](https://github.com/jwtk/jjwt/issues/1).  To avoid this, you should change your project build configuration to explicitly point to a 2.x version of Jackson.  For example:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.4.2</version>
</dependency>
```

## Author

Maintained by [Stormpath](https://stormpath.com/)

## Licensing

This project is open-source via the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0).