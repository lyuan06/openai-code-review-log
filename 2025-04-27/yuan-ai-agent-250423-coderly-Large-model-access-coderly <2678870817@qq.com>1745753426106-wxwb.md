```markdown
# 代码评审

## pom.xml

### 添加的依赖

你添加了以下依赖：

1. **langchain4j-community-dashscope (1.0.0-beta2)**:
   - 用于与灵积大模型（DashScope）的社区版集成。
   - 这个依赖可能是为了测试或实验目的，因为最新的版本通常是稳定的。

2. **spring-ai-ollama-spring-boot-starter (1.0.0-M6)**:
   - 用于与Ollama大模型集成。
   - 版本为M6，表示这是一个预发布版本，可能包含未稳定的特性。

### 评审建议

- 确保你了解1.0.0-M6版本的已知问题和限制。
- 考虑在生产环境中使用稳定版本的依赖。
- 如果使用Ollama，确保本地服务已经启动并运行在指定的`base-url`。

## OllamaAiInvoke.java

### 文件内容

```java
package com.coderly.yuanaiagent.demo.invoke;

import jakarta.annotation.Resource;
import org.springframework.ai.chat.messages.AssistantMessage;
import org.springframework.ai.chat.model.ChatModel;
import org.springframework.ai.chat.prompt.Prompt;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

/**
 * 使用 ollama 接入本地deepseek大模型
 */
@Component
public class OllamaAiInvoke implements CommandLineRunner {

    @Resource
    private ChatModel ollamaChatModel;

    @Override
    public void run(String... args) throws Exception {
        AssistantMessage output = ollamaChatModel.call(new Prompt("你是谁"))
                .getResult()
                .getOutput();
        System.out.println(output.getText());
    }
}
```

### 评审建议

- 这个类是一个`CommandLineRunner`，会在应用启动时执行，调用Ollama模型并打印输出。
- 确保`ollamaChatModel`的依赖已经正确配置，并且Ollama服务在本地运行。
- 可以考虑添加日志记录，以便于调试和监控。

## SpingAiInvoke.java

### 文件内容

```java
package com.coderly.yuanaiagent.demo.invoke;

import org.springframework.ai.chat.prompt.Prompt;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

-@Component
public class SpingAiInvoke implements CommandLineRunner {
}
```

### 评审建议

- 这个类是一个`CommandLineRunner`，但没有实现`run`方法。
- 如果这个类是废弃的，建议删除。如果仍然需要，可以实现`run`方法并添加相关逻辑。

## application.yml

### 修改内容

你修改了`application.yml`文件，主要变化如下：

1. 移除了对DashScope的配置：
   ```yaml
   - spring.ai.dashscope:
       api-key: sk-bf847947d9954670b4d92ef186a3425f
       chat:
         options:
           model: qwen-plus
   ```

2. 添加了Ollama的配置：
   ```yaml
   spring.ai.ollama:
     base-url: http://101.133.234.38:11434
     chat:
       model: deepseek-r1:1.5b
   ```

3. 为测试Ollama临时禁用了DashScope自动配置类：
   ```yaml
   spring.autoconfigure.exclude:
     - com.alibaba.cloud.ai.autoconfigure.dashscope.DashScopeAutoConfiguration
   ```

### 评审建议

- 确保`base-url`指向正确的Ollama服务地址。
- 检查`deepseek-r1:1.5b`模型是否已经在Ollama服务中可用。
- 禁用DashScope自动配置类是一个好的做法，可以避免不必要的冲突和配置错误。

## 总结

- 你成功地集成了Ollama大模型，并配置了相关的参数。
- 确保所有依赖和配置都正确无误，以避免运行时错误。
- 考虑在生产环境中使用稳定版本的依赖，并进行充分的测试。

```