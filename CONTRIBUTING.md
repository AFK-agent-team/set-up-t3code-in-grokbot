# 贡献指南（Contributing）

感谢你关注并改进 `set-up-t3code-in-grokbot`。本仓库维护面向 Grok Bot 环境的 T3 Code 中文设置与排障技能（[SKILL.md](SKILL.md)）及离线复盘手册（[index.html](index.html)）。

## 可以贡献什么
- **新的故障分诊案例**：补充可复现的症状、先查顺序、最小可逆修复与明确通过标准。
- **兼容性与参数变更记录**：当新版 T3 Code、Tailscale 或 `agy` CLI 的 `--help` 参数发生变化时，补充条件化适配说明（不硬编码版本或下载 URL）。
- **脱敏后的真实实验证据**：提交带有时区标注（如 UTC+8）、明确实验范围与边界说明的冷/暖启动、采样或恢复复测记录。
- **文档与可读性改进**：修复失效链接、过时术语或补充清晰的静态图表。

## 必须遵守的安全与脱敏红线
提交 Issue 或 Pull Request 前，请确保所有新增内容**不包含**：
- 任何完整配对链接、一次性配对码、授权码、会话 Cookie、访问令牌（Token）或 API Key；
- 个人公网域名（如个人 `.ts.net` 地址）、私有 IP、个人邮箱、本机用户路径（如 `/Users/...`）或真实进程号（PID）；
- 绕过平台出站限制、关闭 TLS/公网鉴权或伪造整机休眠证据的做法。

## 修改流程与同步规则
1. 若修改 [SKILL.md](SKILL.md)，请同步更新 [index.html](index.html) 中 `<pre>...</pre>` 内的技能正文，保持离线页面与主技能一致。
2. 保持 [index.html](index.html) 与 [eval.html](eval.html) 零脚本、零第三方外部依赖，同目录相对引用（`SKILL.md`、`index.html`）有效。
3. 保留专业英文术语、命令、文件名、环境变量与配置字段原样（如 `CopyFromBox`、`GEMINI_HOME`、`AGY_ACP_FORCE_FILE_STORAGE=1`、`agy acp --login`），正文解释使用简洁中文。
4. 提交前运行 [docs/verification-evidence.md](docs/verification-evidence.md#三本地静态核对命令不触碰高风险安装脚本) 中的本地静态核对脚本，确认输出 `STATIC_CHECK_PASS`。

## 反馈与讨论
- 发现问题、提出新场景或提交脱敏排障经验，请前往 [GitHub Issues](https://github.com/AFK-agent-team/set-up-t3code-in-grokbot/issues) 创建工单。
- **许可说明**：仓库当前尚未添加开源许可证；如需对外再分发或商业复用，请先通过 Issue 与维护者确认授权范围。
