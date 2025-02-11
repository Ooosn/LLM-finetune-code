# LLM-finetune-code
微调 Qwen2.5-VL的代码，可用于延伸大部分大模型的代码的微调

# 参考大模型部署代码
首先需要参考LLM中代码部署中所使用的数据处理工具，message 如何合成，以及最终采用的何种方式生成 input

# 设计 Dataset 部分以及 collator 部分
比如在 Qwen2.5-VL 中，除 labels 外，另外包含四种值，因为如果在 transformers 模组中未适配的话，之间传入会引起数据格式错误，
在 collator 部分，会再次引起连锁反应。

因此这两部分需要自己的简单设置一下，根据大模型部署的部分让 Dataset 函数返回正确的 inputs 字典。（可通过 model(**inputs) 查看是否能正确产生 loss）
labels 部分可能也需要稍微修改一下，对于多模态，或者格式处理，有一些不一样的方式。
比如在 Qwen 中，tokenizer 或者说 processor 会在前后部分产生一些标识符，所以如果在 message 部分，text 已经加入了正确答案/填充部分，在 labels 中不能是简单的对后续部分替换，否则会出现不对齐的现象发生。（根据部署模型的 input_ids 和 generate_ids，进行具体的查看以及后续调整）

随后通过 collator 简单化处理，使其直接返回 inputs/batch ，传入 model 函数中：
对于 batch 非1，需要根据模型结构，自行添加合适的 padding 结构，使不同的数据对齐。

# 此时可以利用 Trainer 函数 以及 training_args 进行简单的调整
