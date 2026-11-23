# 都市pa 远程版发布包

这个仓库只放前端和脚本，卡本身仍然是 `导出/都市pa/都市pa.json`。

## 一次性设置

1. 在源项目里设置你的 GitHub 地址：

   ```
   node scripts/set-remote.mjs --user 你的GitHub用户名 --repo 都市pa-web --tag main
   ```

2. 重新构建：

   ```
   pnpm build
   ```

3. 生成发布文件：

   ```
   node scripts/prepare-remote-release.mjs
   ```

   会把下面四个文件复制到 `远程发布`：

   - `statusbar/index.html`
   - `phone/index.html`
   - `phone.js`
   - `statusbar-push.js`

4. 按需编辑 `远程发布/media.json`，把图片/音乐放进 `远程发布/media/图片`、`远程发布/media/音乐`。相对路径按仓库根目录解析：

   ```json
   {
     "images": [{ "title": "夜晚的公寓", "url": "media/图片/night.png" }],
     "music": [{ "title": "City Pop", "url": "media/音乐/city.mp3" }]
   }
   ```

   也可以直接写完整的外链 URL。

5. 在 GitHub 建一个**公开**仓库（例如 `都市pa-web`），把 `远程发布` 里的全部内容推到仓库根目录。jsDelivr 只读公开仓库。

6. 回到源项目，检查并导出卡：

   ```
   pnpm check:card -- "都市pa"
   pnpm bundle:card -- "都市pa"
   ```

7. 把 `导出/都市pa/都市pa.json` 发给朋友导入。状态栏会加载：

   `https://cdn.jsdelivr.net/gh/你的用户名/都市pa-web@main/statusbar/index.html`

   小手机和状态栏推送脚本会从同一个仓库 `import`。

## 更新

改完代码后：

1. `set-remote` 里的 `--tag` 改成新版本（例如 `v2`），或者保持 `main`。
2. `pnpm build`
3. `node scripts/prepare-remote-release.mjs`
4. 推送到 GitHub。
5. 重新导出卡；如果 URL 里的 tag 变了，朋友要重新导入卡，或者刷新让酒馆重新加载脚本。

## 注意

- 卡里的 `状态栏推送` 和 `小手机` 现在是远程 `import`，不要再把它们内联回 `index.yaml`；只有 `开局设定`、`变量结构`、`时间推进`、`动向生成` 还是内联的。
- 本地服务不再是必需的；状态栏页面在 CDN 加载失败时会退回 `http://localhost:6630/index.html`，那是给本地调试用的。
- 媒体全部走网络链接；本地的 `src/都市pa/媒体` 文件夹不再参与卡内逻辑。
- 公开仓库不要放 API Key、酒馆配置、聊天记录和私人图片。
