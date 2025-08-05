# 图片上传指南

## 📸 如何上传你的头像和背景壁纸

### 1. 头像上传

**步骤：**
1. 准备你的头像图片（推荐正方形，200x200px或更高分辨率）
2. 将图片重命名为 `avatar.jpg`、`avatar.png` 或 `avatar.webp`
3. 将文件放入 `static/images/` 文件夹
4. 更新 `hugo.toml` 配置文件中的头像路径：

```toml
[params.homeInfoParams]
  imageUrl = "/images/avatar.jpg"  # 改为你的文件名
  imageWidth = 120
  imageHeight = 120
  imageTitle = "你的名字"
```

### 2. 背景壁纸上传

**步骤：**
1. 准备你的背景图片（推荐横向，1920x1080px或更高分辨率）
2. 将图片重命名为 `background.jpg`、`background.png` 或 `background.webp`
3. 将文件放入 `static/images/` 文件夹
4. 更新 `assets/css/extended/custom.css` 文件中的背景路径：

```css
.main {
    background-image: url('/images/background.jpg');  /* 改为你的文件名 */
    background-size: cover;
    background-position: center;
    background-attachment: fixed;
    background-repeat: no-repeat;
}
```

### 3. 网站图标上传

**步骤：**
1. 准备你的图标文件（推荐32x32px的PNG或SVG格式）
2. 将文件重命名为 `favicon.png` 或 `favicon.svg`
3. 将文件放入 `static/` 文件夹根目录
4. 更新 `hugo.toml` 配置文件：

```toml
[params]
  favicon = "/favicon.png"  # 改为你的文件名
```

### 4. 支持的图片格式

- **JPG/JPEG**: 适合照片，文件较小
- **PNG**: 支持透明背景，适合图标
- **WebP**: 现代格式，文件更小，质量更好
- **SVG**: 矢量格式，无损缩放，适合图标和简单图形

### 5. 图片优化建议

**头像：**
- 尺寸：200x200px 到 400x400px
- 格式：PNG（如需透明背景）或 JPG
- 文件大小：< 100KB

**背景壁纸：**
- 尺寸：1920x1080px 或更高
- 格式：JPG 或 WebP
- 文件大小：< 500KB（建议压缩）

**网站图标：**
- 尺寸：32x32px 或 64x64px
- 格式：PNG 或 SVG
- 文件大小：< 10KB

### 6. 在线图片压缩工具

- [TinyPNG](https://tinypng.com/) - PNG/JPG压缩
- [Squoosh](https://squoosh.app/) - 多格式压缩
- [SVGOMG](https://jakearchibald.github.io/svgomg/) - SVG优化

### 7. 预览更改

上传图片后，运行以下命令预览更改：

```bash
cd a:\tyhzxh-blog
hugo server
```

然后在浏览器中访问 `http://localhost:1313` 查看效果。

### 8. 提交更改

确认效果满意后，提交到Git：

```bash
git add .
git commit -m "更新头像和背景图片"
git push
```

---

**注意：** 如果你使用的是默认的SVG图片，可以直接替换 `static/images/` 文件夹中的文件，无需修改配置。