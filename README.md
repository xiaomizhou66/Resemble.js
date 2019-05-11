<h1 align="center"><img src="https://raw.github.com/rsmbl/Resemble.js/master/demoassets/resemble.png" alt="Resemble.js" width="256"/></h1>

<p align="center">
    <a href="https://travis-ci.org/rsmbl/Resemble.js"><img alt="Build Status" src="https://travis-ci.org/rsmbl/Resemble.js.svg?branch=master" /></a>
    <a href="https://www.codacy.com/app/jamescryer/Resemble.js?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=rsmbl/Resemble.js&amp;utm_campaign=Badge_Grade"><img alt="Code Health" src="https://api.codacy.com/project/badge/Grade/1e0972581406417e9914bc58f57704b3" /></a>
    <a href="https://www.codacy.com/app/jamescryer/Resemble.js?utm_source=github.com&utm_medium=referral&utm_content=rsmbl/Resemble.js&utm_campaign=Badge_Coverage"><img alt="Coverage" src="https://api.codacy.com/project/badge/Coverage/9223d8d37c99428c8c06b889470327a5" /></a>
    <a href="https://opensource.org/licenses/MIT"><img alt="Build Status" src="https://img.shields.io/badge/License-MIT-yellow.svg" /></a>
    <a href="https://www.npmjs.com/package/resemblejs"><img alt="NPM Downloads" src="https://img.shields.io/npm/dm/resemblejs.svg" /></a>
</p>

<p align="center">
  Analyse and compare images with Javascript and HTML5. <a href="http://rsmbl.github.io/Resemble.js/">More info & Resemble.js Demo</a>. Compatible with Node.js >8.
</p>

<hr />

### Get it 安装

`npm install resemblejs`

`bower install resemblejs`

### Example 例子

检索图像的基本分析：

```javascript
var api = resemble(fileData).onComplete(function(data) {
    console.log(data);
    /*
	{
	  red: 255,
	  green: 255,
	  blue: 255,
	  brightness: 255
	}
	*/
});
```

使用 resemble 比较两个图像：

```javascript
var diff = resemble(file)
    .compareTo(file2)
    .ignoreColors()
    .onComplete(function(data) {
        console.log(data);
        /*
	{
	  misMatchPercentage : 100, // %
	  isSameDimensions: true, // or false
	  dimensionDifference: { width: 0, height: -1 }, // defined if dimensions are not the same
	  getImageDataUrl: function(){}
	}
	*/
    });
```

将第二个图像缩放到第一个图像的尺寸：

```javascript
//diff.useOriginalSize();
diff.scaleToSameSize();
```

您还可以在第一次分析后更改比较方法：

```javascript
// diff.ignoreNothing();
// diff.ignoreColors();
// diff.ignoreAlpha();
diff.ignoreAntialiasing();
```

并更改输出显示样式：

```javascript
resemble.outputSettings({
    errorColor: {
        red: 255,
        green: 0,
        blue: 255
    },
    errorType: "movement",
    transparency: 0.3,
    largeImageThreshold: 1200,
    useCrossOrigin: false,
    outputDiff: true
});
// .repaint();
```

通过指定从左上角以像素为单位测量的边界框，可以缩小比较区域：

```javascript
const box = {
    left: 100,
    top: 200,
    right: 200,
    bottom: 600
};
resemble.outputSettings({ boundingBox: box });
```

```javascript
resemble.outputSettings({ boundingBoxes: [box1, box2] });
```

您还可以通过从左上角指定排除区域（以像素为单位）来从比较中排除部分图像：

```javascript
const box = {
    left: 100,
    top: 200,
    right: 200,
    bottom: 600
};
resemble.outputSettings({ ignoredBox: box });
```

```javascript
resemble.outputSettings({ ignoredBoxes: [box1, box2] });
```

默认情况下，当图像宽度或高度大于1200像素时，比较算法会跳过像素。 这可以缓解性能问题。

您可以通过将`largeImageThreshold`选项设置为其他值来修改此行为。 将其设置为** 0 **以完全关闭它。

Resemble.js还支持数据URI作为字符串：

```javascript
resemble.outputSettings({ useCrossOrigin: false });
var diff = resemble("data:image/jpeg;base64,/9j/4AAQSkZJRgAB...").compareTo(
    "data:image/jpeg;base64,/9j/,/9j/4AAQSkZJRg..."
);
```

`useCrossOrigin` is true by default, you might need to set it to false if you're using [Data URIs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs).

如果你想像早期那样的旧方式使用 resemble：

```javascript
resemble(img1)
    .compareTo(img2)
    .setReturnEarlyThreshold(8) // %
    .onComplete(data => {
        /* do something */
    });
```

### Single callback api

resemble.compare API提供了一个方便的功能，使用如下：

```js
const compare = require("resemblejs").compare;

function getDiff() {
    const options = {
          //停止比较一旦确定> 5％不匹配; 这将
         //也启用仅比较模式，不会渲染输出图像;
         //这些组合可以显着提高批处理速度
        returnEarlyThreshold: 5
    };

     //参数可以是Node Buffers
     //数据与通常使用额外的getBuffer（）函数相同
    compare(image1, image2, options, function(err, data) {
        if (err) {
            console.log("An error!");
        } else {
            console.log(data);
            /*
            {
            misMatchPercentage : 100, // %
            isSameDimensions: true, // or false
            dimensionDifference: { width: 0, height: -1 }, //定义维度是否不相同
            getImageDataUrl: function(){}
            }
            */
        }
    });
}
```

### Node.js

#### Usage

Node下的API与`resemble.compare`上的API相同，但基于promise：

```js
const compareImages = require("resemblejs/compareImages");
const fs = require("mz/fs");

async function getDiff() {
    const options = {
        output: {
            errorColor: {
                red: 255,
                green: 0,
                blue: 255
            },
            errorType: "movement",
            transparency: 0.3,
            largeImageThreshold: 1200,
            useCrossOrigin: false,
            outputDiff: true
        },
        scaleToSameSize: true,
        ignore: "antialiasing"
    };

    //参数可以是Node Buffers
     //数据与通常使用额外的getBuffer（）函数相同
    const data = await compareImages(
        await fs.readFile("./your-image-path/People.jpg"),
        await fs.readFile("./your-image-path/People2.jpg"),
        options
    );

    await fs.writeFile("./output.png", data.getBuffer());
}

getDiff();
```

#### Tests

要在Node上运行测试（使用Jest），请键入：

```bash
npm run test
```

还有一些浏览器内测试。 要运行这些安装并从项目的根目录运行http服务器，如[http-server](https://github.com/indexzero/http-server)。 然后在浏览器中，导航到 `localhost:8080/chai-tests/test.html，打开开发人员控制台以查看结果。

#### Dockerfile

为方便起见，我添加了一个简单的Dockerfile来在Ubuntu容器中运行NodeJS测试

```bash
docker build -t rsmbl/resemble .
docker run rsmbl/resemble
```

#### Reference to academic papers 参考学术论文

正如人们过去所问的那样，Resemble.js并没有故意实施任何已发表的想法。 使用Canvas API时，RGBA颜色比较简单明了。 抗混叠算法是在[Huddle](https://github.com/HuddleEng) 中使用PhantomCSS测试的各种假阳性结果进行了几天的反复试验。

---

Created by [James Cryer](http://github.com/jamescryer) and the [Huddle development team](https://github.com/HuddleEng).
