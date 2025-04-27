```markdown
### 代码评审

#### pom.xml
```xml
diff --git a/pom.xml b/pom.xml
index 774c621..d875fc6 100644
--- a/pom.xml
+++ b/pom.xml
@@ -78,6 +78,12 @@
             <artifactId>langchain4j-community-dashscope</artifactId>
             <version>1.0.0-beta2</version>
         </dependency>
+        <!-- https://java2ai.com/docs/1.0.0-M6.1/models/ollama/ -->
+        <dependency>
+            <groupId>org.springframework.ai</groupId>
+            <artifactId>spring-ai-ollama-spring-boot-starter</artifactId>
+            <version>1.0.0-M6</version>
+        </dependency>
```
**评审解释**:
- 添加了 `spring-ai-ollama-spring-boot-starter` 依赖，用于集成 Ollama 大模型。这是一个很好的扩展，可以支持本地大模型的调用，提供更多的灵活性。

#### OllamaAiInvoke.java
```java
diff --git a/src/main/java/com/coderly/yuanaiagent/demo/invoke/OllamaAiInvoke.java b/src/main/java/com/coderly/yuanaiagent/demo/invoke/OllamaAiInvoke.java
new file mode 100644
index 0000000..fadd794
--- /dev/null
+++ b/src/main/java/com/coderly/yuanaiagent/demo/invoke/OllamaAiInvoke.java
@@ -0,0 +1,26 @@
+package com.coderly.yuanaiagent.demo.invoke;
+
+import jakarta.annotation.Resource;
+import org.springframework.ai.chat.messages.AssistantMessage;
+import org.springframework.ai.chat.model.ChatModel;
+import org.springframework.ai.chat.prompt.Prompt;
+import org.springframework.boot.CommandLineRunner;
+import org.springframework.stereotype.Component;
+
+/**
+ * 使用 ollama 接入本地deepseek大模型
+ */
+@Component
+public class OllamaAiInvoke implements CommandLineRunner {
+
+    @Resource
+    private ChatModel ollamaChatModel;
+
+    @Override
+    public void run(String... args) throws Exception {
+        AssistantMessage output = ollamaChatModel.call(new Prompt("你是谁"))
+                .getResult()
+                .getOutput();
+        System.out.println(output.getText());
+    }
+}
```
**评审解释**:
- 新增了一个 `OllamaAiInvoke` 类，用于通过 Ollama 接入本地 `deepseek` 大模型。这个类实现了 `CommandLineRunner` 接口，在应用启动时会执行 `run` 方法，调用大模型并打印输出。
- 使用了 `@Resource` 注解注入 `ChatModel` 对象，这是 Spring AI 提供的用于与聊天模型交互的接口。
- 通过 `ollamaChatModel.call(new Prompt("你是谁"))` 调用大模型，并打印输出结果。

#### SpingAiInvoke.java
```java
diff --git a/src/main/java/com/coderly/yuanaiagent/demo/invoke/SpingAiInvoke.java b/src/main/java/com/coderly/yuanaiagent/demo/invoke/SpingAiInvoke.java
index 4b765b0..bc4262a 100644
--- a/src/main/java/com/coderly/yuanaiagent/demo/invoke/SpingAiInvoke.java
+++ b/src/main/java/com/coderly/yuanaiagent/demo/invoke/SpingAiInvoke.java
@@ -6,7 +6,7 @@ import org.springframework.ai.chat.prompt.Prompt;
 import org.springframework.boot.CommandLineRunner;
 import org.springframework.stereotype.Component;
 
-@Component
+//@Component
 public class SpingAiInvoke implements CommandLineRunner {
 
     /**
diff --git a/src/main/resources/application.yml b/src/main/resources/application.yml
index e852616..3c69790 100644
--- a/src/main/resources/application.yml
+++ b/src/main/resources/application.yml
@@ -3,11 +3,20 @@ spring:
     name: yuan-ai-agent
   # 整合灵积大模型
   ai:
-    dashscope:
-      api-key: sk-bf847947d9954670b4d92ef186a3425f
+#    dashscope:
+#      api-key: sk-bf847947d9954670b4d92ef186a3425f
+#      chat:
+#        options:
+#          model: qwen-plus
+    ollama:
+      base-url: http://101.133.234.38:11434
       chat:
-        options:
-          model: qwen-plus
+        model: deepseek-r1:1.5b
+  # 为测试ollama临时禁用dashscope自动配置类
+  autoconfigure:
+    exclude:
+      - com.alibaba.cloud.ai.autoconfigure.dashscope.DashScopeAutoConfiguration
+
 
 server:
   port: 8123
```
**评审解释**:
- `SpingAiInvoke` 类的 `@Component` 注解被注释掉了，这意味着这个类不会被 Spring 容器管理，也不会在应用启动时执行 `run` 方法。
- 在 `application.yml` 中，移除了与 DashScope 相关的配置，并添加了 Ollama 的配置，包括 `base-url` 和 `model`。这表明应用将使用 Ollama 大模型而不是 DashScope。
- 添加了 `autoconfigure.exclude` 配置，用于临时禁用 DashScope 的自动配置类，确保应用只使用 Ollama 大模型。

### 总结
- 通过添加 `spring-ai-ollama-spring-boot-starter` 依赖和 `OllamaAiInvoke` 类，应用现在可以接入本地 Ollama 大模型，提供更多的灵活性。
- 在 `application.yml` 中进行了相应的配置调整，确保应用使用 Ollama 大模型，并禁用了 DashScope 的自动配置。
- `SpingAiInvoke` 类的 `@Component` 注解被注释掉，这可能是因为暂时不需要使用 Spring AI 的其他功能。

这些改动使得应用更加灵活，能够支持本地大模型的调用，同时也提供了更多的配置选项。
```