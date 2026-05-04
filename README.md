# pl-ml-project-template

> 一个 Claude Code 技能，为标准监督学习研究任务一键生成 PyTorch Lightning 项目脚手架。

参考：[miracleyoo/pytorch-lightning-template](https://github.com/miracleyoo/pytorch-lightning-template)

---

## 支持的任务类型

| 类型 | 说明 |
| ---- | ---- |
| `classification` | 图像/表格分类 |
| `regression` | 连续值回归 |
| `timeseries` | 时序预测 |

**不适用于：** LLM 预训练、PEFT/LoRA、RAG、Agent、推理服务、纯 scikit-learn 项目。

---

## 安装

```bash
git clone https://github.com/wangyi0403/pl-ml-project-template ~/.claude/skills/pl-ml-project-template
```

---

## 使用

直接告诉 Claude：

```
帮我初始化一个分类实验项目
```

或手动运行脚手架：

```bash
python scripts/init_project.py /path/to/your/project --task-type classification
python scripts/init_project.py /path/to/your/project --task-type regression
python scripts/init_project.py /path/to/your/project --task-type timeseries

# 强制覆盖已有文件
python scripts/init_project.py /path/to/your/project --task-type timeseries --force
```

---

## 生成的项目结构

```
project/
├── main.py                     # 入口：argparse + Trainer 组装
├── utils.py                    # 工具函数（配置加载等）
├── Makefile                    # 一键工作流
├── README.md
├── requirements.txt
├── .env.example
├── .gitignore
├── configs/
│   └── default.yaml            # 默认超参数
├── data/
│   ├── __init__.py
│   ├── data_interface.py       # LightningDataModule 接口
│   └── standard_dataset.py     # 占位数据集（用户替换）
├── model/
│   ├── __init__.py
│   ├── model_interface.py      # LightningModule 接口
│   └── standard_model.py       # 占位模型（用户替换）
├── scripts/
│   ├── train.sh
│   ├── test.sh
│   ├── run_all.sh              # 一键复现：训练→测试→可视化→导出
│   ├── ablation.sh             # 消融实验参数扫描
│   ├── visualize.py            # 绘制训练曲线 → figures/
│   └── export_results.py       # 结果 JSON → LaTeX 表格
├── docs/doc/                   # 论文素材（命名规范：YYYY-MM-DD_描述_vN.ext）
├── results/
├── figures/
├── train_log/
└── test_log/
```

---

## 核心设计：接口模式

训练逻辑与模型/数据集实现解耦，通过两个接口类连接：

```
main.py
  ├── MInterface (LightningModule)    ──动态导入──>  my_model.py (纯 nn.Module)
  └── DInterface (LightningDataModule) ──动态导入──>  my_data.py  (纯 Dataset)
```

**添加新模型只需：**
1. 创建 `model/my_model.py`，定义 `class MyModel(nn.Module)`
2. 运行 `python main.py --model_name my_model`
3. 无需修改接口或 main.py

命名约定：文件 `foo_bar.py` → 类名 `FooBar`。

---

## 快速开始（生成项目后）

```bash
make train      # 训练
make test       # 测试
make visualize  # 生成训练曲线图
make export     # 导出 LaTeX 结果表
make all        # 以上全部
make ablation   # 消融实验
```

---

## License

MIT
