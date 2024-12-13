# Goldfinger [![Build Status](https://app.bitrise.io/app/bc0cdf2da387a5c3/status.svg?token=eHOSr1ZB1HzNnKZfxYjxbA&branch=master)](https://bintray.com/infinum/android/goldfinger/_latestVersion)

<img src='./logo.svg' width='264'/>

## Description

Android library to simplify Biometric authentication implementation.

## Table of contents

* [Requirements](#requirements)
* [Getting started](#getting-started)
* [Usage](#usage)
* [Rx module](#rx-module)
* [Configuration](#configuration)
* [Known issues](#known-issues)
* [Contributing](#contributing)
* [License](#license)
* [Credits](#credits)

## Requirements

The project requires a minimum API version 14.

#### Important

This version is compatible with `androidx.biometric`. If you do not want to use `androidx.biometric`, feel free to use [older version of Goldfinger](https://github.com/infinum/Android-Goldfinger/tree/v1.2.1).

## Getting started

#### Add mavenCentral

To include Goldfinger in your project, you have to add buildscript dependencies in your project level `build.gradle`:

```groovy
buildscript {
    repositories {
        mavenCentral()
    }
}
```

#### Add dependency

```gradle
implementation 'com.infinum:goldfinger:2.2.0'
```

## Usage

#### Initialize

```java
Goldfinger.Builder(context).build()
```

#### Check prerequisites

```java
if (goldfinger.canAuthenticate(@AuthenticatorTypes int authenticators)) {
  /* Authenticate */
}
```

#### Build params

PromptParams are directly linked to [BiometricPrompt.PromptInfo](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.PromptInfo.Builder.html) so be sure to read which parameters are required.

```java
Goldfinger.PromptParams params = new Goldfinger.PromptParams.Builder(activity)
  .title("Title")
  .negativeButtonText("Cancel")
  .description("Description")
  .subtitle("Subtitle")
  .build();
```

I also suggest looking at [ValidateUtils](./core/src/main/java/co/infinum/goldfinger/ValidateUtils.java) class to understand what is allowed by the Biometric library.

#### Authenticate

```java
goldfinger.authenticate(params, new Goldfinger.Callback() {
    @Override
    public void onError(@NonNull Exception e) {
        /* Critical error happened */
    }

    @Override
    public void onResult(@NonNull Goldfinger.Result result) {
        /* Result received */
    }
});
```

You can see all Goldfinger methods [here](./core/src/main/java/co/infinum/goldfinger/Goldfinger.java).

## Rx module

Goldfinger has separate Rx module in case you want to use reactive approach.

#### Add dependencies

```gradle
implementation 'com.infinum:goldfinger:2.2.0'
implementation 'com.infinum:goldfinger-rx:2.2.0'
```

#### Initialize

```java
RxGoldfinger.Builder(context).build()
```

#### Authenticate

```java
goldfinger.authenticate(params).subscribe(new DisposableObserver<Goldfinger.Result>() {

  @Override
  public void onComplete() {
    /* Biometrics authentication is finished */
  }

  @Override
  public void onError(Throwable e) {
    /* Critical error happened */
  }

  @Override
  public void onNext(Goldfinger.Result result) {
    /* Result received */
  }
});
```

You can see all RxGoldfinger methods [here](./rx/src/main/java/co/infinum/goldfinger/rx/RxGoldfinger.java).

## Configuration

If you don’t like default implementations, you can easily modify them using `Goldfinger.Builder` object.

```java
Goldfinger.Builder(context)
  .logEnabled(true)
  .cipherFactory(factory)
  .cipherCrypter(crypter)
  .build()
```

**Important**: Both Factory and Crypter should use same object type. If MacFactory is used, be sure to also provide MacCrypter!

#### Factory (Cipher, Mac, Signature)

Factory class should be used to create `Cipher`/`Mac`/`Signature` which will be used internally when `CryptoObject` is created.

```java
new CipherFactory() {
  @Nullable
  @Override
  public Cipher createEncryptionCrypter(String key) {
     /* Create Cipher for encryption */
  }

  @Nullable
  @Override
  public Cipher createDecryptionCrypter(String key) {
    /* Create Cipher for decryption */
  }
};
```

Goldfinger will default to [AesCipherFactory](./core/src/main/java/co/infinum/goldfinger/crypto/impl/AesCipherFactory.java) if other implementation is not provided.

#### Crypter (Cipher, Mac, Signature)

Crypter class should be used to encrypt/decrypt data with `Cipher`/`Mac`/`Signature` which was created with `Factory` above.

```java
new CipherCrypter() {
  @Nullable
  @Override
  public String encrypt(@NonNull Cipher crypter, @NonNull String value) {
    /* Encrypt data with given crypter */
  }

  @Nullable
  @Override
  public String decrypt(@NonNull Cipher crypter, @NonNull String value) {
    /* Decrypt data with given crypter */
  }
};
```

Goldfinger will default to [Base64CipherCrypter](./core/src/main/java/co/infinum/goldfinger/crypto/impl/Base64CipherCrypter.java) if other implementation is not provided.

#### Logging

Logging is **off** by default. You can enable it by calling `Goldfinger.Builder(context).logEnabled(true)`.

## Known issues

- Android Oreo does not throw `KeyPermanentlyInvalidatedException` - [Link](https://issuetracker.google.com/issues/65578763)

## Contributing

We believe that the community can help us improve and build better a product.
Please refer to our [contributing guide](CONTRIBUTING.md) to learn about the types of contributions we accept and the process for submitting them.

To ensure that our community remains respectful and professional, we defined a [code of conduct](CODE_OF_CONDUCT.md) <!-- and [coding standards](<link>) --> that we expect all contributors to follow.

We appreciate your interest and look forward to your contributions.

## License

```
Copyright 2018 Infinum

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

## Credits

Maintained and sponsored by [Infinum](https://infinum.com).

<p align="center">
  <a href='https://infinum.com'>
    <picture>
        <source srcset="https://assets.infinum.com/brand/logo/static/white.svg" media="(prefers-color-scheme: dark)">
        <img src="https://assets.infinum.com/brand/logo/static/default.svg">
    </picture>
  </a>
</p>
