```javascript
fetch('https://tpc.googlesyndication.com/simgad/14553437832346711238')
  .then(result => result.blob())
  .then(blob => {
    if (typeof globalThis !== undefined && globalThis.URL) {
      const image = new Image();
      image.src = globalThis.URL.createObjectURL(blob);
      globalThis.document.body.appendChild(image);
      return image;
    }
    return blob;
  })
```
```javascript
fetch('https://tpc.googlesyndication.com/simgad/14553437832346711238')
  .then(result => result.arrayBuffer())
  .then(arraybuffer => {
      const data = new Uint8Array(arraybuffer);
      if (typeof globalThis !== undefined && globalThis.btoa) {
          const baseStr = globalThis.btoa(String.fromCharCode.apply(null, data));
          const image = new Image();
          image.src = `data:image/jpeg;base64,${baseStr}`;
          globalThis.document.body.appendChild(image);
          
          return image;
      }
      return data;
    
  })
```
[link]([https://stackoverflow.com/questions/23013871/how-to-parse-into-base64-string-the-binary-image-from-response](https://stackoverflow.com/questions/23013871/how-to-parse-into-base64-string-the-binary-image-from-response))
