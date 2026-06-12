# 架构设计模块

> 产品经理核心能力之二：系统架构设计

---

## 适用场景

- 系统整体架构规划
- 功能模块划分与关系梳理
- 技术选型建议

---

## 设计框架

### 1. 功能结构图

```
系统
├── 模块A
│   ├── 功能A1
│   └── 功能A2
├── 模块B
│   ├── 功能B1
│   └── 功能B2
└── 模块C
```

### 2. 业务流程图

```mermaid
graph TD
    A[开始] --> B{判断}
    B -->|是| C[处理1]
    B -->|否| D[处理2]
    C --> E[结束]
    D --> E
```

### 3. 状态流转图

```mermaid
stateDiagram-v2
    [*] --> 状态1
    状态1 --> 状态2 : 触发事件
    状态2 --> 状态3 : 触发事件
    状态3 --> [*]
```

### 4. ER图设计

```mermaid
erDiagram
    实体A ||--o{ 实体B : 1:N
    实体B }o--|| 实体C : N:1
```

### 5. 架构分层

```
表现层（前端/移动端）
    ↓
接入层（网关/负载均衡）
    ↓
接口层（Controller/API）
    ↓
业务层（Service/Domain）
    ↓
持久层（Repository/Mapper）
    ↓
数据层（MySQL/Redis/ES）
```

---

## 状态流转图模板

### 简单状态流转

```mermaid
stateDiagram-v2
    [*] --> 草稿 : 创建
    草稿 --> 待审核 : 提交审核
    待审核 --> 审核中 : 开始审核
    审核中 --> 已通过 : 审批通过
    审核中 --> 已驳回 : 审批驳回
    已驳回 --> 待审核 : 重新提交
    已通过 --> 已完成 : 确认完成
    草稿 --> 已作废 : 作废
    已驳回 --> 已作废 : 作废
    已完成 --> [*]
    已作废 --> [*]
```

### 复杂状态流转（含子状态）

```mermaid
stateDiagram-v2
    [*] --> 待处理 : 创建
    
    待处理 --> 处理中 : 开始处理
    state 处理中 {
        [*] --> 初审
        初审 --> 复审 : 初审通过
        复审 --> 终审 : 复审通过
        初审 --> [*] : 初审驳回
        复审 --> [*] : 复审驳回
    }
    
    处理中 --> 已完成 : 终审通过
    处理中 --> 已驳回 : 驳回
    已驳回 --> 待处理 : 重新提交
    已完成 --> [*]
```

---

## 时序图模板

### 用户操作时序图

```mermaid
sequenceDiagram
    actor User as 用户
    participant FE as 前端
    participant BE as 后端
    participant DB as 数据库

    User->>FE: 点击按钮
    FE->>BE: 发送请求
    BE->>DB: 查询数据
    DB-->>BE: 返回数据
    BE-->>FE: 返回结果
    FE-->>User: 展示结果
```

### 审批流程时序图

```mermaid
sequenceDiagram
    actor Applicant as 申请人
    actor Reviewer as 审核人
    participant System as 系统
    participant DB as 数据库

    Applicant->>System: 提交申请
    System->>DB: 保存申请（状态=待审核）
    System->>Reviewer: 发送审核通知
    
    Reviewer->>System: 审核通过
    System->>DB: 更新状态（状态=已通过）
    System->>Applicant: 发送结果通知
```

---

## 类图模板

```mermaid
classDiagram
    class Entity {
        +Long id
        +String name
        +String status
        +DateTime createTime
        +DateTime updateTime
    }
    
    class EntityA {
        +String field1
        +String field2
        +method1()
    }
    
    class EntityB {
        +String field3
        +String field4
        +method2()
    }
    
    Entity <|-- EntityA
    Entity <|-- EntityB
    EntityA "1" --> "N" EntityB : contains
```

---

## ER图模板

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : "is in"
    
    USER {
        bigint id PK
        varchar username
        varchar email
        datetime created_at
    }
    
    ORDER {
        bigint id PK
        bigint user_id FK
        decimal total_amount
        varchar status
        datetime created_at
    }
    
    ORDER_ITEM {
        bigint id PK
        bigint order_id FK
        bigint product_id FK
        int quantity
        decimal unit_price
    }
    
    PRODUCT {
        bigint id PK
        varchar name
        decimal price
        int stock
    }
```

---

## 输出物

- 功能结构说明书
- 业务流程图
- 状态流转图
- 时序图
- 类图
- ER图
- 技术架构图
