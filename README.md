# Token-Verifier-Data

[Token-Verifier](https://github.com/chaterm/Token-Verifier) 的官方数据仓库：**官方题库快照与基线 rawData 的发布地**。

本仓库的 git 里只放 workflow 与本索引；题库和 rawData 一律以 **GitHub Release** 形式发布（基线是不可变发布物，不进版本历史）。每条基线 = 一个 release，tag 形如 `baseline-<model>-suitev<N>`，附件为四个**未打包**的文件：

| 附件 | 说明 |
|---|---|
| 题库快照 yaml | 采集该 rawData 时用的题库（`suite_version` 与 manifest 强一致校验） |
| `*.rawdata.jsonl.gz` | digest 级 rawData（full 级含模型输出文本，门禁直接拒绝） |
| config yaml | 采集配置存档（无密钥）。发布时 workflow 自动：把 `suite.path` 改写为同级文件名并填入 `suite.sha256`；把 `target.base_url` 脱敏为 `https://api.example.com`（base_url 不进 digest，不影响可比性）；扫描到明文密钥样式则拒绝发布 —— 下载到同一目录、换成自己的端点即可 `tv run` |
| `SHA256SUMS` | 上述全部附件的校验和，由 workflow 生成 |

## 基线索引

| 模型 | suite | tool 版本 | 采集窗口/备注 | Release |
|---|---|---|---|---|
| DeepSeek-V4.1-Flash | v4 | 0.1.0-rc.1 | — | [`baseline-deepseek-v4.1-flash-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-deepseek-v4.1-flash-suitev4) |
| DeepSeek-V4-Pro-0813 | v4 | 0.1.0-rc.1 | — | [`baseline-deepseek-v4-pro-0813-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-deepseek-v4-pro-0813-suitev4) |
| GLM-5.3-Flash | v4 | 0.1.0-rc.1 | — | [`baseline-glm-5.3-flash-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-glm-5.3-flash-suitev4) |
| GLM-5.3 | v4 | 0.1.0-rc.1 | — | [`baseline-glm-5.3-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-glm-5.3-suitev4) |
| GPT-6-Astra | v4 | 0.1.0-rc.2 | — | [`baseline-gpt-6-astra-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-gpt-6-astra-suitev4) |
| GPT-5.6-Sol | v4 | 0.1.0-rc.2 | — | [`baseline-gpt-5.6-sol-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-gpt-5.6-sol-suitev4) |
| GPT-5.6-Luna | v4 | 0.1.0-rc.2 | — | [`baseline-gpt-5.6-luna-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-gpt-5.6-luna-suitev4) |
| GPT-5.6-Terra | v4 | 0.1.0-rc.2 | — | [`baseline-gpt-5.6-terra-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-gpt-5.6-terra-suitev4) |
| Claude-Sonnet-5 | v4 | 0.1.0-rc.3 | — | [`baseline-claude-sonnet-5-suitev4`](https://github.com/chaterm/Token-Verifier-Data/releases/tag/baseline-claude-sonnet-5-suitev4) |
<!-- baseline-rows -->

rawData 的可比性由 format_version / probe_version / suite_version 等共同决定，任一不一致 digest 闸门会拒绝比较，详见主仓库 [docs/BASELINES.md](https://github.com/chaterm/Token-Verifier/blob/main/docs/BASELINES.md) 与 [docs/SPEC-RAWDATA.md](https://github.com/chaterm/Token-Verifier/blob/main/docs/SPEC-RAWDATA.md)。

## 使用基线

```bash
TAG=baseline-<model>-suitev<N>
BASE=https://github.com/chaterm/Token-Verifier-Data/releases/download/$TAG
curl -LO $BASE/<题库文件>.yaml
curl -LO $BASE/<rawData文件>.rawdata.jsonl.gz
curl -LO $BASE/<config文件>.yaml
# config 的 suite.path / suite.sha256 已指向同级题库，直接：
tv run -c <config文件>.yaml <rawData文件>.rawdata.jsonl.gz
```

## 维护者：发布新基线

1. 用当前官方题库在官方端点采集：`tv collect -c <model>.yaml -o <model>.rawdata.jsonl.gz`（`raw_level: digest`）
2. 本仓库 → Releases → **Draft a new release**：tag 填 `baseline-<model>-suitev<N>`，把题库 yaml、config yaml、rawData 三个文件**逐个拖入（不要打包成 zip/tar）**，保存为 draft
3. Actions → **Publish Baseline** → Run workflow，输入 draft 的 tag（模型名、备注可留空/选填）
4. workflow 校验通过后自动：改写 config 的 `suite.path`/`sha256` → 生成 SHA256SUMS → 发布 release → 在本页索引表加一行。任一校验失败则 release 保持 draft，不会发布
5. 已发布的 release 不可重跑（基线不可变）；更正请用新 tag 重新走 draft 流程
