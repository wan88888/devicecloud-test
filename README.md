# Device Cloud Test

基于 [Device Cloud](https://devicecloud.dev) 和 [Maestro](https://maestro.mobile.dev) 的移动应用 UI 自动化测试项目。

## 项目结构

```
devicecloud-test/
├── .github/
│   └── workflows/
│       └── devicecloud-test.yaml    # GitHub Actions 工作流
├── tests/
│   └── login.yaml                   # Maestro 测试流程文件
└── README.md
```

## 功能特性

- 在真实云端设备上运行 Maestro UI 测试
- 支持 GitHub Actions 自动化触发
- 测试结果自动保存为 Artifact
- 飞书机器人实时通知

## 工作流触发条件

| 触发方式 | 说明 |
|---------|------|
| `push` | 推送到 `main` 分支时触发 |
| `pull_request` | PR 到 `main` 分支时触发 |
| `workflow_dispatch` | 手动触发 |

## 配置说明

### GitHub Secrets 配置

在仓库的 **Settings → Secrets and variables → Actions** 中添加以下 Secrets：

| Secret 名称 | 必填 | 说明 |
|------------|------|------|
| `DCD_API_KEY` | 是 | Device Cloud API Key，在 [控制台](https://console.devicecloud.dev) 获取 |
| `FEISHU_WEBHOOK` | 否 | 飞书机器人 Webhook 地址，用于测试结果通知 |

### 工作流参数

当前配置的测试参数：

| 参数 | 值 | 说明 |
|-----|-----|------|
| `app-binary-id` | `9424cc28-de18-4aed-86d9-0f3459f933ad` | 预上传的应用 ID |
| `flows` | `tests/` | Maestro 测试流程目录 |
| `android-device` | `pixel-6` | 测试设备型号 |
| `json-file` | `true` | 输出 JSON 格式测试报告 |

## 编写测试用例

测试文件位于 `tests/` 目录，使用 YAML 格式编写 Maestro 流程。

### 示例：登录测试

```yaml
appId: com.example.app
---
- launchApp
- tapOn: "Username"
- inputText: "test_user"
- tapOn: "Password"
- inputText: "test_password"
- tapOn: "LOGIN"
- assertVisible: "Welcome"
- stopApp
```

### 常用 Maestro 命令

| 命令 | 说明 | 示例 |
|-----|------|------|
| `launchApp` | 启动应用 | `- launchApp` |
| `stopApp` | 停止应用 | `- stopApp` |
| `tapOn` | 点击元素 | `- tapOn: "Button"` |
| `inputText` | 输入文本 | `- inputText: "Hello"` |
| `assertVisible` | 断言元素可见 | `- assertVisible: "Title"` |
| `assertNotVisible` | 断言元素不可见 | `- assertNotVisible: "Error"` |
| `scroll` | 滚动 | `- scroll` |
| `swipe` | 滑动 | `- swipe: LEFT` |
| `waitForAnimationToEnd` | 等待动画结束 | `- waitForAnimationToEnd` |

更多命令请参考 [Maestro 文档](https://maestro.mobile.dev/api-reference/commands)。

## 测试报告

### 查看方式

1. **Device Cloud 控制台** - 点击飞书通知中的「查看详细报告」按钮
2. **GitHub Artifacts** - 在 Actions 运行记录中下载 `test-results` 产物

### 输出变量

工作流提供以下输出变量，可用于后续步骤：

| 变量 | 说明 |
|-----|------|
| `DEVICE_CLOUD_CONSOLE_URL` | 测试报告控制台链接 |
| `DEVICE_CLOUD_UPLOAD_STATUS` | 测试状态 (PASSED/FAILED) |
| `DEVICE_CLOUD_FLOW_RESULTS` | 各流程测试结果 (JSON) |
| `DEVICE_CLOUD_APP_BINARY_ID` | 应用 Binary ID |

## 飞书通知

测试完成后会自动发送飞书卡片通知：

- 蓝色卡片：测试通过
- 红色卡片：测试失败

### 配置飞书机器人

1. 打开飞书群 → 设置 → 群机器人
2. 添加机器人 → 自定义机器人
3. 复制 Webhook 地址
4. 添加到 GitHub Secrets 的 `FEISHU_WEBHOOK`

## 本地开发

### 安装 Maestro CLI

```bash
curl -Ls "https://get.maestro.mobile.dev" | bash
```

### 本地运行测试

```bash
# 运行单个测试
maestro test tests/login.yaml

# 运行目录下所有测试
maestro test tests/
```

## 相关链接

- [Device Cloud 官网](https://devicecloud.dev)
- [Device Cloud 控制台](https://console.devicecloud.dev)
- [Maestro 文档](https://maestro.mobile.dev)
- [GitHub Action](https://github.com/devicecloud-dev/device-cloud-for-maestro)

## License

MIT
