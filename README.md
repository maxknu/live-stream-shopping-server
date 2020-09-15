# 📺 Live stream RTMP server

### Live stream using RTMP for React Native App Live Stream

Client: https://github.com/maxknu/live-stream-shopping-client

## Demo 

**Note: Here is demo**
- NOTE: the product listing is not implemented

| Streamer                                                                                                                  | Viewer                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| <img src="https://raw.githubusercontent.com/sieuhuflit/react-native-live-stream-rtmp-example/master/demo/streamer.gif" /> | <img src="https://raw.githubusercontent.com/sieuhuflit/react-native-live-stream-rtmp-example/master/demo/viewer.gif" /> |

## Feature

- ✅ Live Stream with input username account
- ✅ The video can replay
- ✅ Live update status when `Pending`, `On Live`, and `Finish` live streaming process
- ✅ Streamer and viewer can chat and send heart when livestream

## Teachnology using

- Using node-media-server

## Prerequisite

- Install NodeJS (https://nodejs.org)
- Install ffmpeg (https://www.ffmpeg.org/download.html). If you are using MacOS just type _brew install ffmpeg_
- Use MongoDB Atlas https://www.mongodb.com/cloud/atlas and create a free account (You can also install local MongoDB www.mongodb.com)

## Get Started

- grab your MongoDB connection link from MongoDB Atlas
- create .env file in root folder
- add two .env variables here is an example

```
MongoCloudDB=mongodb+srv://USERNAME:PASSWORD@cluster0-kv8at.gcp.mongodb.net/test?retryWrites=true&w=majority
Port=3333
```

run yarn to install packages and start the nodejs server
```
yarn install
node server.js
```

## Local MongoDB
Skip this step if you use https://www.mongodb.com/cloud/atlas. Only if you want to install MongoDB locally. Then start MongoDB. Then type the following to terminal. 

```
# mongo
```

Then switch to admin database

```
> use admin
```

Then create user admin

```
db.createUser({
  user: 'admin',
  pwd: '123456',
  roles: [
    { role: 'userAdminAnyDatabase', db: 'admin' },
    { role: 'dbAdminAnyDatabase', db: 'admin' },
    { role: 'readWriteAnyDatabase', db: 'admin' }
  ]
})
```


## Want to Replay video

`Concept`: After live stream finish, the mp4 file will generate and save to folder `media/*` folder.
We need to do this step to get exact mp4 path information and save it to MongoDB.

Open this file `node_modules/node_media_server/node_trans_session.js`. Then import this to the top

```js
const context = require('./node_core_ctx');
```

Then add this

```js
context.nodeEvent.emit('getFilePath', this.conf.streamPath, ouPath, mp4FileName);
```

Under this line

```js
Logger.log('[Transmuxing MP4] ' + this.conf.streamPath + ' to ' + ouPath + '/' + mp4FileName);
```

The result look similar like this

```js

// ...
// ===> ADD THIS LINE
const context = require("./node_core_ctx");
// ...

class NodeTransSession extends EventEmitter {
  constructor(conf) {
    super();
    this.conf = conf;
  }

  run() {
    // ...
    // Rest of stuff
    // ...
    if (this.conf.mp4) {
      this.conf.mp4Flags = this.conf.mp4Flags ? this.conf.mp4Flags : '';
      let now = new Date();
      let mp4FileName = dateFormat('yyyy-mm-dd-HH-MM') + '.mp4';
      let mapMp4 = `${this.conf.mp4Flags}${ouPath}/${mp4FileName}|`;
      mapStr += mapMp4;
      Logger.log('[Transmuxing MP4] ' + this.conf.streamPath + ' to ' + ouPath + '/' + mp4FileName);

      // ===> ADD THIS LINE
      context.nodeEvent.emit("getFilePath", this.conf.streamPath, ouPath, mp4FileName);
    }

    //...
  }
```

## Common Problem

`1/ I can't replay the video ?`

Sometimes the video can't replay, you need to wait a little bit wait the saving mp4 file process finish, then you can replay the video.

## License

[MIT](https://choosealicense.com/licenses/mit/)
