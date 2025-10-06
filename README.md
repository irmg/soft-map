# soft-map

软件导航网站，使用Vue 3和Vite构建。

## 部署到GitHub Pages

本项目已配置为可以轻松部署到GitHub Pages。

### 自动部署（推荐）

1. 将代码推送到GitHub仓库的master分支
2. GitHub Actions会自动构建并部署网站
3. 部署完成后可以通过 `https://<your-username>.github.io/soft-map/` 访问

### 手动部署

如果需要手动部署，可以按照以下步骤操作：

1. 安装依赖：
```
npm install
```

2. 构建项目：
```
npm run build
```

3. 部署到GitHub Pages：
```
npm run deploy
```

## 本地开发

```
npm install
npm run dev
```

## 预览构建结果

```
npm run build
npm run preview
```