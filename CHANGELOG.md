# 更新日志

## [1.0.0] - 2025-12-02

### 新增
- ✨ 自动读取 `package.json` 中的版本号
- ✨ 自动递增 patch 版本号（语义化版本）
- ✨ 自动生成有序的 Alpha 版本号 (v1.5.13-alpha.1 等)
- ✨ 自动搜索现有 Alpha 标签，避免版本号重复
- ✨ 输出完整的版本信息供后续 CI/CD 步骤使用
- ✨ 支持 GitHub Actions 工作流运行编号追踪

### 特性
- 📦 输出参数：`current_version`、`test_version`、`alpha_number`、`build_number`
- 🔧 支持语义化版本规范 (SemVer)
- 🏷️ 基于 git 标签管理版本发布历史
- 📊 详细的日志输出便于调试
