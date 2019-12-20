# CRUD Operations Using Mongoose

### Introducing MongoDB

MongoDB is a document or NoSQL database. MongoDB는 기존의 관계형 데이터 베이스인 SQL server와는 다른 특징을 가지고 있습니다. 우리는 JSON 형태의 객체를 MongoDB에 저장하게 됩니다.

Mongoose는 MongoDB로 작업할 수 있게 우리에게 간단한 api를 제공합니다.

### Schema

MongoDB안에 collection에 있는 documents의 모양을 결정하는데 있어 Schema를 사용합니다. Schema는 엄밀히 말하면 MongoDB가 아닌 Mongoose에서 제공하는 개념입니다. RDBMS의 table이 collection과 매칭되며 document가 해당 table의 row와 매칭됩니다. Schema를 사용함으로써 document가 어떠한 property를 가지게 할지를 결정할 수 있습니다.

```javascript
const mongoose = require("mongoose");

mongoose
  .connect("mongodb://localhost/playground")
  .then(() => console.log("MongoDB Connected..."))
  .catch(err => console.log("Could Not Connect MongoDB ", err));

const courseSchema = new mongoose.Schema({
  name: String,
  author: String,
  tags: [String],
  date: { type: Date, default: Date.now },
  isPublished: Boolean
});
```

mongoose.connect는 Promise이기에 then과 catch로 Logic을 설정합니다. 그 다음 courseSchema를 생성합니다. mongoose.connect.Schema는 Class입니다. mongoose Schema 타입에는 총 7개가 있습니다.

- String
- Number
- Date
- Buffer (Binary Data 저장)
- Boolean
- ObjectID
- Array

