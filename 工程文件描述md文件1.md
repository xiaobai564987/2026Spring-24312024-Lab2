# CoreVisionKitOCR 工程文件描述

## 项目结构概览

### 顶级目录结构
```
CoreVisionKitOCR-master/
├── AppScope/                    # 应用全局配置目录
├── entry/                       # 主模块目录
├── hvigor/                      # 构建缓存目录
├── .hvigor/                     # Hvigor工具相关目录
├── .idea/                       # IDE配置文件目录
├── oh_modules/                  # 依赖模块目录
├── screenshots/                 # 截图目录
├── build-profile.json5          # 全局构建配置文件
├── code-linter.json5           # 代码检查配置
├── hvigorfile.ts               # 构建脚本
├── LICENSE                      # Apache 2.0许可证
├── oh-package.json5            # 项目依赖配置
├── oh-package-lock.json5       # 依赖锁文件
├── README.md                   # 项目说明文档
└── README.en.md                # 英文说明文档
```

## 核心配置文件分析

### 1. AppScope/app.json5 - 应用配置
```json5
{
  "app": {
    "bundleName": "com.example.corevisionkitocr",  // 应用包名
    "vendor": "example",                           // 厂商名称
    "versionCode": 1000000,                        // 版本代码
    "versionName": "1.0.0",                        // 版本名称
    "icon": "$media:layered_image",                // 应用图标
    "label": "$string:app_name"                    // 应用显示名称
  }
}
```

**关键信息**：
- 应用标识：`com.example.corevisionkitocr`
- 版本：1.0.0 (版本代码1000000)
- 使用分层图标资源

### 2. entry/src/main/module.json5 - 模块配置
```json5
{
  "module": {
    "name": "entry",                              // 模块名称
    "type": "entry",                              // 入口模块类型
    "description": "$string:module_desc",         // 模块描述
    "mainElement": "EntryAbility",                // 主Ability
    "deviceTypes": ["phone"],                     // 支持设备类型：手机
    "deliveryWithInstall": true,                  // 随安装包分发
    "installationFree": false,                    // 非免安装应用
    "pages": "$profile:main_pages",               // 页面配置文件
    
    "abilities": [{
      "name": "EntryAbility",                     // Ability名称
      "srcEntry": "./ets/entryability/EntryAbility.ets", // 源代码路径
      "description": "$string:EntryAbility_desc", // Ability描述
      "icon": "$media:layered_image",             // Ability图标
      "label": "$string:EntryAbility_label",      // Ability显示标签
      "startWindowIcon": "$media:startIcon",      // 启动窗口图标
      "startWindowBackground": "$color:start_window_background", // 启动窗口背景
      "exported": true,                           // 可被外部调用
      "skills": [{
        "entities": ["entity.system.home"],       // 实体：系统主页
        "actions": ["ohos.want.action.home"]      // 动作：主页
      }]
    }],
    
    "extensionAbilities": [{
      "name": "EntryBackupAbility",               // 备份Ability名称
      "srcEntry": "./ets/entrybackupability/EntryBackupAbility.ets", // 源代码路径
      "type": "backup",                           // 类型：备份
      "exported": false,                          // 不可被外部调用
      "metadata": [{
        "name": "ohos.extension.backup",          // 备份扩展
        "resource": "$profile:backup_config"      // 备份配置资源
      }]
    }]
  }
}
```

**关键信息**：
- 单Ability架构，支持备份功能
- 仅支持手机设备
- 包含主页启动技能配置

### 3. entry/src/main/ets/entryability/EntryAbility.ts - 入口Ability
```typescript
import { AbilityConstant, ConfigurationConstant, UIAbility, Want } from '@kit.AbilityKit';
import { hilog } from '@kit.PerformanceAnalysisKit';
import { window } from '@kit.ArkUI';
import { BusinessError } from '@kit.BasicServicesKit';

export default class EntryAbility extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    try {
      // 强制设置为浅色模式
      this.context.getApplicationContext().setColorMode(ConfigurationConstant.ColorMode.COLOR_MODE_LIGHT);
    } catch (error) {
      // 错误处理
    }
    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onCreate');
  }

  onDestroy(): void {
    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onDestroy');
  }

  onWindowStageCreate(windowStage: window.WindowStage): void {
    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onWindowStageCreate');
    
    // 加载主页面
    windowStage.loadContent('pages/Index', (err) => {
      if (err.code) {
        hilog.error(DOMAIN, 'testTag', 'Failed to load the content. Cause: %{public}s', JSON.stringify(err));
        return;
      }
      hilog.info(DOMAIN, 'testTag', 'Succeeded in loading the content.');
    });
  }

  onWindowStageDestroy(): void {
    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onWindowStageDestroy');
  }

  onForeground(): void {
    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onForeground');
  }

  onBackground(): void {
    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onBackground');
  }
}
```

**关键信息**：
- 使用ArkTS开发的UIAbility
- 强制应用使用浅色模式
- 完整生命周期管理
- 集成日志记录功能

### 4. oh-package.json5 - 依赖配置
```json5
{
  "modelVersion": "5.1.1",                      // HarmonyOS API版本
  "description": "Please describe the basic information.", // 项目描述
  "dependencies": {},                           // 运行时依赖（空）
  "devDependencies": {                          // 开发依赖
    "@ohos/hypium": "1.0.21",                   // 测试框架
    "@ohos/hamock": "1.0.0"                     // Mock框架
  }
}
```

**关键信息**：
- HarmonyOS API版本：5.1.1
- 仅包含开发依赖（测试框架）
- 无运行时依赖，说明主要使用系统内置Kit

### 5. build-profile.json5 - 构建配置
```json5
{
  "app": {
    "signingConfigs": [],                        // 签名配置（空）
    "products": [{
      "name": "default",                         // 产品名称
      "signingConfig": "default",                // 签名配置名称
      "targetSdkVersion": "5.1.1(19)",           // 目标SDK版本
      "compatibleSdkVersion": "5.1.1(19)",       // 兼容SDK版本
      "runtimeOS": "HarmonyOS",                  // 运行时操作系统
      "buildOption": {
        "strictMode": {
          "caseSensitiveCheck": true,            // 大小写敏感检查
          "useNormalizedOHMUrl": true            // 使用规范化OHM URL
        }
      }
    }],
    "buildModeSet": [{                           // 构建模式
      "name": "debug"                            // 调试模式
    }, {
      "name": "release"                          // 发布模式
    }]
  },
  "modules": [{                                  // 模块配置
    "name": "entry",                             // 模块名称
    "srcPath": "./entry",                        // 源代码路径
    "targets": [{
      "name": "default",                         // 目标名称
      "applyToProducts": ["default"]             // 应用的产品
    }]
  }]
}
```

**关键信息**：
- 目标SDK：5.1.1(19)
- 兼容SDK：5.1.1(19)
- 运行时：HarmonyOS
- 支持debug和release两种构建模式

## 项目技术栈总结

### 开发语言与框架
- **开发语言**: ArkTS (基于TypeScript的HarmonyOS开发语言)
- **UI框架**: ArkUI
- **API版本**: HarmonyOS 5.1.1 (API 19)

### 核心Kit依赖
从EntryAbility.ts中分析使用的Kit：
1. **AbilityKit**: UIAbility生命周期管理
2. **PerformanceAnalysisKit**: 性能分析和日志记录
3. **BasicServicesKit**: 基础服务支持

### 构建工具
- **Hvigor**: HarmonyOS构建工具
- **Node.js**: 依赖管理

### 开发工具
- **DevEco Studio**: HarmonyOS官方IDE
- **Hypium**: 测试框架 (v1.0.21)
- **Hamock**: Mock框架 (v1.0.0)

## 项目特点

### 架构特点
1. **单模块架构**: 仅包含一个entry模块
2. **单Ability设计**: 使用一个UIAbility作为应用入口
3. **备份支持**: 包含备份扩展Ability
4. **浅色模式**: 强制应用使用浅色主题

### 配置特点
1. **最小化依赖**: 无外部运行时依赖，主要使用系统Kit
2. **严格模式**: 启用大小写敏感检查和规范化URL
3. **完整生命周期**: 实现所有UIAbility生命周期方法
4. **错误处理**: 完善的异常捕获和日志记录

### 开发特点
1. **测试友好**: 集成测试和Mock框架
2. **日志完善**: 使用hilog进行详细日志记录
3. **配置驱动**: 通过JSON5配置文件管理应用行为

## 已知限制

1. **设备限制**: 仅支持手机设备
2. **主题限制**: 强制使用浅色模式
3. **依赖限制**: 无外部依赖，完全基于HarmonyOS原生Kit
4. **签名配置**: 当前未配置签名信息，需要手动配置

## 开发建议

1. **签名配置**: 需要在build-profile.json5中配置签名信息
2. **资源管理**: 注意AppScope和entry模块的资源文件组织
3. **测试开发**: 充分利用集成的Hypium和Hamock框架
4. **日志优化**: 根据实际需求调整日志级别和输出

---

*文档生成时间: 2026-05-13*
*基于项目核心配置文件分析生成*