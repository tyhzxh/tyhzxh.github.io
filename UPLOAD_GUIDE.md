# 博客图片上传和配置指南

本指南将帮助你上传和配置个人头像、背景壁纸和网站图标，以及自定义背景颜色。

## 📁 文件位置

所有图片文件应放置在 `static/images/` 目录下：

```
static/
└── images/
    ├── avatar.jpg      # 个人头像
    ├── background.jpg  # 背景壁纸 (可选)
    └── favicon.ico     # 网站图标
```

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

### 2. 背景壁纸和颜色配置

#### 选项1：使用自定义壁纸图片

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

#### 选项2：使用纯色背景

如果你不想使用壁纸，可以设置纯色背景：

```css
.main {
    /* 移除或注释掉 background-image */
    /* background-image: url('/images/background.jpg'); */
    
    /* 设置纯色背景 */
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    /* 或者使用单色 */
    /* background-color: #f0f0f0; */
}
```

#### 选项3：使用渐变背景

```css
.main {
    /* 移除背景图片 */
    /* background-image: url('/images/background.jpg'); */
    
    /* 设置渐变背景 */
    background: linear-gradient(135deg, 
        #ff9a9e 0%, 
        #fecfef 50%, 
        #fecfef 100%);
    
    /* 或者其他渐变效果 */
    /* background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1); */
}
```

#### 常用渐变色方案

```css
/* 蓝紫渐变 */
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);

/* 粉色渐变 */
background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 100%);

/* 绿色渐变 */
background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);

/* 橙色渐变 */
background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);

/* 深色渐变 */
background: linear-gradient(135deg, #2c3e50 0%, #34495e 100%);
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

## 🔧 高级配置

### 页面布局调整

当前配置已优化页面布局，内容占用85%的屏幕宽度。如需调整，可修改 `assets/css/extended/custom.css`：

```css
/* 调整内容宽度 */
.main {
    max-width: 90% !important;  /* 可改为 70%, 80%, 90% 等 */
    width: 90% !important;
}
```

### 背景透明度调整

调整内容区域的背景透明度：

```css
.post-content, .post-header, .home-info {
    background: rgba(var(--theme-rgb), 0.95); /* 0.95 = 95%不透明度 */
    backdrop-filter: blur(15px); /* 模糊效果强度 */
}
```

### 修改配置文件

如果需要调整头像显示效果，可以编辑 `hugo.toml`：

```toml
[params.homeInfoParams]
imageUrl = "/images/avatar.jpg"
imageWidth = 150
imageHeight = 150
imageTitle = "我的头像"
```

---

**注意：** 如果你使用的是默认的SVG图片，可以直接替换 `static/images/` 文件夹中的文件，无需修改配置。