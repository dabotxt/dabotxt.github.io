---
title: 解决iOS分发Non-public API问题
abbrlink: 90338fix
date: 2026-01-29 10:38:20
tags: React Native
categories: React Native
cover: 'https://s1.ax1x.com/2022/05/10/OtyPTx.jpg'
top_img: 'https://s1.ax1x.com/2022/05/10/OtyvUP.jpg'
---

### 问题描述

在提交 iOS 应用到 TestFlight 或 App Store 时，遇到以下错误：

```
90338: Non-public API usage. The app references non-public selectors in Chainbox-QA: 
_isKeyDown, _modifiedInput, _modifierFlags. 

If method names in your source code match the private Apple APIs listed above, 
altering your method names will help prevent this app from being flagged in future submissions.
```

这是因为 React Native 的 `RCTKeyCommands.m` 和其他文件中使用了 Apple 的私有 API 选择器。

### 解决方案

在 `Podfile` 中添加 `post_install` 脚本，自动替换这些私有 API 名称。

#### 1. 首先定义 `find_and_replace` 函数

在 `Podfile` 顶部添加：

```ruby
def find_and_replace(dir, findstr, replacestr)
  Dir[dir].each do |name|
    text = File.read(name)
    replace = text.gsub(findstr, replacestr)
    if text != replace
      puts "Fix: " + name
      File.open(name, "w") { |file| file.puts replace }
      STDOUT.flush
    end
  end
  Dir[dir + '*/'].each(&method(:find_and_replace))
end
```

#### 2. 在 `post_install` 中调用替换

```ruby
post_install do |installer|
  # 替换 RCTKeyCommands.m 中的私有 API
  find_and_replace("./node_modules/react-native/React/Base/RCTKeyCommands.m",
                   "_modifierFlags",
                   "_modifierEventFlags")
  find_and_replace("./node_modules/react-native/React/Base/RCTKeyCommands.m",
                   "_modifiedInput",
                   "_modifiedEventInput")
  find_and_replace("./node_modules/react-native/React/Base/RCTKeyCommands.m",
                   "_isKeyDown",
                   "_isKeyEventDown")
  
  # 替换 RCTPackagerClient.h 中的私有 API
  find_and_replace("./node_modules/react-native/React/DevSupport/RCTPackagerClient.h",
                   "handleNotification",
                   "handlePackageNotification")
  
  # 替换 RCTPackagerConnection.mm 中的私有 API
  find_and_replace("./node_modules/react-native/React/DevSupport/RCTPackagerConnection.mm",
                   "handleNotification",
                   "handlePackageNotification")
  
  # 其他 post_install 配置...
end
```

### 完整 Podfile 示例

```ruby
def find_and_replace(dir, findstr, replacestr)
  Dir[dir].each do |name|
    text = File.read(name)
    replace = text.gsub(findstr, replacestr)
    if text != replace
      puts "Fix: " + name
      File.open(name, "w") { |file| file.puts replace }
      STDOUT.flush
    end
  end
  Dir[dir + '*/'].each(&method(:find_and_replace))
end

# ... 你的其他 Podfile 配置 ...

post_install do |installer|
  # 修复 Non-public API 问题
  find_and_replace("./node_modules/react-native/React/Base/RCTKeyCommands.m",
                   "_modifierFlags", "_modifierEventFlags")
  find_and_replace("./node_modules/react-native/React/Base/RCTKeyCommands.m",
                   "_modifiedInput", "_modifiedEventInput")
  find_and_replace("./node_modules/react-native/React/Base/RCTKeyCommands.m",
                   "_isKeyDown", "_isKeyEventDown")
  find_and_replace("./node_modules/react-native/React/DevSupport/RCTPackagerClient.h",
                   "handleNotification", "handlePackageNotification")
  find_and_replace("./node_modules/react-native/React/DevSupport/RCTPackagerConnection.mm",
                   "handleNotification", "handlePackageNotification")
end
```

### 使用步骤

1. 将上述代码添加到 `ios/Podfile`
2. 运行 `pod install`
3. 重新构建并提交到 TestFlight

### 注意事项

- 每次运行 `pod install` 时会自动执行替换
- 这个修复适用于 debug 模式上传到 TestFlight
- 如果更新了 React Native 版本，可能需要调整文件路径

### 相关链接

- [Apple Technical Support](http://developer.apple.com/support/technical/)
- [React Native GitHub Issues](https://github.com/facebook/react-native/issues)
