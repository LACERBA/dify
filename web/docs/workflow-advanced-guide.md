# Dify 工作流组件补充文档

## 9. 如何添加新的工作流节点组件

### 9.1 添加新节点的步骤

要在 Dify 工作流中添加一个新的节点类型，需要完成以下步骤：

#### 9.1.1 更新节点类型枚举

首先，在 `app/components/workflow/types.ts` 文件中的 `BlockEnum` 中添加新节点类型：

```typescript
export enum BlockEnum {
  // 现有节点类型
  // ...
  // 添加新节点类型
  NewNodeType = 'new-node-type',
}
```

#### 9.1.2 创建节点组件

在 `app/components/workflow/nodes/` 目录下创建新节点的目录和组件文件：

```typescript
// app/components/workflow/nodes/new-node-type/index.tsx

import { FC, memo, useCallback } from 'react'
import { Handle, Position } from 'reactflow'
import { useTranslation } from 'react-i18next'

import { BlockEnum, CommonNodeType } from '../../types'
import { useStore } from '../../store'
import NodeWrapper from '../node-wrapper'

// 定义节点数据类型
export type NewNodeDataType = {
  // 节点特定属性
  config: {
    param1: string
    param2: number
    // 其他参数
  }
}

// 节点组件属性
type NewNodeProps = {
  id: string
  data: CommonNodeType<NewNodeDataType>
}

// 节点组件实现
const NewNode: FC<NewNodeProps> = ({ id, data }) => {
  const { t } = useTranslation()
  const { config } = data
  
  // 处理节点交互
  const handleClick = useCallback(() => {
    // 节点点击处理逻辑
  }, [])
  
  return (
    <NodeWrapper id={id} data={data} onClick={handleClick}>
      <div className="node-content">
        {/* 节点内容显示 */}
        <div className="param-display">
          <span className="label">{t('workflow.node.newNode.param1')}:</span>
          <span className="value">{config.param1}</span>
        </div>
        {/* 更多内容 */}
      </div>
      
      {/* 节点连接点 */}
      <Handle type="target" position={Position.Top} id="input" />
      <Handle type="source" position={Position.Bottom} id="output" />
    </NodeWrapper>
  )
}

export default memo(NewNode)
```

#### 9.1.3 创建节点配置面板

在 `app/components/workflow/panel/` 目录下创建新节点的配置面板：

```typescript
// app/components/workflow/panel/new-node-panel/index.tsx

import { FC, useCallback, useState } from 'react'
import { useTranslation } from 'react-i18next'

import { BlockEnum } from '../../types'
import { useStore } from '../../store'
import PanelWrapper from '../panel-wrapper'
import FormInput from '../components/form-input'

// 面板组件
const NewNodePanel: FC = () => {
  const { t } = useTranslation()
  const selectedNode = useStore(state => state.selectedNode)
  const updateNodeData = useStore(state => state.updateNodeData)
  
  // 面板状态
  const [param1, setParam1] = useState(selectedNode?.data.config?.param1 || '')
  const [param2, setParam2] = useState(selectedNode?.data.config?.param2 || 0)
  
  // 处理参数变更
  const handleParam1Change = useCallback((value: string) => {
    setParam1(value)
    if (selectedNode) {
      updateNodeData(selectedNode.id, {
        config: {
          ...selectedNode.data.config,
          param1: value,
        },
      })
    }
  }, [selectedNode, updateNodeData])
  
  // 更多参数变更处理
  
  return (
    <PanelWrapper title={t('workflow.node.newNode.title')}>
      <div className="panel-content">
        <FormInput
          label={t('workflow.node.newNode.param1')}
          value={param1}
          onChange={handleParam1Change}
        />
        {/* 更多表单控件 */}
      </div>
    </PanelWrapper>
  )
}

export default NewNodePanel
```

#### 9.1.4 注册节点类型和面板

在 `app/components/workflow/node-types.ts` 和 `app/components/workflow/panel/index.ts` 中注册新节点：

```typescript
// app/components/workflow/node-types.ts
import NewNode from './nodes/new-node-type'

export const nodeTypes = {
  // 现有节点
  // ...
  [BlockEnum.NewNodeType]: NewNode,
}

// app/components/workflow/panel/index.ts
import NewNodePanel from './new-node-panel'

export const nodePanels = {
  // 现有面板
  // ...
  [BlockEnum.NewNodeType]: NewNodePanel,
}
```

#### 9.1.5 添加节点默认配置

在 `app/components/workflow/nodes-default-config.ts` 中添加新节点的默认配置：

```typescript
export const nodesDefaultConfig = {
  // 现有节点配置
  // ...
  [BlockEnum.NewNodeType]: {
    type: BlockEnum.NewNodeType,
    title: 'New Node',
    desc: 'This is a new node type',
    config: {
      param1: 'default value',
      param2: 0,
    },
  },
}
```

### 9.2 节点设计最佳实践

1. **UI设计一致性**：保持与现有节点相同的视觉风格和交互模式
2. **响应式设计**：确保节点内容可以适应不同尺寸
3. **类型安全**：使用 TypeScript 定义所有类型
4. **性能优化**：使用 memo 包装组件，避免不必要的重渲染
5. **国际化支持**：所有文本使用 i18n 标记，支持多语言

## 10. 如何编辑工作流组件

### 10.1 编辑节点属性

编辑节点属性主要通过节点配置面板完成：

```typescript
// 更新节点数据
const updateNodeData = useStore(state => state.updateNodeData)

// 应用更新
updateNodeData(nodeId, {
  title: 'New Title',
  desc: 'New Description',
  config: {
    // 更新的配置项
  },
})
```

### 10.2 自定义节点外观

要自定义节点外观，可以编辑节点组件的样式：

```typescript
// 使用 TailwindCSS 自定义节点样式
<div className="bg-white rounded-lg shadow-md p-4 border-l-4 border-blue-500">
  {/* 节点内容 */}
</div>
```

### 10.3 修改节点行为

要修改节点行为，需要编辑相应的交互钩子：

```typescript
// 在节点组件中
const handleNodeClick = useCallback(() => {
  // 自定义点击行为
}, [/* 依赖 */])

// 或者在全局交互钩子中修改特定节点的处理
const handleNodeClick = useCallback((_, node) => {
  if (node.data.type === BlockEnum.YourNodeType) {
    // 特定节点类型的处理
  }
  else {
    // 默认处理
  }
}, [/* 依赖 */])
```

### 10.4 添加新功能

要为工作流组件添加新功能，可以通过扩展现有钩子或创建新钩子：

```typescript
// 创建新钩子
const useCustomFeature = () => {
  // 特性状态和方法
  const [featureState, setFeatureState] = useState()
  
  const handleFeatureAction = useCallback(() => {
    // 处理逻辑
  }, [])
  
  return {
    featureState,
    handleFeatureAction,
  }
}

// 在主组件中使用
const { featureState, handleFeatureAction } = useCustomFeature()
```

## 11. 现有工作流节点组件

Dify 工作流目前提供了以下节点类型：

### 11.1 基础节点

#### 11.1.1 开始节点 (Start)

工作流的入口点，接收用户输入并开始工作流执行。

```typescript
// 开始节点特性
- 只能有一个输出连接
- 不能有输入连接
- 每个工作流必须有一个开始节点
```

#### 11.1.2 结束节点 (End)

工作流的出口点，返回最终结果给用户。

```typescript
// 结束节点特性
- 只能有一个输入连接
- 不能有输出连接
- 每个工作流必须至少有一个结束节点
```

### 11.2 AI 处理节点

#### 11.2.1 LLM 节点

调用大型语言模型进行文本生成和处理。

```typescript
// LLM 节点配置
- 模型选择：支持各种 LLM 模型
- 提示词模板：可自定义提示词
- 温度参数：控制输出随机性
- 最大长度：限制输出长度
- 系统提示：设置模型的行为指南
```

#### 11.2.2 知识检索节点 (Knowledge Retrieval)

从知识库中检索相关信息。

```typescript
// 知识检索节点配置
- 知识库选择：选择要查询的知识库
- 查询模式：相似度、混合搜索等
- 检索数量：需要返回的文档数量
- 重排序选项：是否对结果进行重新排序
```

#### 11.2.3 Agent 节点

实现自主代理，能够规划和执行多步骤任务。

```typescript
// Agent 节点配置
- 代理类型：ReAct、Plan-and-Solve等
- 可用工具：配置代理可使用的工具
- 最大步数：限制代理执行的最大步骤数
- 思考过程：是否显示代理的思考过程
```

### 11.3 流程控制节点

#### 11.3.1 条件节点 (If-Else)

基于条件表达式的分支控制。

```typescript
// 条件节点配置
- 条件表达式：支持JavaScript语法的条件表达式
- 多分支支持：可配置多个条件分支
- 默认分支：当所有条件不满足时执行
```

#### 11.3.2 迭代节点 (Iteration)

对数组数据进行迭代处理。

```typescript
// 迭代节点由两部分组成
1. 迭代开始节点：指定迭代的数组变量
2. 迭代内部节点：在迭代内部处理数据
```

#### 11.3.3 循环节点 (Loop)

重复执行一组操作，直到满足特定条件。

```typescript
// 循环节点由两部分组成
1. 循环开始节点：指定循环条件和初始状态
2. 循环内部节点：循环执行的操作
```

#### 11.3.4 问题分类器节点 (Question Classifier)

基于自然语言理解的分支控制，可以对用户问题进行分类并路由到不同处理流程。

```typescript
// 问题分类器配置
- 分类规则：定义问题类别和示例
- 模型选择：用于分类的AI模型
- 分支处理：各类别对应的处理流程
```

### 11.4 数据处理节点

#### 11.4.1 变量赋值节点 (Variable Assigner)

创建或修改工作流中的变量。

```typescript
// 变量赋值节点配置
- 变量名：要创建或修改的变量名
- 变量值：可以是常量或其他变量的引用
- 变量类型：字符串、数字、布尔值、对象、数组等
```

#### 11.4.2 变量聚合节点 (Variable Aggregator)

将多个变量合并成一个对象或数组。

```typescript
// 变量聚合节点配置
- 聚合模式：对象模式或数组模式
- 输入变量：要聚合的变量列表
- 输出变量：聚合结果的变量名
```

#### 11.4.3 模板转换节点 (Template Transform)

使用模板语法转换文本内容。

```typescript
// 模板转换节点配置
- 模板内容：使用模板语法的文本
- 变量映射：模板中使用的变量映射
- 输出格式：纯文本、HTML、Markdown等
```

#### 11.4.4 列表操作节点 (List Filter)

对数组数据进行过滤、映射、排序等操作。

```typescript
// 列表操作节点配置
- 操作类型：过滤、映射、排序、切片等
- 输入列表：要操作的数组变量
- 操作参数：根据操作类型设置相应参数
```

#### 11.4.5 参数提取器节点 (Parameter Extractor)

从结构化或非结构化数据中提取参数。

```typescript
// 参数提取器配置
- 提取模式：正则表达式、JSON路径、AI辅助提取等
- 输入内容：要提取的内容来源
- 参数定义：要提取的参数名称和类型
```

### 11.5 集成节点

#### 11.5.1 HTTP 请求节点

与外部 API 进行交互。

```typescript
// HTTP 请求节点配置
- 请求方法：GET、POST、PUT、DELETE等
- URL：API端点
- 请求头：HTTP头信息
- 请求体：POST/PUT请求的数据
- 认证设置：API认证方式
```

#### 11.5.2 代码节点 (Code)

执行自定义 Python 代码。

```typescript
// 代码节点配置
- 代码编辑器：编写Python代码
- 输入变量：代码可访问的变量
- 输出变量：代码执行后返回的变量
- 超时设置：代码执行的最大时间
```

#### 11.5.3 工具节点 (Tool)

调用预定义或自定义工具。

```typescript
// 工具节点配置
- 工具选择：选择要使用的工具
- 工具参数：设置工具的输入参数
- 结果处理：配置工具输出的处理方式
```

#### 11.5.4 文档提取器节点 (Document Extractor)

从文档中提取结构化数据。

```typescript
// 文档提取器配置
- 文档来源：上传文件或变量引用
- 提取方式：表格提取、文本提取等
- 输出格式：JSON、CSV、纯文本等
```

## 12. 工作流组件调试和测试

### 12.1 单节点调试

Dify 工作流支持对单个节点进行调试，验证节点配置和行为：

```typescript
// 单节点运行
const runSingleNode = async (nodeId) => {
  // 设置输入值
  setInputs({
    message: 'Test input message',
    // 其他输入
  })
  
  // 运行单节点
  await handleRunSingleNode(nodeId)
  
  // 查看输出
  const nodeOutput = getNodeOutput(nodeId)
  console.log('Node output:', nodeOutput)
}
```

### 12.2 工作流模拟运行

可以在不实际执行工作流的情况下，模拟工作流的执行过程：

```typescript
// 模拟运行工作流
const simulateWorkflow = async () => {
  // 准备输入
  const inputs = {
    message: 'Test workflow input',
    // 其他输入
  }
  
  // 开始模拟
  setSimulationMode(true)
  await runWorkflowSimulation(inputs)
  
  // 查看模拟结果
  const simulationResult = getSimulationResult()
  console.log('Simulation result:', simulationResult)
  
  // 关闭模拟
  setSimulationMode(false)
}
```

## 13. 最佳实践和优化建议

### 13.1 工作流设计原则

1. **简单原则**：保持工作流简洁明了，避免过多的节点和复杂连接
2. **模块化设计**：将复杂功能拆分为多个简单节点
3. **错误处理**：为关键节点添加错误处理策略
4. **变量命名**：使用清晰、一致的变量命名约定
5. **可测试性**：设计便于测试的工作流结构

### 13.2 性能优化建议

1. **减少节点数量**：合并可以组合的功能，减少节点总数
2. **优化循环操作**：避免处理大量数据的循环
3. **缓存中间结果**：对重复使用的计算结果进行缓存
4. **减少远程调用**：尽量在一次调用中完成多个操作
5. **注意资源限制**：合理设置超时和资源限制

## 14. 工作流组件未来发展

Dify 工作流组件计划在未来引入更多高级功能，包括：

1. **工作流模板**：预设常用工作流模板
2. **可视化调试**：更丰富的可视化调试工具
3. **协作编辑**：支持多人同时编辑工作流
4. **版本控制**：工作流的版本管理和回滚
5. **更多集成节点**：支持更多外部系统和服务的集成
6. **性能优化**：优化大规模工作流的编辑和执行性能 