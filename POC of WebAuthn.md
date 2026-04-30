# POC of WebAuthn

The Web Authentication API (also known as WebAuthn) is a [specification](https://w3c.github.io/webauthn/) written by the [W3C](https://www.w3.org/) and [FIDO](https://fidoalliance.org/), with the participation of Google, Mozilla, Microsoft, Yubico, and others. The API allows servers to register and authenticate users using public key cryptography instead of a password.

## Registration flow

![](/Users/zhenyuan.h/Library/Application%20Support/marktext/images/2024-01-31-14-16-55-image.png)

[Online FlowChart &amp; Diagrams Editor - Mermaid Live Editor](https://mermaid.live/edit#pako:eNqVU8tOwzAQ_BVrTyDaktShaXOoVIGQOCChAheUi3G2iUViFz8Eoeq_46SFUGgR-LLW7s7MPuwVcJUhJGDw2aHkeCFYrlmVSuLPvUHdn05PGktmOUp7dF5oVeFxQuYNwFiiMRfGamaFkh1qN7vhmGNZC5mTG6Zt3cF5wcoSZY4b7E5W_5D2-S5ov-DM2cK7BGdWaY_RyCwSrjFrvKwkL8IW3_V3QJ_6XbnMx5UWb9_a3aPXFvWRjmajdqccL8jVxekl4-jtb6I_mu4qZzIjRuSSWafxH0PvKI6W7rEUnDxhfbyP7m97mH_ZPTGOczTmYDlfh7kfBj2oUFdMZP5BrhpPCn4yFaaQ-GuGC-ZKm0Iq1z7Vz1bd1pJDYrXDHrhl5he8fb-QLFhpvHfJJCQreIXkLB7QmAYRDcYTGgWTuAc1JP1oEg-GdEzPJnQY0zgcr3vwppRnCAejYRD6OA1HdBREcdTSPbTBLT1mwu_sevOH2q-0fgcFMBvt)

## Login flow

![](/Users/zhenyuan.h/Library/Application%20Support/marktext/images/2024-01-31-14-21-14-image.png)

[Online FlowChart &amp; Diagrams Editor - Mermaid Live Editor](https://mermaid.live/edit#pako:eNqVU9FqwjAU_ZVwnzZWXTXVah8EUQaDDYbbXkZfQnptA23ikpStiv--VOdKO5QtLwm559xzcpLsgKsEIQKD7yVKjkvBUs2KWBI3Xg3q3mx2U89knqK0V4tMqwKvI7KqCcaSXKVCNvA2rCavMK-ETMkT07ZqeDxjeY4yxSO3heqdE12cSITJhHCNiSsLlhORXLIwL21WAzmzSjcWjEgls6VG8iFs1nXUIv04atjM1ZUWW2aFku28OnoHUyc4mqPaiyp5Ru6Xt3eMo5sviXZjeD4Z_0fuHc7f8n6oL5eYknM05qxYk80vPHhQoC6YSNwb29U7MbgTFhhD5JYJrlmZ2xhiuXdQl5F6riSHyOoSPSg3CbOnJwnRmuXG7W6YhGgHnxCNwj4NqR9QfzKlgT8NPagg6gXTsD-kEzqa0mFIw8Fk78FWKddh0B8P_YGr08GYjv0gDA7t3g7F7_aYCJf94_FbHH7H_gsXZQpZ)

## Code Sample

html

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>WebAuthn Demo</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>
</head>

<body>

    Username:
    <br>
    <input type="text" name="username" id="email" placeholder="i.e. foo@bar.com">
    <br>
    <br>
    <button onclick="registerUser()">Register</button>
    <button onclick="loginUser()">Login</button>

    <script>
        const url = "https://localhost"
        $(document).ready(function () {
            // check whether current browser supports WebAuthn
            if (!window.PublicKeyCredential) {
                alert("Error: this browser does not support WebAuthn");
                return;
            }
        });

        function bufferDecode(value) {
            return Uint8Array.from(atob(value.replace(/_/g, '/').replace(/-/g, '+')), c => c.charCodeAt(0));
        }

        function bufferEncode(value) {
            return btoa(String.fromCharCode.apply(null, new Uint8Array(value)))
                .replace(/\+/g, "-")
                .replace(/\//g, "_")
                .replace(/=/g, "");;
        }

        function registerUser() {

            username = $("#email").val()
            if (username === "") {
                alert("please enter a username");
                return;
            }

            $.get(
                url + '/register/begin/' + username,
                null,
                function (data) {
                    return data
                },
                'json')
                .then((credentialCreationOptions) => {
                    credentialCreationOptions.publicKey.challenge = bufferDecode(credentialCreationOptions.publicKey.challenge);
                    credentialCreationOptions.publicKey.user.id = bufferDecode(credentialCreationOptions.publicKey.user.id);
                    return navigator.credentials.create({
                        publicKey: credentialCreationOptions.publicKey
                    })
                })
                .then((credential) => {
                    let attestationObject = credential.response.attestationObject;
                    let clientDataJSON = credential.response.clientDataJSON;
                    let rawId = credential.rawId;
                    jQuery.ajax({
                        url: url + '/register/finish',
                        type: "POST",
                        data: JSON.stringify({
                            name: username,
                            id: credential.id,
                            rawId: bufferEncode(rawId),
                            type: credential.type,
                            response: {
                                attestationObject: bufferEncode(attestationObject),
                                clientDataJSON: bufferEncode(clientDataJSON),
                            },
                        }),
                        dataType: "json",
                        contentType: "application/json; charset=utf-8",
                        success: function (data) {
                            return data
                        }
                    })
                })
                .then((success) => {
                    alert("successfully registered " + username + "!")
                    return
                })
                .catch((error) => {
                    console.log(error)
                    alert("failed to register " + username)
                })
        }
        function loginUser() {

            username = $("#email").val()
            if (username === "") {
                alert("please enter a username");
                return;
            }

            $.get(
                url + '/login/begin/' + username,
                null,
                function (data) {
                    return data
                },
                'json')
                .then((data) => {
                    const credentialRequestOptions = data.credentialRequestOptions
                    credentialRequestOptions.publicKey.challenge = bufferDecode(credentialRequestOptions.publicKey.challenge);
                    credentialRequestOptions.publicKey.allowCredentials.forEach(function (listItem) {
                        listItem.id = bufferDecode(listItem.id)
                    });

                    return navigator.credentials.get({
                        publicKey: credentialRequestOptions.publicKey
                    })
                })
                .then((assertion) => {

                    let authData = assertion.response.authenticatorData;
                    let clientDataJSON = assertion.response.clientDataJSON;
                    let rawId = assertion.rawId;
                    let sig = assertion.response.signature;
                    let userHandle = assertion.response.userHandle;
                    jQuery.ajax({
                        url: url + '/login/finish',
                        type: "POST",
                        data: JSON.stringify({
                            name: username,
                            id: assertion.id,
                            rawId: bufferEncode(rawId),
                            type: assertion.type,
                            response: {
                                authenticatorData: bufferEncode(authData),
                                clientDataJSON: bufferEncode(clientDataJSON),
                                signature: bufferEncode(sig),
                                userHandle: bufferEncode(userHandle),
                            },
                        }),
                        dataType: "json",
                        contentType: "application/json; charset=utf-8",
                        success: function (data) {
                            return data
                        }
                    })
                })
                .then((success) => {
                    alert("successfully logged in " + username + "!")
                    return
                })
                .catch((error) => {
                    console.log(error)
                    alert("failed to logged " + username)
                })
        }
    </script>
</body>

</html>
```

biz.go

```
package biz

import (
    v1 "auth/api/auth/v1"
    "auth/internal/data"
    "auth/internal/dto"
    "bytes"
    "context"
    "encoding/json"
    "net/http"

    "github.com/go-kratos/kratos/v2/log"
    "github.com/go-webauthn/webauthn/protocol"
    "github.com/go-webauthn/webauthn/webauthn"
    "github.com/samber/lo"
)

// AuthUsecase is a auth usecase.
type AuthUsecase struct {
    user     data.UserRepo
    webauthn *webauthn.WebAuthn
    store    data.StoreRepo
    log      *log.Helper
}

// NewAuthUsecase new a Auth usecase.
func NewAuthUsecase(user data.UserRepo, authn *webauthn.WebAuthn, store data.StoreRepo, logger log.Logger) *AuthUsecase {
    return &AuthUsecase{user: user, webauthn: authn, log: log.NewHelper(logger), store: store}
}

func (a *AuthUsecase) LoginBigin(ctx context.Context, req *v1.LoginBeginRequest) (*v1.LoginBeginReply, error) {
    user, err := a.user.GetUser(ctx, req.Name)
    if err != nil {
        return nil, err
    }
    if user.ID == "" {
        return nil, v1.ErrorUserNotFound("user not found")
    }
    options, sessionData, err := a.webauthn.BeginLogin(user)
    if err != nil {
        return nil, err
    }
    a.store.Save(sessionData, data.LoginSession)
    return &v1.LoginBeginReply{
        CredentialRequestOptions: &v1.LoginBeginReply_Credentialrequestoptions{
            PublicKey: &v1.LoginBeginReply_Publickey{
                Challenge: options.Response.Challenge.String(),
                Timeout:   uint32(options.Response.Timeout),
                RpId:      options.Response.RelyingPartyID,
                AllowCredentials: lo.Map(options.Response.AllowedCredentials, func(p protocol.CredentialDescriptor, _ int) *v1.LoginBeginReply_Allowcredentials {
                    return &v1.LoginBeginReply_Allowcredentials{
                        Type: string(p.Type),
                        Id:   p.CredentialID.String(),
                    }
                }),
            },
        },
    }, nil
}

func (a *AuthUsecase) LoginFinish(ctx context.Context, req *v1.LoginFinishRequest) (*v1.LoginFinishReply, error) {
    user, err := a.user.GetUser(ctx, req.Name)
    if err != nil {
        return nil, err
    }
    if user.ID == "" {
        return nil, v1.ErrorUserNotFound("user not found")
    }
    sessionData := a.store.Get(user.ID, data.LoginSession)
    data, err := json.Marshal(req)
    if err != nil {
        return nil, err
    }
    request, err := http.NewRequest("POST", a.webauthn.Config.RPID, bytes.NewBuffer(data))
    if err != nil {
        return nil, err
    }
    if _, err := a.webauthn.FinishLogin(user, *sessionData, request); err != nil {
        a.log.Error(err)
        return nil, err
    }
    return &v1.LoginFinishReply{}, nil
}

func (a *AuthUsecase) RegisterBigin(ctx context.Context, req *v1.RegisterBiginRequest) (*v1.RegisterBiginReply, error) {
    user, err := a.user.GetUser(ctx, req.Name)
    if err != nil {
        return nil, err
    }
    if user.ID == "" {
        user = dto.NewUser(req.Name, req.Name)
    }
    options, sessionData, err := a.webauthn.BeginRegistration(user)
    if err != nil {
        return nil, err
    }
    if err = a.user.SaveUser(ctx, user); err != nil {
        return nil, err
    }
    a.store.Save(sessionData, data.RegisterSession)
    return &v1.RegisterBiginReply{
        PublicKey: &v1.PublicKey{
            Challenge: options.Response.Challenge.String(),
            Rp: &v1.PublicKey_RP{
                Name: options.Response.RelyingParty.Name,
                Id:   options.Response.RelyingParty.ID,
            },
            User: &v1.PublicKey_User{
                Name:        options.Response.User.Name,
                DisplayName: options.Response.User.DisplayName,
                Id:          options.Response.User.ID.(protocol.URLEncodedBase64).String(),
            },
            PubKeyCredParams: lo.Map(options.Response.Parameters, func(p protocol.CredentialParameter, _ int) *v1.PublicKey_PubKeyCredentialParam {
                return &v1.PublicKey_PubKeyCredentialParam{
                    Type: string(p.Type),
                    Alg:  int32(p.Algorithm),
                }
            }),
            AuthenticatorSelection: &v1.PublicKey_AuthenticatorSelection{
                UserVerification: string(options.Response.AuthenticatorSelection.UserVerification),
            },
            Timeout:     int32(options.Response.Timeout),
            Attestation: string(options.Response.Attestation),
        },
    }, nil
}

func (a *AuthUsecase) RegisterFinish(ctx context.Context, req *v1.RegisterFinishRequest) (*v1.RegisterFinishReply, error) {
    user, err := a.user.GetUser(ctx, req.Name)
    if err != nil {
        return nil, err
    }
    if user.ID == "" {
        return nil, v1.ErrorUserNotFound("user not found")
    }
    sessionData := a.store.Get(user.ID, data.RegisterSession)
    data, err := json.Marshal(req)
    if err != nil {
        return nil, err
    }

    request, err := http.NewRequest("POST", a.webauthn.Config.RPID, bytes.NewBuffer(data))
    if err != nil {
        return nil, err
    }
    credential, err := a.webauthn.FinishRegistration(user, *sessionData, request)
    if err != nil {
        return nil, err
    }
    user = user.AddCredential(*credential)
    a.user.SaveUser(ctx, user)
    return &v1.RegisterFinishReply{
        Message: "success",
    }, nil
}
```

user.go

```
package dto

import (
    "github.com/go-webauthn/webauthn/webauthn"
    "github.com/google/uuid"
)

type User struct {
    ID          string
    Name        string
    DisplayName string
    Credentials []webauthn.Credential
}

// NewUser creates and returns a new User
func NewUser(name string, displayName string) User {

    user := User{}
    user.ID = uuid.New().String()
    user.Name = name
    user.DisplayName = displayName

    return user
}

// WebAuthnID returns the user's ID
func (u User) WebAuthnID() []byte {
    return []byte(u.ID)
}

// WebAuthnName returns the user's username
func (u User) WebAuthnName() string {
    return u.Name
}

// WebAuthnDisplayName returns the user's display name
func (u User) WebAuthnDisplayName() string {
    return u.DisplayName
}

// WebAuthnIcon is not (yet) implemented
func (u User) WebAuthnIcon() string {
    return ""
}

// AddCredential associates the credential to the user
func (u User) AddCredential(cerd webauthn.Credential) User {
    u.Credentials = append(u.Credentials, cerd)
    return u
}

// WebAuthnCredentials returns credentials owned by the user
func (u User) WebAuthnCredentials() []webauthn.Credential {
    return u.Credentials
}
```

proto

```
syntax = "proto3";

package auth.v1;

import "google/api/annotations.proto";

option go_package = "auth/api/auth/v1;v1";
option java_multiple_files = true;
option java_package = "dev.kratos.api.auth.v1";

service Auth {
  rpc RegisterBigin(RegisterBiginRequest) returns (RegisterBiginReply) {
    option (google.api.http) = {
      get : "/register/begin/{name}"
    };
  }

  rpc RegisterFinish(RegisterFinishRequest) returns (RegisterFinishReply) {
    option (google.api.http) = {
      post : "/register/finish"
      body : "*"
    };
  }

  rpc LoginBigin(LoginBeginRequest) returns (LoginBeginReply) {
    option (google.api.http) = {
      get : "/login/begin/{name}"
    };
  }

  rpc LoginFinish(LoginFinishRequest) returns (LoginFinishReply) {
    option (google.api.http) = {
      post : "/login/finish"
      body : "*"
    };
  }
}

message RegisterBiginRequest { string name = 1; }

message RegisterBiginReply { PublicKey publicKey = 1; }

message RegisterFinishRequest {
  message Response {
    string attestationObject = 1;
    string clientDataJSON = 2;
  }

  string id = 1;
  string rawId = 2;
  string type = 3;
  Response response = 4;
  string name = 5;
}

message RegisterFinishReply { string message = 1; }

message LoginBeginRequest { string name = 1; }

message LoginBeginReply {
  message Allowcredentials {
    string type = 1;
    string id = 2;
  }

  message Publickey {
    string challenge = 1;
    uint32 timeout = 2;
    string rpId = 3;
    repeated Allowcredentials allowCredentials = 4;
  }

  message Credentialrequestoptions { Publickey publicKey = 1; }

  Credentialrequestoptions credentialRequestOptions = 1;
}

message LoginFinishRequest {
  message Response {
    string authenticatorData = 1;
    string clientDataJSON = 2;
    string signature = 3;
    string userHandle = 4;
  }

  string id = 1;
  string rawId = 2;
  string type = 3;
  Response response = 4;
  string name = 5;
}

message LoginFinishReply {}

message PublicKey {
  string challenge = 1;

  message RP {
    string name = 1;
    string id = 2;
  }

  RP rp = 2;

  message User {
    string name = 1;
    string displayName = 2;
    string id = 3;
  }

  User user = 3;

  message PubKeyCredentialParam {
    string type = 1;
    int32 alg = 2;
  }

  repeated PubKeyCredentialParam pubKeyCredParams = 4;

  message AuthenticatorSelection { string userVerification = 1; }

  AuthenticatorSelection authenticatorSelection = 5;

  int32 timeout = 6;
  string attestation = 7;
}
```
