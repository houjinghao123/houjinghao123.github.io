# phjhq's Blog - 个人技术博客

这是一个使用 [Hugo](https://gohugo.io/) 静态网站生成器构建的个人技术博客，部署在 GitHub Pages 上。

## 🚀 在线访问

- 主站: [https://houjinghao123.github.io](https://houjinghao123.github.io)

## 📝 博客内容

博客主要分享以下技术主题：

- **Docker**: Docker 命令、Dockerfile、Docker Compose、容器化部署
- **MySQL**: 数据库优化、索引、事务、锁、存储引擎
- **Java**: Spring Boot、Spring Security、JWT、集合框架
- **Kotlin**: 基础语法、函数式编程
- **数据结构与算法**: 树结构、算法优化
- **其他技术**: 开发工具、配置技巧、学习笔记

## 🛠️ 技术栈

- **静态网站生成器**: [Hugo](https://gohugo.io/) v0.134.1
- **主题**: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- **部署**: GitHub Pages + GitHub Actions
- **样式**: 自定义 CSS 扩展
- **搜索**: 内置搜索功能
- **评论**: 支持评论系统
- **数学公式**: 支持 MathJax 渲染

## 📁 项目结构

```
.
├── .github/workflows/      # GitHub Actions 部署配置
├── archetypes/             # Hugo 内容模板
├── assets/CSS/extended/    # 自定义 CSS 样式
├── content/                # 博客内容
│   ├── posts/             # 博客文章
│   ├── java/              # Java 相关文章
│   └── about.md           # 关于页面
├── layouts/               # Hugo 布局模板
│   ├── _default/         # 默认布局
│   └── partials/         # 部分模板组件
├── static/               # 静态资源
├── hugo.yaml            # Hugo 主配置文件
└── README.md            # 项目说明文档
```

## 🚀 本地运行

### 前提条件

1. 安装 [Hugo](https://gohugo.io/installation/) (版本 0.134.1 或更高)
2. 安装 [Git](https://git-scm.com/)

### 运行步骤

```bash
# 克隆项目
git clone https://github.com/houjinghao123/houjinghao123.github.io.git
cd houjinghao123.github.io

# 启动本地开发服务器
hugo server -D
```

访问 `http://localhost:1313` 查看本地预览。

### 构建静态网站

```bash
# 生成静态文件到 public/ 目录
hugo
```

## 🔄 部署流程

本项目使用 GitHub Actions 自动部署：

1. 推送到 `main` 分支触发构建
2. GitHub Actions 自动安装 Hugo 和依赖
3. 构建静态网站
4. 部署到 GitHub Pages

详细配置见 [.github/workflows/hugo.yaml](.github/workflows/hugo.yaml)

## ✨ 主题特性

基于 PaperMod 主题，支持以下功能：

- 响应式设计
- 亮色/暗色模式
- 文章分类和标签
- 文章搜索
- 代码语法高亮
- 数学公式支持 (MathJax)
- 社交分享链接
- 阅读进度条
- 多语言支持 (当前为中文)

## 📄 许可证

本项目内容遵循 [知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议](http://creativecommons.org/licenses/by-nc-sa/4.0/)。

代码部分遵循 MIT 许可证。

## 🤝 贡献

欢迎通过以下方式贡献：

1. 提交 Issue 报告问题或建议
2. 提交 Pull Request 改进代码或文档
3. 分享技术文章或教程

## 📧 联系

- GitHub: [@houjinghao123](https://github.com/houjinghao123)
- 博客: [phjhq's Blog](https://houjinghao123.github.io/)

---

*最后更新: 2024年*
