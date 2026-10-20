# 膝关节 KL 分级基线（双视图正侧位）

## 文件
- `dataset.py`  数据集类：按划分表读正位(-1)/侧位(-2)图 + 正位KL标签
- `model.py`    双视图 ResNet50 模型（+单视图对照版）
- `train.py`    训练：迁移学习 + 类别不平衡加权 + 用 val 的 QWK 选最优模型
- `evaluate.py` 在 test 集做最终评估（QWK / 准确率 / macro-F1 / 混淆矩阵）

## 准备
1. 把 `规范图片/` 目录和 `数据划分_九批_80_10_10.csv` 上传到 GPU 服务器，与本代码同目录。
2. 装依赖：`pip install -r requirements.txt`
   （torch 建议按服务器 CUDA 版本从官网命令装。）

## 跑
```bash
python train.py       # 训练，最优模型存到 checkpoints/best.pth
python evaluate.py    # 只在最后跑一次，得到 test 成绩
```

## 改配置
- `train.py` 顶部 CONFIG：MODEL='dual'(双视图)/'single'(仅正位)、EPOCHS、BATCH、LR。
- 想做"双视图 vs 单视图"的对照实验（论文卖点）：分别用 dual / single 各训一次，比 test QWK。

## 说明
- 标签用正位 KL（国际惯例，负重正位为准）；侧位图作为第二输入参与预测。
- 主指标 Quadratic Weighted Kappa（KL 有序分级的标准指标）。
- 按病人分组划分已在 csv 中固定，无数据泄漏。
- 下一步论文增强：ordinal/序数损失、Grad-CAM 可解释性、与医生评分一致性、外部验证集。
