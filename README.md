## esc_utils

小票打印机 *ESC* 数据转换工具，主要用于将 image 图像转换成小票打印机可识别的字节数组。

内部图像处理使用 `image: ^3.0.2` 。

### 使用示例

#### 1. 获取图片数据转 Uint8List
```dart
// 通过文件路径获取图片
static Future<Uint8List> getImage(String imgPath) async {
    final imgFile = File(imgPath);
    final exist = await imgFile.exists();
    if(exist) {
      return imgFile.readAsBytes();
    } else {
      throw Exception('print imgFile is not exist');
    }
  }
```
#### 2. Unit8List 转 esc 字节数组
```dart
import 'package:image/image.dart' as img;

// unit8List 转 esc 字节数组
static Future<List<int>> decodeBytes(Uint8List imgData) async {
   final img.Image image = await cropImage(
      imgData,
    );
    Generator generator = Generator();
    List<int> bytes = [];
    // 打印机状态重置
    bytes += generator.reset();
    // 打印光栅位图
    bytes += generator.imageRaster(image);
    // 走纸 2mm
    bytes += generator.feed(2);
    // 切刀
    bytes += generator.cut();
    return bytes;
  }
```

### 注意事项

1. 通常不会把一整张图片直接发给打印机，那样太大。都会分割成小图片逐个发送。
   比如数据太大导致打印机内存溢出输出乱码。

2. 小票机，目前常用尺寸为 80mm、58mm。通常 1mm 等于 8个像素，因此，适合小票打印机打印的图片像素尺寸应为：558px、372px。（因为宽度适配在实际打印中有偏差，不宜设置为完全吻合）

3. 打印的图片宽度像素太大会造成打印无反应。
