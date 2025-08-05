# tyhzxh的个人博客

这是使用Hugo静态站点生成器和PaperMod主题构建的个人博客。

## 🚀 特性

- ⚡ 基于Hugo的快速静态站点生成
- 🎨 使用PaperMod主题，简洁美观
- 📱 响应式设计，支持移动端
- 🌙 支持深色/浅色模式切换
- 🔍 内置搜索功能
- 📊 显示阅读时间
- 🏷️ 支持标签和分类
- 📝 支持Markdown编写
- 🚀 自动部署到GitHub Pages

## 🛠️ 技术栈

- **静态站点生成器**: [Hugo](https://gohugo.io/)
- **主题**: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- **部署**: GitHub Pages + GitHub Actions
- **内容格式**: Markdown

## 📁 项目结构

```
├── .github/
│   └── workflows/
│       └── hugo.yml          # GitHub Actions工作流
├── archetypes/
│   └── default.md           # 文章模板
├── content/
│   ├── posts/               # 博客文章
│   ├── about.md            # 关于页面
│   ├── archives.md         # 归档页面
│   └── search.md           # 搜索页面
├── static/                  # 静态文件
├── themes/
│   └── PaperMod/           # 主题文件
├── hugo.toml               # Hugo配置文件
└── README.md
```

## 🚀 本地开发

### 环境要求

- Hugo Extended版本 (>= 0.112.0)
- Git

### 克隆项目

```bash
git clone https://github.com/tyhzxh/tyhzxh.github.io.git
cd tyhzxh.github.io
git submodule update --init --recursive
```

### 本地运行

```bash
# 启动开发服务器
hugo server -D

# 或者不包含草稿
hugo server
```

访问 `http://localhost:1313` 查看博客。

### 创建新文章

```bash
hugo new content posts/your-post-name.md
```

## 📝 写作指南

### 文章Front Matter

```yaml
---
title: "文章标题"
date: 2024-01-01T10:00:00+08:00
draft: false
tags: ["标签1", "标签2"]
categories: ["分类"]
description: "文章描述"
cover:
    image: "图片路径"
    alt: "图片描述"
    caption: "图片标题"
---
```

### 图片使用

将图片放在 `static/images/` 目录下，然后在文章中引用：

```markdown
![图片描述](/images/your-image.jpg)
```

## 🚀 部署

本博客使用GitHub Actions自动部署到GitHub Pages。每次推送到main分支时会自动触发部署。

### 手动部署

```bash
# 构建站点
hugo --minify

# 生成的文件在public/目录下
```

## 📄 许可证

本项目采用 [MIT 许可证](LICENSE)。

## 🤝 贡献

欢迎提交Issue和Pull Request！

## 📞 联系方式

- GitHub: [@tyhzxh](https://github.com/tyhzxh)
- Email: your-email@example.com

---

⭐ 如果这个项目对你有帮助，请给它一个星标！