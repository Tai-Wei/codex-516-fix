# codex-516-fix

一个针对 [openai/codex#29353](https://github.com/openai/codex/issues/29353) 的 `AGENTS.md` 规避配置。

这个项目不是 OpenAI 官方补丁，也不会修改 Codex Desktop、模型路由或 reasoning budget 的内部实现。它提供的是一份项目级或全局级的 `AGENTS.md`，用更严格的问题分析规则降低 Codex 在复杂推理题上过早直出错误答案的概率。

## 背景

[openai/codex#29353](https://github.com/openai/codex/issues/29353) 报告了一个可复现现象：在 Codex Desktop 中使用 `gpt-5.5` + `xhigh` reasoning 时，同一个推理题有时会直接进入 `final_answer`，没有可见的中间 `commentary`，并返回错误答案。失败样本的本地 session 日志稳定出现：

```text
reasoning_output_tokens: 516
phases: ["final_answer"]
```

该 issue 中的复现题是一个可以通过触摸区分形状的糖果抽取问题。错误路径常把问题当成普通盲抽，得到 `29`；正确路径会利用“形状可触摸区分”这一可控信息，得到 `21`。

后续相关 issue [openai/codex#30364](https://github.com/openai/codex/issues/30364) 又给出了更大范围的聚合观察：`gpt-5.5` 的 reasoning token metadata 在 `516`、`1034`、`1552` 附近出现固定边界聚集，并且 `516` 聚集和复杂任务上的表现下降可能相关。

外部讨论 [router-for-me/CLIProxyAPI#3937](https://github.com/router-for-me/CLIProxyAPI/discussions/3937) 也围绕同一个糖果题和 `reasoning 516` 现象进行了复现交流。

## 这个项目解决什么

`codex-516-fix` 试图解决的是 #29353 暴露出的项目侧问题：

> 当模型在复杂保证类问题上过早套用“盲抽”模式时，即使 reasoning effort 设置很高，也可能走短路径并给出错误答案。

## 适用场景

适合用于这些 Codex 任务：

- 算法题、逻辑题、称重题、排序题、边界题
- 需要最坏情况保证的问题
- 容易被误解成一次性盲抽的问题
- 含有“可观察、可触摸、可标记、可排序、可控制”条件的问题
- 希望 Codex 少汇报中间过程、但最终答案更严谨的工作流

它可以辅助文艺内容的分析、改写和结构判断，但不是专门为开放式创作设计的。

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

## 复现检查

可以用 #29353 中的糖果题检查配置效果。关键检查点不是输出是否更长，而是模型是否正确利用“形状可以靠触摸区分”这一条件。

期望结果：

```text
最少取出 21 个。
```

如果模型仍然回答 `29`，通常说明它把问题当成了无法控制形状的普通盲抽问题，没有使用题目中给出的可触摸区分条件。

## 文件说明

```text
AGENTS.md   Codex 行为规则
LICENSE     Apache-2.0 许可证
```

## License

Apache-2.0
