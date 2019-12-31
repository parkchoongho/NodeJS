# Authentication & Authorization

### Introduction

**Authentication**이란 사용자가 자기가 누구인지를 주장하는 것을 판별하는 절차입니다. 유저가 email과 password를 서버로 전송해 로그인을 시도하면, 서버는 해당 데이터를 활용해 유저를 **authenticate**합니다. 

**Authorization**이란 해당 유저가 특정 동작을 행하는데 있어 필요한 권한을 결정하는 절차입니다. 

### Creating the User Model

```javascript
const mongoose = require("mongoose");
const Joi = require("@hapi/joi");

const {
  Schema,
  model
} = mongoose;

const UserSchema = new Schema({
  name: {
    type: String,
    required: true,
    minlength: 5,
    maxlength: 30
  },

  email: {
    type: String,
    required: true,
    minlength: 5,
    maxlength: 255,
    unique: true
  },

  password: {
    type: String,
    required: true,
    minlength: 5,
    maxlength: 1024
  },

  avatarUrl: String,

  comments: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: "Comment"
  }],

  videos: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: "Video"
  }]
});

const User = model("User", UserSchema);

const validateUser = user => {
  const schema = {
    name: Joi.string()
      .alphanum()
      .min(5)
      .max(30)
      .required(),
    password: Joi.string()
      .alphanum()
      .lowercase().min(5).max(20),
    email: Joi.string().min(5).max(255).required().email()
  };
  return Joi.validate(user, schema);
};

module.exports = {
  User,
  validateUser
};
```

### JSON Web Tokens

**Json Web Token**은 사용자를 구별하는 긴 문자열입니다. 클라이언트에서 서버에 로그인 요청을 하여 로그인에 성공을 하면 서버는 JWT을 발행해 클라이언트에게 전달합니다. JWT는 크게 Header, Payload, Verify Signature 이렇게 3가지로 구성됩니다.

Encoded

```javascript
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

.으로 Header, Payload, Verify Signature 파트를 구분합니다.

Decoded

Header

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Payload

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

Verify Signature

```json
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  
your-256-bit-secret

) secret base64 encoded
```

