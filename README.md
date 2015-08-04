#### Current test status

[![Build Status](https://travis-ci.io/github.com/sec51/twofactor/status.png)](https://travis-ci.io/github.com/sec51/twofactor/status.png)

## `totp`

This package implements the RFC 6238 OATH-TOTP algorithm; 
See also the [godocs](https://godoc.org/github.com/sec51/twofactor/)
for this package.

### Installation

```go get github.com/sec51/twofactor```

### Features

* Built-in support for secure crypto keys generation

* Built-in back-off time when a user fails to authenticate more than 3 times

* Bult-in serialization and deserialization to store the one time token struct in a persistence layer

* Automatic re-synchronization with the client device

* Built-in generation of a PNG QR Code for adding easily the secret key on the user device

* Supports 6, 7, 8 digits tokens

* Supports HMAC-SHA1, HMAC-SHA256, HMAC-SHA512


### Storing Keys

> **The key crerated is using go crypto random function and it's a cryptographic secret key.**
> It needs to be protected against unauthorized access and they cannot be leaked.
> In addition when shared with the client, the connection should be secured.

The `totp` struct can be easily serialized using the `ToBytes()` function. 
The bytes can then be stored on a persistent layer. Again the secret key needs to be protected.
You can then retrieve the object back with the function: `TOTPFromBytes`

Again if you trannsfer those bytes via a network connection, this should be a secured one.

The struct needs to be stored in a persistent layer becase its values, like last token verification time, 
max user authentication failures, etc.. needs to be preserved.
The secret key needs to be preserved too, between the user accound and the user device.
The secret key is used to derive tokens.
Once more the secret key needs to be safely stored.

### Upcoming features

* Securely store the secret keys in the persistent layer and allow secure transfer on the network

* Integration with Twilio for sending the token via SMS, in case the user loses its entry in the Google authenticator app.


### Example Usages

#### Case 1: Google Authenticator

* How to use the library

1- Import the library

```
import github.com/sec51/twofactor
```

2- Instanciate the `totp` object via:

```
	otp, err := twofactor.NewTOTP("info@sec51.com", "Sec51", crypto.SHA1, 8)	
	if err != nil {
		return err
	}
```

3- Display the PNG QR code to the user and an input text field, so that he can insert the token generated from his device

```
	qrBytes, err := otp.QR()
	if err != nil {
		return err
	}
```

4- Verify the user provided token, coming from the google authenticator app

```
	err := otp.Validate(USER_PROVIDED_TOKEN)
	if err != nil {
		return err
	}
	// if there is an error, then the authentication failed
	// if it succeeded, then store this information and do not display the QR code ever again.
```

5- All following authentications should display only a input field with no QR code.


### References

* [RFC 6238 - *TOTP: Time-Based One-Time Password Algorithm*](https://tools.ietf.org/rfc/rfc6238.txt)

* The [Key URI Format](https://code.google.com/p/google-authenticator/wiki/KeyUriFormat)


### Author

`totp` was written by Sec51 <info@sec51.com>.


### License

```
Copyright (c) 2015 Sec51.com <info@sec51.com>

Permission to use, copy, modify, and distribute this software for any
purpose with or without fee is hereby granted, provided that the above 
copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE. 
```