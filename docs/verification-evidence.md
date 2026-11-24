# 真实验收证据、早期演练边界与静态核对记录

本文档整理了 `set-up-t3code-in-grokbot` 在 2026 年 11 月 24 日（北京时间，UTC+8）完成的真实受控验收记录、早期草稿演练基线与静态发布检查细节。所有结论严格区分**已实测证据**与**待验证边界**。

返回主文档：[README.md](../README.md) · 技能正文：[SKILL.md](../SKILL.md) · 离线复盘页：[index.html](../index.html) · 初稿演练评测页：[eval.html](../eval.html)

---

## 一、2026-11-24 真实验收时间线（UTC+8）

| 时间（UTC+8） | 验证层 / 实验项 | 观测结果 | 范围与边界说明 |
|---|---|---|---|
| `09:41` | T3 内模型服务任务 | 选择 `antigravity / gemini-4-neon-low`，任务状态 `completed`，输出 `T3_ANTIGRAVITY_VERIFIED sum=423` | 仅证明任务完成与实际输出；`selected` 不等于 `executed`，且 `executedModel` 为空，不据此证明实际执行模型身份 |
| `09:47` | 用户终端确认 | 用户在实际终端确认可用（`all set`） | 属于一次真实终端确认，不替代后续整机休眠恢复验证 |
| `09:53` (`09:53:11.792` → `09:53:31.480`) | 专用守护进程受控重启 | 重启恢复耗时 **17.239s**；Funnel 配置前后一致，T3 进程未变，原诊断登录仍有效，未消费用户配对码 | 属于进程级受控重启，**不等于**真实整机休眠与唤醒 |
| `09:53:35.838` — `10:03:34.804` | 10 分钟连续采样（30s 计划间隔，共 21 次） | 在线 **21/21**；会话 API、HTML、JS、CSS 各 **21/21**（合计 **84/84**）；`authenticated=true` **21/21**；JS/CSS 哈希匹配各 **21/21**；WebSocket 101 **11/11**；会话 API 延迟中位数 **0.283s**、最大 **1.507s** | 请求执行带来少量时间偏移；并非每个样本都测 WS；10 分钟通过不保证无限期稳定 |
| `09:54` — `09:55` | 真实浏览器暖启动（2 次） | 两次到达配对页，耗时 **1.283s** 与 **1.071s**，未观察到页面错误 | 仅覆盖本轮暖启动 |
| `09:57` (`09:57:05.310` → `09:57:11.734` → `09:57:16.286`) | 新原生无痕浏览器冷启动 | `09:57:05.310` 启动，`09:57:11.734` 仍在加载，`09:57:16.286` 确认配对页；耗时**上界 10.976s** | 上界观测值，非精确首屏计时；未捕获本轮冷启动请求瀑布图，不能声称冷启动无错误，也不能据此倒推早期卡 logo 根因 |
| `10:03:35.673` | 验收收尾 | 专用诊断会话已撤销 | 保持最小权限与现场清理 |

### 仍待有界实验验证的范围
1. **真实整机休眠与恢复**：未执行真实整机休眠；不得为测试强制休眠共享宿主。后续应在自然唤醒或获准窗口中按检查点复测。
2. **冷启动精确请求瀑布图与历史卡 logo 初因**：需在独立冷缓存上下文中采集脱敏请求瀑布图与控制台日志，定位资源、初始化、API 或会话阶段。
3. **实际执行模型身份（`executedModel`）**：当驱动或接口返回空值时，只报告任务输出已核验、执行模型未证实。

---

## 二、早期草稿演练（With / Without Skill）基线与限制

以下为早期草稿阶段的文本流程演练数据，与上方真实运行验收属于**两类不同证据**：

| 原草稿演练场景 | With Skill（使用技能） | Without Skill（不使用技能） | 关键差异与取舍 |
|---|---:|---:|---|
| 网络排障场景（Tailscale / 启动分层） | **8 / 8** | **2 / 8** | 使用技能后覆盖实例归属核对、持久化路径检查、冷暖拆分与安全门禁；不使用技能时易跳步或仅凭 HTTP 200 误判 |
| 隔离连接场景（Antigravity ACP 认证） | **7 / 10** | **3 / 10** | 使用技能后覆盖目录隔离、环境变量对齐与 T3 端到端输出核验；仍有 3 项复合断言未在早期草稿全覆盖 |

**必须保留的评测边界**：
- 基线输出继承了父会话上下文，**并非**严格上下文隔离评测。
- 仅衡量文字执行计划覆盖率，**不是**真实安装、真实网络或稳定性测试。
- 复合断言缺任一项即判失败，未陈述某步不等于执行了危险动作。
- 后续修订版未重新评分，因此**不声称**当前 `SKILL.md` 达到 100% 通过。

---

## 三、本地静态核对命令（不触碰高风险安装脚本）

本仓库目前未内置专用 verify skill、未部署网站、也未配置 CI/CD。修改文档或同步页面后，可在仓库根目录运行以下只读静态校验：

```bash
python3 - <<'PY'
import pathlib, re, sys

readme = pathlib.Path("README.md").read_text(encoding="utf-8")
skill = pathlib.Path("SKILL.md").read_text(encoding="utf-8")
html = pathlib.Path("index.html").read_text(encoding="utf-8")

# 1. 核对 SKILL.md 元数据
assert re.search(r"(?m)^name:\s*set-up-T3code-in-grokbot\s*$", skill), "SKILL.md name 缺失"
assert re.search(r"(?m)^description:\s*.+", skill), "SKILL.md description 缺失"

# 2. 核对 index.html 内嵌技能与 SKILL.md 一致
m = re.search(r"<pre>(.*?)</pre>", html, re.S)
assert m and m.group(1).strip() == skill.strip(), "index.html 内嵌 <pre> 与 SKILL.md 不一致"

# 3. 核对 index.html 与 eval.html 无脚本/外部依赖且相对引用正确
assert "<script" not in html.lower(), "index.html 不应包含脚本"
assert 'href="SKILL.md"' in html, "index.html 缺少同目录 SKILL.md 下载链接"
eval_html = pathlib.Path("eval.html").read_text(encoding="utf-8")
assert "<script" not in eval_html.lower(), "eval.html 不应包含脚本"
assert 'href="index.html"' in eval_html and 'href="SKILL.md"' in eval_html, "eval.html 缺少同目录链接"

# 4. 核对 README.md 本地相对路径存在
for target in re.findall(r"\]\((?!https?://|#|mailto:)([^)]+)\)", readme):
    clean = target.split("#", 1)[0]
    assert pathlib.Path(clean).exists(), f"README 相对路径不存在: {clean}"

print("STATIC_CHECK_PASS")
PY
```

该脚本仅作文档、链接与内嵌一致性校验，不构成新增运行时或稳定性结论。
