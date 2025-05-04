### 代码评审

#### pom.xml

在 `pom.xml` 文件中，你添加了两个依赖项：

1. **langchain4j-community-dashscope**:
   - 依赖项 `langchain4j-community-dashscope` 版本为 `1.0.0-beta2`。
   - 这个依赖项可能是为了集成灵积大模型（DashScope）的功能。

2. **spring-ai-ollama-spring-boot-starter**:
   - 依赖项 `spring-ai-ollama-spring-boot-starter` 版本为 `1.0.0-M6`。
   - 这个依赖项是为了集成 Ollama 大模型的功能。

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-ollama-spring-boot-starter</artifactId>
    <version>1.0.0-M6</version>
</dependency>
```

#### OllamaAiInvoke.java

`OllamaAiInvoke.java` 是一个新的类，用于使用 Ollama 接入本地 DeepSeek 大模型。

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

**评审**:
- 类使用 `@Component` 注解，表明它是一个 Spring 组件。
- 实现 `CommandLineRunner` 接口，在应用启动时执行 `run` 方法。
- 使用 `@Resource` 注解注入 `ChatModel` 对象 `ollamaChatModel`。
- 在 `run` 方法中，使用 Ollama 模型生成回复并打印输出。

#### SpingAiInvoke.java

`SpingAiInvoke.java` 类的实现被注释掉了。

```java
package com.coderly.yuanaiagent.demo.invoke;

import org.springframework.ai.chat.prompt.Prompt;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class SpingAiInvoke implements CommandLineRunner {
    /**
     * 这里原本有代码，但现在被注释掉了
     */
}
```

**评审**:
- 类使用 `@Component` 注解，表明它是一个 Spring 组件。
- 实现 `CommandLineRunner` 接口，但在当前代码中被注释掉了。
- 可以考虑删除这个类或重新启用并实现相关功能。

#### application.yml

`application.yml` 配置文件进行了以下修改：

1. 移除了与 DashScope 相关的配置。
2. 添加了与 Ollama 相关的配置。
3. 为测试 Ollama 临时禁用了 DashScope 自动配置类。

```yaml
spring:
  name: yuan-ai-agent
  ai:
    ollama:
      base-url: http://101.133.234.38:11434
      chat:
        model: deepseek-r1:1.5b
  autoconfigure:
    exclude:
      - com.alibaba.cloud.ai.autoconfigure.dashscope.DashScopeAutoConfiguration

server:
  port: 8123
```

**评审**:
- 移除了与 DashScope 相关的配置，表明项目现在主要使用 Ollama 大模型。
- 添加了 Ollama 的基础 URL 和模型配置。
- 使用 `autoconfigure.exclude` 属性禁用了 DashScope 的自动配置类，避免冲突。

### 总结

你的代码修改主要集中在集成 Ollama 大模型，并移除了与 DashScope 相关的配置。`OllamaAiInvoke.java` 类实现了使用 Ollama 模型生成回复的功能，而 `SpingAiInvoke.java` 类的实现被注释掉了。`application.yml` 配置文件也进行了相应的调整，以支持 Ollama 大模型的使用。整体上，这些修改表明你正在将项目从使用 DashScope 大模型转向使用 Ollama 大模型。