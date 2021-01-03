### 设置宽高

+ html设置

  `  <canvas class="canvas" width="300" height="300"></canvas>` 

+ js设置

  ```javascript
   let canvas = document.querySelector('.canvas')
      let context = canvas.getContext('2d')
      let cx = canvas.width = 300;
      let cy = canvas.height = 300;
  ```

+ css设置

  ```css
  .canvas{
          background: #000;
          width: 400px;
          height: 400px;
  }
  ```

区别和联系

html设置是直接给canvas设置宽高，js也是改变这个canvas属性的宽高值。如果js会让html都不设置，canvas对象会默认`300 * 150`。

而css是修改canvas这个DOM对象。like二向箔。所以会使DOM平铺。导致动画变形。故不建议css修改canvas属性。