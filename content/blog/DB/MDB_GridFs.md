---
title: "[MongoDB] GridFS"
date: 2021-07-18 16:21:13
category: 'DB'
draft: false
---

<br>

> MongoDB의 [GridFS문서](https://docs.mongodb.com/manual/core/gridfs/)를 참고하여 작성하였습니다.

MongoDB에서는 대용량의 파일을 저장하기 위해서 GridFS를 사용합니다.  
GridFS는 큰 바이너리 파일을 `chunck`라 불리는 여러 작은 파일들로 나누어 MongoDB에 저장합니다. 
<div align=center>
<img src = "https://user-images.githubusercontent.com/28651727/126057249-65135276-4224-4985-8a9a-c5e988315ab7.png">
<p>GridFS Structure</p>
</div>

## When to Use Grid FS
다음의 몇가지 상황에서 DB에 데이터를 저장하는것이 System-level의 파일시스템에 데이터를 저장하는것 보다 유리합니다.
- 파일시스템의 디렉토리내 파일개수제한에 걸릴 경우
- 전체 파일을 메모리에 로드할 필요 없이 대용량 파일의 일부정보에만 액세스 하려는 경우 
- 파일및 메타데이터를 여러 시스템과 기능에 걸쳐 자동으로 동기화 및 배포되도록 유지하려는 경우

## Example
GridFS를 사용하여 이미지파일을 MongoDB에 저장해보았습니다(flask활용)

다음과 같이 인덱스파일과 서버파일을 작성하고 **server.py**를 실행합니다.  
**flaskr/server.py**
```python
from bson import ObjectId
from flask import Flask, render_template, request, Response
from gridfs import GridFS
from pymongo import MongoClient

app = Flask(__name__)
client = MongoClient('localhost', 27017)
db = client.media # 데이터베이스 이름 : media
gfs = GridFS(db)

@app.route("/")
def render_file() :
    return render_template('index.html')

@app.route('/upload', methods=['POST'])
def upload():
    file = request.files.get("file")
    file_name = file.filename
    data = file.read()
    content_type = file.content_type
    insertimg = gfs.put(data, content_type=content_type, filename=file_name)
    return str(insertimg)

if __name__ == '__main__':
    app.run(debug=True)
```

**flaskr/templates/index.html**
```html
<html>
    <body>
        <form action = "http://localhost:5000/upload" method = "POST"
            enctype="multipart/form-data">
            <input type = "file" name = "file" />
            <input type = "submit"/>
        </form>
    </body>
</html>
```

<div align=center><img src="https://user-images.githubusercontent.com/28651727/126057433-d7dbf0f2-1088-499f-b3ec-331408bf1cd6.png"></div>

이 페이지를 통해 이미지를 업로드 하면 다음과 같이 MongoDB에 하나의 파일이 3개의 Document로 나뉘어 Put되어있는것을 확인할 수 있습니다.

<div align=center><img src="https://user-images.githubusercontent.com/28651727/126057522-d8d0fabf-6b70-41d2-8fd3-cfe6dc545dc2.png"></div>
