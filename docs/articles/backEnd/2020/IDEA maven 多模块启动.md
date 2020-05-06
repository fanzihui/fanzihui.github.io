---
title: IDEA maven 多模块启动
date: 2020-03-12
author: 范了饭饭
categories:
 - backEnd
tags: 
 - Maven
---

## 1. 找到.idea下面的workspace.xml

在文件里添加以下代码

```xml
<component name="RunDashboard">
  <option name="ruleStates">
    <list>
      <RuleState>
        <option name="name" value="ConfigurationTypeDashboardGroupingRule" />
      </RuleState>
      <RuleState>
        <option name="name" value="StatusDashboardGroupingRule" />
      </RuleState>
    </list>
  </option>
  <option name="configurationTypes">
    <set>
      <option value="SpringBootApplicationConfigurationType" />
    </set>
  </option>
</component>
```

重新启动 IDEA, 打开项目即可



cb5e9a84c6aacfe880d90d65538bf5510df19a4b 


https://leecason.github.io/


https://fanzihui.github.io/