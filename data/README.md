# 数据更新说明

## 方案：GitHub Pages 托管数据

### 1. 创建 GitHub 仓库

1. 登录 GitHub，创建新仓库（如 `maplestory-data`）
2. 仓库设置为 **Public**
3. 开启 GitHub Pages：
   - Settings → Pages → Source → Deploy from a branch
   - 选择 `main` 分支，`/ (root)` 目录
   - 点击 Save

### 2. 上传数据文件

将 `data/` 目录下的文件上传到仓库根目录：

```
data/
├── latest.json          # 最新数据（每天更新这个文件）
└── daily/
    ├── 2026-05-09.json  # 历史数据（按日期归档）
    └── 2026-05-10.json
```

### 3. 配置小程序

修改 `utils/remoteDataService.js` 中的地址：

```javascript
const DATA_BASE_URL = 'https://你的用户名.github.io/仓库名/data';
```

例如：
```javascript
const DATA_BASE_URL = 'https://zhangsan.github.io/maplestory-data/data';
```

### 4. 每天更新数据

#### 方式一：直接在 GitHub 网页编辑

1. 打开 GitHub 仓库
2. 点击 `data/latest.json`
3. 点击右上角 ✏️ 编辑按钮
4. 修改金价数据
5. 点击 "Commit changes" 保存

#### 方式二：本地编辑后上传

1. 克隆仓库到本地
2. 修改 `data/latest.json` 文件
3. 复制一份到 `data/daily/YYYY-MM-DD.json`
4. 提交并推送：

```bash
git add .
git commit -m "更新 2026-05-10 数据"
git push
```

### 5. 数据文件格式

#### latest.json / daily/YYYY-MM-DD.json

```json
{
  "date": "2026-05-09",
  "updateTime": "09:45",
  "source": "菠菜maplestory碎碎念",
  "goldPrices": [
    { "server": "一区路西德", "price": 0.87, "change": 0.02, "isChallenger": false },
    { "server": "一区威尔", "price": 0.65, "change": 0.03, "isChallenger": false },
    ...
  ],
  "accountPrices": [
    { "server": "一区路西德", "account8000": 800, "account8000Change": 10, "power1e": 1688, "power1eChange": 0 },
    ...
  ]
}
```

### 6. 字段说明

| 字段 | 说明 |
|------|------|
| `date` | 数据日期，格式 `YYYY-MM-DD` |
| `updateTime` | 更新时间 |
| `source` | 数据来源 |
| `goldPrices` | 金价数组 |
| `goldPrices[].server` | 服务器名称 |
| `goldPrices[].price` | 当前金价（元/亿） |
| `goldPrices[].change` | 涨跌金额 |
| `goldPrices[].isChallenger` | 是否为挑战者服务器 |
| `accountPrices` | 账号价格数组 |
| `accountPrices[].account8000` | 8000联盟账号价格 |
| `accountPrices[].account8000Change` | 账号涨跌 |
| `accountPrices[].power1e` | 1亿战力账号价格 |
| `accountPrices[].power1eChange` | 战力号涨跌 |

### 7. 小程序数据获取流程

```
1. 打开小程序
2. 尝试从 GitHub Pages 获取 latest.json
3. 获取成功 → 保存到本地缓存 → 显示数据
4. 获取失败 → 使用本地缓存（2小时内有效）
5. 无缓存 → 使用内置本地数据
```

### 8. 注意事项

- GitHub Pages 有缓存，修改后可能需要 5-10 分钟生效
- 小程序会自动缓存数据 2 小时，减少网络请求
- 如果 GitHub Pages 访问慢，可以考虑使用 Gitee Pages 或自己的服务器
- 建议每天固定时间更新（如上午 10 点）

### 9. 替代方案

如果不使用 GitHub Pages，也可以：

1. **Gitee Pages**：国内访问更快
2. **自己的服务器**：上传 JSON 文件到任意 Web 服务器
3. **腾讯云 COS**：对象存储 + CDN 加速
4. **阿里云 OSS**：同上

只需修改 `DATA_BASE_URL` 为对应的地址即可。
