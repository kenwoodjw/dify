# 数组元数据过滤功能 - Debug指南

## 问题分析

从你的截图可以看出，当前选择的是`name`字段，它是`string`类型的元数据字段。要使用数组变量选择器，需要满足以下条件：

1. **元数据字段必须是array类型** - 当前显示的是string类型
2. **工作流中需要有array类型的变量** - 用于变量选择

## Debug步骤

### 1. 创建array类型的元数据字段

首先需要在知识库中创建一个array类型的元数据字段：

```bash
# 通过API创建array类型元数据字段
curl -X POST \
  http://localhost:3000/datasets/{dataset_id}/metadata \
  -H "Authorization: Bearer your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "array",
    "name": "job_ids"
  }'
```

### 2. 在工作流中添加数组变量

在工作流的开始节点中添加array类型的变量：

```json
{
  "key": "user_jobs",
  "name": "用户工作ID列表",
  "type": "array",
  "required": true,
  "default": ["job1", "job2"]
}
```

### 3. 测试流程

1. **选择array类型字段**：在元数据过滤条件中选择`job_ids`字段
2. **查看变量选择器**：此时应该显示array类型的变量
3. **验证操作符**：确认显示了`in`, `not in`, `contains`, `not contains`等数组操作符

### 4. 添加调试日志

我已经在代码中添加了调试日志，打开浏览器开发者工具查看：

- `🔍 ConditionArray Debug` - 显示可用的数组变量
- `🔧 数组变量被选择` - 显示变量选择过程

### 5. 检查数据流

**当前问题**：
- 元数据字段类型：`string` (name字段)
- 需要的字段类型：`array` (如job_ids字段)

**解决方案**：
1. 创建array类型的元数据字段
2. 选择该字段进行过滤
3. 此时变量选择器会显示数组类型变量

## 验证方法

### 检查元数据字段列表
```javascript
// 在浏览器控制台运行
console.log('当前元数据字段:', metadataList);
```

### 检查可用变量
```javascript
// 检查数组变量
console.log('可用数组变量:', availableArrayVars);
console.log('通用数组变量:', availableCommonArrayVars);
```

### 验证变量过滤逻辑
```javascript
// 检查变量过滤器
const filterArrayVar = (varPayload) => {
  return [
    'arrayString', 
    'arrayNumber', 
    'arrayObject', 
    'array'
  ].includes(varPayload.type);
};
console.log('过滤后的数组变量:', variables.filter(filterArrayVar));
```

## 常见问题

### Q1: 为什么没有显示数组变量？
**A**: 当前选择的是string类型字段。数组变量只在array类型字段中显示。

### Q2: 如何创建array类型的元数据字段？
**A**: 
1. 通过API创建：`POST /datasets/{id}/metadata` with `{"type": "array", "name": "field_name"}`
2. 或在知识库管理界面创建

### Q3: 变量选择器为空？
**A**: 检查工作流中是否有array类型的变量，确保变量类型为 `arrayString`, `arrayNumber`, `arrayObject`, 或 `array`

### Q4: 如何验证功能正常工作？
**A**: 
1. 创建array类型元数据字段
2. 在工作流开始节点添加array变量
3. 选择array字段进行过滤
4. 查看变量选择器是否显示数组变量

## 示例配置

### 元数据字段示例
```json
{
  "id": "job_ids_field",
  "name": "job_ids", 
  "type": "array"
}
```

### 工作流变量示例
```json
{
  "key": "target_jobs",
  "name": "目标工作列表",
  "type": "array",
  "default": ["job1", "job2", "job3"]
}
```

### 过滤条件示例
```json
{
  "id": "condition_1",
  "name": "job_ids",
  "comparison_operator": "in",
  "value": ["job1", "job2"]
}
```

## 下一步

1. 首先确认是否有array类型的元数据字段
2. 如果没有，创建一个
3. 确保工作流中有array类型的变量
4. 选择array字段进行测试 