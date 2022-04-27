WIP readme

Credit to @maslick for his react component and base:

# =koder=
QR/bar code scanner for the Browser
More info: https://github.com/maslick/koder

## ⚡ Usage

### Install dependencies
```shell
npm install --global yarn
yarn install --frozen-lockfile
```

### Run React app
```shell
npm run start
open https://locahost:8080
```

### Productionize
```shell
npm run build                # -> build React app into ./public
npm run prod                 # -> serve static web app on port 8082
open http://localhost:8082
```

## ⚡ NPM module
```
npm install @maslick/koder
```

```javascript
// CommonJS
const Koder = require('@maslick/koder');

// ES6 modules
import Koder from '@maslick/koder';
```

```javascript
const Koder = require('@maslick/koder');
const {loadImage, createCanvas} = require("canvas");

const getImageData = async (src) => {
  const img = await loadImage(src);
  const canvas = createCanvas(img.width, img.height);
  const ctx = canvas.getContext('2d');
  ctx.drawImage(img, 0, 0);
  return {
    data: ctx.getImageData(0, 0, img.width, img.height).data,
    width: img.width,
    height: img.height
  };
};

(async () => {
  const url = 'https://raw.githubusercontent.com/maslick/koder/master/screenshots/app_1.png';
  const koder = await new Koder().initialized;
  const {data, width, height} = await getImageData(url);

  const t0 = new Date().getTime();
  const res = koder.decode(data, width, height);
  const t1 = new Date().getTime();

  console.log(`Scanned in ${t1-t0} ms`);  // Scanned in 7 ms
  console.log(res);                       // http://en.m.wikipedia.org
})();
```

## :spades: Development

### Fetch or build the Builder image
```shell
docker pull maslick/emscripten-zbar-sdk
docker build -t maslick/emscripten-zbar-sdk -f docker/Dockerfile docker
```

### Build WASM artifacts
```shell
# React app
docker run \
  -e INPUT_FILE=zbar/qr.cpp \
  -e OUTPUT_FILE=zbar \
  -e OUTPUT_DIR=public/wasm \
  -v $(pwd):/app \
  maslick/emscripten-zbar-sdk make -B
```

### Clean the build artifacts (if necessary):
```shell
# React app
OUTPUT_DIR=public/wasm OUTPUT_FILE=zbar make clean
```


## 🔭 References
* [WebAssembly at Ebay](https://tech.ebayinc.com/engineering/webassembly-at-ebay-a-real-world-use-case/)
* [Barcode Scanner WebAssembly](https://barkeywolf.consulting/posts/barcode-scanner-webassembly/)
* [zbar.wasm](https://github.com/samsam2310/zbar.wasm)
