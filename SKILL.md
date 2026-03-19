# Second Brain Triage

智能信息分诊系统，基于Tiago Forte的PARA方法（Projects/Areas/Resources/Archive）自动分类和优先级排序。

## 功能

- **内容分析器**：自动识别内容类型（文章、视频、任务、代码等），提取元数据
- **PARA分类器**：智能分类到项目/领域/资源/归档
- **紧急度评分**：多维度算法评估处理优先级（1-10分）
- **关联性检测**：发现内容间的相似性和关联关系

## 使用方法

### 基本用法

```javascript
const { SecondBrainTriage } = require('./src');

const triage = new SecondBrainTriage();

// 分诊单个内容
const result = triage.triage('TODO: 完成项目报告，截止本周五');
console.log(result.summary);
// {
//   title: "完成项目报告，截止本周五",
//   type: "task",
//   category: "项目",
//   urgency: "高紧急",
//   urgencyScore: 8,
//   action: "今天处理：建议在24小时内完成"
// }

// 批量分诊
const results = triage.triageBatch([
  'https://github.com/user/repo',
  '学习React Hooks的笔记',
  'TODO: 修复登录bug',
]);

// 导出报告
const report = triage.exportReport(results, 'markdown');
```

### CLI使用

```bash
# 分析单个内容
node scripts/triage.js "需要处理的文本内容"

# 分析文件
node scripts/triage.js --file ./notes.txt

# 批量分析
node scripts/triage.js --batch ./items.json --output report.md
```

## 分类说明

### PARA分类

| 分类 | 说明 | 示例 |
|------|------|------|
| **Projects** | 有明确目标和截止日期的项目 | "开发新功能"、"完成报告" |
| **Areas** | 长期负责的标准和责任领域 | "健康管理"、"技能提升" |
| **Resources** | 感兴趣的主题和参考资料 | "技术文章"、"学习笔记" |
| **Archive** | 已完成或不活跃的内容 | "已完成项目"、"历史记录" |
| **Inbox** | 待分类的临时存储 | 无法确定分类的内容 |

### 紧急度等级

| 分数 | 等级 | 说明 | 建议 |
|------|------|------|------|
| 9-10 | 极紧急 | 立即处理 | 马上行动 |
| 7-8 | 高紧急 | 今天处理 | 24小时内完成 |
| 5-6 | 中等 | 本周处理 | 本周内安排 |
| 3-4 | 低紧急 | 低优先级 | 可以延后 |
| 1-2 | 不急 | 存档备查 | 无需立即行动 |

## 技术架构

```
src/
├── content-analyzer.js    # 内容类型识别和元数据提取
├── para-classifier.js     # PARA分类算法
├── urgency-scorer.js      # 紧急度评分算法
├── relatedness-detector.js # 关联性检测
└── index.js               # 主入口和API
```

## 评分算法

### 紧急度评分维度

1. **时间敏感性** (30%)：截止日期、时间关键词
2. **行动需求** (25%)：必须/计划/可能等行动词
3. **后果** (20%)：不处理的潜在影响
4. **上下文信号** (15%)：阻塞、外部依赖等
5. **用户偏好** (10%)：可配置的优先级

### 关联性检测

- 标签相似度（Jaccard系数）
- 标题/描述文本相似度（余弦相似度）
- 语义相似度（基于语义组）
- 类型匹配

## 配置选项

```javascript
const triage = new SecondBrainTriage({
  enableRelatedness: true,    // 启用关联性检测
  urgencyThreshold: 5,        // 紧急度阈值
});
```

## 输出格式

支持导出为：
- JSON（完整数据）
- Markdown（阅读友好）
- CSV（表格格式）

## 依赖

- Node.js >= 14
- 无外部依赖（纯JavaScript实现）

## 许可证

MIT