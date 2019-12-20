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

### Models

클래스의 instance가 객체가 되듯이 실제 document의 class에 해당하는 model을 만들기 위해 schema를 활용합니다. (해당 Schema를 model안에 compile합니다.)

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

const Course = mongoose.model("Course", courseSchema);

const courseNode = new Course({
  name: "node.js",
  author: "Park Choong Ho",
  tags: ["node", "backend"],
  isPublished: true
});
```

mongoose object에는 model이라는 method가 존재하고 2개의 arguments를 받습니다. 하나는 이 모델을 활용한 collection의 이름, 다른 하나는 해당 collection안 document의 shape을 정해놓은 schema입니다. 그러면 이에 해당하는 Class를 얻게됩니다. 따라서 해당 변수는 맨 앞을 대문자로 작성합니다. 그리고 비로소 해당 Class로 객체를 생성할 수 있습니다. 해당 객체는 MongoDB의 document와 맵핑됩니다.

실제 객체를 생성하는 것에서 RDBMS와의 차이를 확인할 수 있습니다. RDBMS에서는 각각의 column이 하나의 값만을 가지는 것을 원칙으로 합니다. 따라서 RDBMS에서 위 table을 모델링한다고 하면 총 3개의 table이 필요하게 될 것입니다. 하지만 MongoDB는 그럴 필요없이 여러가지 값이나 객체를 쉽게 데이터 베이스에 저장할 수 있습니다. 