graph TD
    subgraph InputLayer [1. 输入数据层 Input Data Layer]
        UId[用户ID + 观测点ID + 证书ID]
        CData[Context Data 真实活动/证书数据]
    end

    subgraph EngineLayer [2. 动态提示词引擎 Dynamic Prompt Engine]
        Hash[确定性哈希生成器 Deterministic Hash Generator]
        Router{上下文路由规则 Context Router}
        
        subgraph ResourcePool [核心弹药库 Resource Pool]
            SDB[(微场景模板库 Scenarios DB)]
            PMX[(人设与参数矩阵 Parameter Matrix)]
        end
        
        Selector[参数选择器 Parameter Selector]
        Guard[安全护栏与约束注入 Guardrails & Injection]
    end

    subgraph OutputLayer [3. 最终装配态 Assembled State]
        SysPrompt[System Prompt 系统指令剧本]
        UsrPrompt[User Prompt 结构化用户素材]
    end

    subgraph LLMLayer [4. 大模型调用层 LLM Layer]
        API((LLM API<br/>参数锁定：Seed, Temperature))
    end

    %% 数据流转关系
    UId -->|生成唯一不变的 Seed| Hash
    CData -->|解析数据结构| Router
    
    Hash -->|驱动取模运算| Selector
    Router -->|冷启动/无数据| SDB
    Router -->|有数据| Selector
    
    SDB -->|提供分镜动作脚本| Selector
    PMX -->|提供语气/切入点/阻力变量| Selector
    
    Selector -->|将选定参数按剧本拼装| Guard
    Guard -->|注入字数/防跑题/反幻觉限制| SysPrompt
    Guard -->|注入字数/防跑题/反幻觉限制| UsrPrompt
    
    SysPrompt --> API
    UsrPrompt --> API
