# generate-release-version

自动生成符合语义化版本规范的 Alpha 发布版本号。用于 GitHub Actions 工作流中自动化管理版本迭代和测试版本发布。

## 功能特性

- 📦 自动读取 `package.json` 中的当前版本
- 🔧 自动递增 patch 版本号（语义化版本 PATCH）
- 🏷️ 自动搜索现有 Alpha 标签并生成有序的 Alpha 版本号
- 📊 输出完整的版本信息用于后续 CI/CD 步骤
- 🔢 记录 GitHub Actions 工作流运行编号便于追踪

## 使用示例

### 基础用法

```yaml
- name: Generate Release Version
  id: version
  uses: Liar0320/generate-release-version@v1.0.0

- name: 使用版本号进行后续操作
  run: |
    echo "当前版本: ${{ steps.version.outputs.current_version }}"
    echo "测试版本: ${{ steps.version.outputs.test_version }}"
    echo "Alpha 编号: ${{ steps.version.outputs.alpha_number }}"
    echo "Build 编号: ${{ steps.version.outputs.build_number }}"
```

## 输出参数

| 输出变量 | 说明 | 示例 |
| -------- | ---- | ---- |
| `current_version` | 当前 package.json 版本号 | `1.5.12` |
| `test_version` | 生成的 Alpha 版本号 | `1.5.13-alpha.1` |
| `alpha_number` | Alpha 序号（该版本已发布的 Alpha 版本数 + 1） | `1` |
| `build_number` | GitHub Actions 工作流运行编号 | `42` |

## 工作流示例

### 发布 Alpha 版本工作流

```yaml
name: Release Alpha Version

on:
  workflow_dispatch:

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: 生成版本号
        id: version
        uses: Liar0320/generate-release-version@v1.0.0

      - name: 创建 Alpha 标签
        run: |
          git tag ${{ steps.version.outputs.test_version }}
          git push origin ${{ steps.version.outputs.test_version }}

      - name: 构建项目
        run: npm run build

      - name: 发布到 npm (alpha 标签)
        run: npm publish --tag alpha
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### 与 WeCom 通知集成

```yaml
- name: 生成版本号
  id: version
  uses: Liar0320/generate-release-version@v1.0.0

- name: 发送企业微信通知
  uses: Liar0320/wecom-notify@v1.0.0
  with:
    body_path: CHANGELOG.md
    robots_key: ${{ secrets.WECOM_ROBOTS_KEY }}
  env:
    VERSION: ${{ steps.version.outputs.test_version }}
```

## 环境依赖

- Node.js（需要可用的 `node` 命令）
- Git（用于 git fetch --tags）

GitHub 托管 runners 默认已提供以上工具。

## 版本递增规则

该 Action 遵循 [语义化版本](https://semver.org/lang/zh-CN/) 规范：

- **主版本号**：通常表示有破坏性变更时递增
- **次版本号**：新增功能时递增
- **修订号**：本 Action 自动递增，用于 Alpha 版本基础

示例递增过程：
```
v1.5.12 (当前版本)
  ↓
v1.5.13-alpha.1 (第一个 Alpha)
v1.5.13-alpha.2 (第二个 Alpha)
v1.5.13 (正式发布)
```

## 许可证

本项目基于 MIT License 发布。
