# 袁海尧作品集 · 部署与使用指南

这套网站是「**静态网页 + Git 后台**」：作品数据存在 `data/works.json`，图片存在 `images/`，朋友通过一个浏览器后台（Sveltia CMS）上传图、选分类、点保存，改动会自动提交到 GitHub，网站随即自动更新。**托管全程免费**，只有域名每年约 $10。

> 分工提醒：下面「一次性部署」偏技术，由你（或帮他的人）照着做**一次**即可；做完后，朋友日常只用最后那个「傻瓜流程」，不碰任何代码。

---

## 一、文件结构

```
portfolio/
├── index.html          ← 网站本体（不用改）
├── data/
│   ├── works.json      ← 作品列表（后台会自动改它）
│   └── site.json       ← 首页大图设置（后台会自动改它）
├── images/
│   ├── hero.jpg        ← 首页大图
│   └── works/          ← 以后上传的作品图都进这里
└── admin/
    ├── index.html      ← 后台入口（朋友访问 /admin）
    └── config.yml      ← 后台配置（部署前改这里两行）
```

> ⚠️ 现在 `works.json` 里的图，仍指向他旧站 Squarespace 的图床。建议尽早把原图下载下来，通过后台重新上传一遍（见第五步），别长期依赖旧图床。

---

## 二、一次性部署（约 1 小时）

### 第 1 步：GitHub（放代码和内容）
1. 注册 github.com 账号（朋友自己注册，因为后台要用他的账号登录）。
2. 新建一个仓库（Repository），名字随意，例如 `portfolio`，建为 **Public**。
3. 把本项目所有文件原样上传进去（网页上「Add file → Upload files」拖进去即可）。

### 第 2 步：Cloudflare Pages（免费托管）
1. 注册 cloudflare.com，进入 **Workers & Pages → 创建 → Pages → 连接到 Git**，选刚才的仓库。
2. 构建设置：**框架预设选「None / 无」，构建命令留空，输出目录填 `/`（根目录）**——因为这是纯静态站，不需要构建。
3. 部署完成后会得到一个网址 `xxx.pages.dev`，先用它测试网站能正常打开、作品能显示。

### 第 3 步：后台登录方式（二选一）
> 背景：以前最流行的 Decap + Netlify 登录服务**今年已停用**，所以这里用 Sveltia CMS。它支持两种登录：

**方式 A · 一键「用 GitHub 登录」（推荐，朋友长期最省心）**
1. 打开 `github.com/sveltia/sveltia-cms-auth`，按它 README 的「Deploy to Cloudflare」一键部署那个小程序（Worker），得到一个 `https://sveltia-cms-auth.你的子域.workers.dev` 网址。
2. 在 GitHub 注册一个 OAuth App（README 里有详细步骤），把上面的 Worker 网址填进去，拿到 Client ID / Secret，填回 Worker 的环境变量。
3. 打开 `admin/config.yml`，把 `repo:` 改成你的「用户名/仓库名」，把 `base_url:` 改成你的 Worker 网址。提交。
4. 之后访问 `/admin` 就是一个「用 GitHub 登录」按钮，点一下即可。

**方式 B · 个人令牌登录（最快，省去上面 Worker）**
1. 把 `admin/config.yml` 里的 `base_url:` 那一整行删掉，`repo:` 照填。
2. 朋友在 GitHub「Settings → Developer settings → Personal access tokens」生成一个有该仓库写权限的令牌，登录后台时粘贴进去即可。
3. 缺点：这种令牌**默认约 90 天过期**，到期要重新生成一个。图省事可先用 B，以后想更顺手再换 A。

### 第 4 步：绑定 haiyaoyuan.com
1. 在 Cloudflare Pages 项目里「Custom domains（自定义域名）」添加 `haiyaoyuan.com`，按提示改 DNS。
2. 因为域名现在很可能挂在 Squarespace（它收购了 Google Domains）：
   - 要么把**域名转出**到便宜的注册商（Cloudflare Registrar / Porkbun，约 $10/年）再指过来；
   - 要么先在原处只改 DNS 解析指向 Cloudflare。
   - 关键：**确认域名不会跟着 Squarespace 订阅一起被退掉/失效**，再去退订阅省钱。

### 第 5 步（建议）：把图换成自己托管
登录后台 → 作品列表 → 逐条把每张作品的「图片」重新上传一次（用从旧站下载的原图）。全部换完后就完全不依赖 Squarespace 了。

---

## 三、朋友的日常流程（傻瓜版）

**加一张新作品：**
1. 浏览器打开 `haiyaoyuan.com/admin`，登录。
2. 进「作品列表」→ 点「添加一张作品」。
3. 上传图片 → 选分类（角色/场景/插画/速涂）→（可选）填编号、要不要设为重点横幅。
4. 点「保存 / 发布」。约 1 分钟后网站自动更新。

**换首页大图：** 进「网站设置 → 首页大图」，上传新图，保存即可。

> 横幅那个老问题已经从布局上解决了：等高排版下，横图自动占宽、竖图缩窄，完成度高的横幅自然显眼。想让某张横幅更突出，编辑那张时把「设为重点横幅」打开。

---

## 四、注意事项

- **不要直接双击 `index.html` 本地打开**——浏览器会拦截读取 `data/works.json`，作品会显示不出来。要预览就看线上地址，或在文件夹里跑一个本地服务器（装了 Python 的话：终端进入文件夹执行 `python -m http.server`，再访问 `http://localhost:8000`）。
- 改了分类名字时，`index.html` 顶部的 `CATS` 和 `admin/config.yml` 里的 options 要**同步改**，否则分类对不上。
- 整套真正花钱的只有域名（约 $10/年）；退掉 Squarespace 订阅后每年还能省下一两千块的订阅费。
