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

