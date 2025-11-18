---
title: "摄影作品"
date: 2024-11-18T10:00:00Z
draft: false
summary: "我的摄影作品集"
---

# 摄影作品集

这里收录了我的一些摄影作品，记录生活中的美好瞬间。

## 风景摄影

{{< gallery >}}
<img src="gallery/landscape01.jpg" class="grid-w50 md:grid-w33" />
<img src="gallery/landscape02.jpg" class="grid-w50 md:grid-w33" />
<img src="gallery/landscape03.jpg" class="grid-w50 md:grid-w33" />
<img src="gallery/landscape04.jpg" class="grid-w50 md:grid-w33" />
<img src="gallery/landscape05.jpg" class="grid-w33" />
<img src="gallery/landscape06.jpg" class="grid-w33" />
{{< /gallery >}}

## 人像摄影

{{< gallery >}}
<img src="gallery/portrait01.jpg" class="grid-w50 md:grid-w33 lg:grid-w25" />
<img src="gallery/portrait02.jpg" class="grid-w50 md:grid-w33 lg:grid-w25" />
<img src="gallery/portrait03.jpg" class="grid-w50 md:grid-w33 lg:grid-w25" />
<img src="gallery/portrait04.jpg" class="grid-w50 md:grid-w33 lg:grid-w25" />
{{< /gallery >}}

## 街拍摄影

{{< gallery >}}
<img src="gallery/street01.jpg" class="grid-w66" />
<img src="gallery/street02.jpg" class="grid-w33" />
<img src="gallery/street03.jpg" class="grid-w50" />
<img src="gallery/street04.jpg" class="grid-w50" />
{{< /gallery >}}

## 建筑摄影

{{< gallery >}}
<img src="gallery/architecture01.jpg" class="grid-w100" />
<img src="gallery/architecture02.jpg" class="grid-w50" />
<img src="gallery/architecture03.jpg" class="grid-w50" />
{{< /gallery >}}

---

### 如何添加你的摄影作品

1. 在 `static/gallery/` 目录下创建你的图片文件夹
2. 将图片文件放入相应文件夹
3. 在上面的gallery shortcode中添加图片引用
4. 使用 `grid-wXX` 类来控制图片宽度（10%-100%，5%递增）
5. 可以使用响应式类如 `md:grid-w33 lg:grid-w25` 来适配不同屏幕

### 图片宽度参考

- `grid-w100`: 100%宽度，适合全景图
- `grid-w66`: 66%宽度，适合横向构图
- `grid-w50`: 50%宽度，标准尺寸
- `grid-w33`: 33%宽度，三列布局
- `grid-w25`: 25%宽度，四列布局