# NPM

### Semantic Versioning

```json
{
  "name": "npm-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "mongoose": "^5.8.0",
    "underscore": "^1.9.1"
  }
}
```

위 파일에서 보면 dependencies에서 mongoose에 숫자가 적혀있는 것을 볼 수 있습니다. 이를 **semantic versioning** 또는 **samver**라고 합니다. 각 숫자들은 각자가 의미하는 바가 있습니다.

맨앞에 숫자는 Major Version, 가운데는 Minor Version, 가장 오른쪽에 있는 숫자는 Patch Version입니다. Patch Version은 버그가 발생했을 시 이를 수정한 것이고 Minor Version은 기존 Application에 영향을 미치지 않는 기능 추가, Major Version은 기존 Applicatoin에 영향을 미치는 변경을 의미합니다. 

`^` 가 의미하는 것은 Major Version 숫자가 맞으면 된다는 의미입니다. `~` 기호도 있습니다. 이 기호가 의미하는 것은 Major Version 뿐만 아니라 Minor Version까지 신경쓴다는 뜻입니다.