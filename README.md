# codex-516-fix

一个面向 Codex 的极简 `AGENTS.md` 配置，用来减少不必要的中间汇报，并强化模型在逻辑、边界、定量和保证类问题上的推理质量。

## 这个项目是什么

本仓库提供一份可直接使用的 `AGENTS.md`。

它主要约束 Codex 的三类行为：

- 少发送非必要的 `commentary` 消息。
- 优先使用第一性原理推理，而不是直接套用模式。
- 对需要保证的题目，给出最坏情况下的充分性证明和匹配下界。

## 适用场景

适合用于这些 Codex 工作流：

- 算法题、逻辑题、称重题、排序题、边界题
- 需要最坏情况保证的问题
- 希望 Codex 少汇报过程、直接给出最终结果的任务
- 希望回答更严格地区分“可观察信息”“可控制行动”和“需要保证的结论”

## 使用方法

### 项目级使用

将 `AGENTS.md` 放到某个 Codex 项目的根目录：

```bash
cp AGENTS.md /path/to/your/project/AGENTS.md
```

之后 Codex 会在该项目中读取这些行为规则。

### 全局使用

如果希望所有 Codex 项目默认使用这套规则，可以将文件移动到 Codex 的全局配置目录：

```bash
mkdir -p ~/.codex
mv AGENTS.md ~/.codex/AGENTS.md
```

如果你想保留当前仓库里的副本，可以改用复制：

```bash
mkdir -p ~/.codex
cp AGENTS.md ~/.codex/AGENTS.md
```

全局规则会影响之后打开的 Codex 会话；项目内的 `AGENTS.md` 适合用来覆盖或补充特定项目的规则。

## 文件说明

```text
AGENTS.md   Codex 行为规则
LICENSE     Apache-2.0 许可证
```

## License

Apache-2.0
