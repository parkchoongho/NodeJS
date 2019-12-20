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

### Saving a Document

이제 해당 객체를 데이터베이스에 저장해 보겠습니다. 

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

async function createCourse() {
  const courseNode = new Course({
    name: "react.js",
    author: "Park Choong Ho",
    tags: ["react", "frontend"],
    isPublished: true
  });
  try {
    const result = await courseNode.save();
    console.log(result);
  } catch (error) {
    console.log(error);
  }
}

createCourse();
```

course 객체는 save method를 가지고 있습니다. 이 save는 비동기 동작입니다. 해당 객체를 mongodb에 저장하는데 시간이 걸리기 때문입니다. (좀 더 명확히 얘기하면 파일시스템에 접근하기 때문입니다.) 이 method는 Promise를 return합니다. 해당 Promise를 await을 통해 받으면 실제 Database에 저장된 객체를 받게됩니다.

이렇게 MongoDB에 데이터를 저장하면 MongoDB는 해당 document를 구별할 수 있는 unique한 구별자를 document에 할당합니다.

위 코드를 실행하면 console에 다음과 같이 찍힙니다.

```powershell
[nodemon] restarting due to changes...
[nodemon] starting `node index.js`
(node:10880) DeprecationWarning: current URL string parser is deprecated, and will be removed in a future version. To use the new parser, pass option { useNewUrlParser: true } to MongoClient.connect.
(node:10880) DeprecationWarning: current Server Discovery and Monitoring engine is deprecated, and will be removed in a future version. To use the new Server Discover and Monitoring engine, pass option { useUnifiedTopology: true } to the MongoClient constructor.
MongoDB Connected...
{
  tags: [ 'node', 'backend' ],
  _id: 5dfc5ff22e037d2a80e48f59,
  name: 'node.js',
  author: 'Park Choong Ho',
  isPublished: true,
  date: 2019-12-20T05:45:22.819Z,
  __v: 0
}
```

앞서 말한 unique한 구별자가 _id property입니다.

### Querying Documents

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

async function createCourse() {
  const courseNode = new Course({
    name: "react.js",
    author: "Park Choong Ho",
    tags: ["react", "frontend"],
    isPublished: true
  });
  try {
    const result = await courseNode.save();
    console.log(result);
  } catch (error) {
    console.log(error);
  }
}

async function findCourses() {
  try {
    const courses = await Course.find();
    console.log(courses);
  } catch (error) {
    console.log(error);
  }
}

findCourses();
```

위에 처럼 쿼리를 날린 후, console창을 확인해봅니다.

```powershell
PS C:\Users\User\Desktop\Project\mongo-demo> node index.js
(node:8308) DeprecationWarning: current URL string parser is deprecated, and will be removed in a future version. To use the new parser, pass option { useNewUrlParser: true } to MongoClient.connect.
(node:8308) DeprecationWarning: current Server Discovery and Monitoring engine is deprecated, and will be removed in a future version. To use the new Server Discover and Monitoring engine, pass option { useUnifiedTopology: true } to the MongoClient constructor.
MongoDB Connected...
[
  {
    tags: [ 'node', 'backend' ],
    _id: 5dfc609e7dbe5333d4d8a094,
    name: 'node.js',
    author: 'Park Choong Ho',
    isPublished: true,
    date: 2019-12-20T05:48:14.830Z,
    __v: 0
  },
  {
    tags: [ 'react', 'frontend' ],
    _id: 5dfc612e5393982c347aa251,
    name: 'react.js',
    author: 'Park Choong Ho',
    isPublished: true,
    date: 2019-12-20T05:50:38.199Z,
    __v: 0
  }
]
```

이렇게 결과값이 나타나는 것을 확인할 수 있습니다.

또한 filter를 줌으로써 원하는 데이터만 가져올 수도 있습니다.

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

async function createCourse() {
  const courseNode = new Course({
    name: "react.js",
    author: "Park Choong Ho",
    tags: ["react", "frontend"],
    isPublished: true
  });
  try {
    const result = await courseNode.save();
    console.log(result);
  } catch (error) {
    console.log(error);
  }
}

async function findCourses() {
  try {
    const courses = await Course.find({ name: "node.js" });
    console.log(courses);
  } catch (error) {
    console.log(error);
  }
}

findCourses();
```

이렇게 쿼리를 날리면 name property가 "node.js"인 객체만 배열에 담아서 반환합니다.

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

async function createCourse() {
  const courseNode = new Course({
    name: "react.js",
    author: "Park Choong Ho",
    tags: ["react", "frontend"],
    isPublished: true
  });
  try {
    const result = await courseNode.save();
    console.log(result);
  } catch (error) {
    console.log(error);
  }
}

async function findCourses() {
  try {
    const courses = await Course.find()
      .limit(10)
      .sort({ name: 1 })
      .select({ name: 1, tags: 1 });
    console.log(courses);
  } catch (error) {
    console.log(error);
  }
}

findCourses();
```

```powershell
(node:3984) DeprecationWarning: current URL string parser is deprecated, and will be removed in a future version. To use the new parser, pass option { useNewUrlParser: true } to MongoClient.connect.
(node:3984) DeprecationWarning: current Server Discovery and Monitoring engine is deprecated, and will be removed in a future version. To use the new Server Discover and Monitoring engine, pass option { useUnifiedTopology: true } to the MongoClient constructor.
MongoDB Connected...
[
  {
    tags: [ 'node', 'backend' ],
    _id: 5dfc609e7dbe5333d4d8a094,
    name: 'node.js'
  },
  },
  {
    tags: [ 'react', 'frontend' ],
    _id: 5dfc612e5393982c347aa251,
    name: 'react.js'
  }
]
```

### Comparison Query Operator

Mongoose, MongoDB에 있는 여러가지 Comparison Query Operator을 알아봅시다.

- eq (equal)
- ne (not equal)
- gt (greater than)
- gte (greater than or equal to)
- lt (less than)
- lte (less than or equal to)
- in
- nin (not in)

```javascript
async function findCourses() {
  try {
    const courses = await Course.find({price: {$gt: 10}})
      .limit(10)
      .sort({ name: 1 })
      .select({ name: 1, tags: 1 });
    console.log(courses);
  } catch (error) {
    console.log(error);
  }
}

findCourses();
```

이렇게 쿼리를 작성하면 price property가 10보다 큰 강의들만 가지고 옵니다. ($로 operator임을 표시합니다.)

```javascript
async function findCourses() {
  try {
    const courses = await Course.find({price: {$gt: 10, $lt: 20}})
      .limit(10)
      .sort({ name: 1 })
      .select({ name: 1, tags: 1 });
    console.log(courses);
  } catch (error) {
    console.log(error);
  }
}

findCourses();
```

price property 값이 10초과 20미만인 강의들을 가져옵니다.

```javascript
async function findCourses() {
  try {
    const courses = await Course.find({price: {$in: [10, 20, 30]}})
      .limit(10)
      .sort({ name: 1 })
      .select({ name: 1, tags: 1 });
    console.log(courses);
  } catch (error) {
    console.log(error);
  }
}

findCourses();
```

price property가 10, 20, 30인 강의들을 반환합니다.