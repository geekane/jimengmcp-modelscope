# Jimeng MCP 服务器

使用TypeScript实现的Model Context Protocol (MCP) 服务器项目，集成了即梦AI图像生成服务，通过逆向工程直接调用即梦官方API。

## 功能

- 基于TypeScript构建
- 使用tsup作为构建工具
- 实现了MCP协议，支持标准的stdio通信
- 直接调用即梦AI图像生成服务，无需第三方API
- 提供多种即梦模型的图像生成工具
- 支持多种图像参数调整，如尺寸、精细度、负面提示词等
- 支持图片混合/参考图生成（通过filePath参数，支持本地图片和网络图片）

## 环境配置

在使用此MCP服务前，需要配置以下环境变量：

### 获取JIMENG_API_TOKEN

1. 访问 [即梦AI官网](https://jimeng.jianying.com) 并登录账号
2. 按F12打开浏览器开发者工具
3. 在Application > Cookies中找到`sessionid`的值
4. 将找到的sessionid值配置为JIMENG_API_TOKEN环境变量

## 即梦AI图像生成

本MCP服务器直接调用即梦AI图像生成API，提供图像生成工具：

`generateImage` - 提交图像生成请求并返回图像URL列表
- 参数：
  - `prompt`：生成图像的文本描述（必填）
  - `filePath`：本地图片路径或图片URL（可选，若填写则为图片混合/参考图生成功能）
  - `model`：模型名称，可选值: jimeng-3.0, jimeng-2.1, jimeng-2.0-pro, jimeng-2.0, jimeng-1.4, jimeng-xl-pro（可选，默认为jimeng-2.1，图片混合时自动切换为jimeng-2.0-pro）
  - `width`：图像宽度，默认值：1024（可选）
  - `height`：图像高度，默认值：1024（可选）
  - `sample_strength`：精细度，默认值：0.5，范围0-1（可选）
  - `negative_prompt`：反向提示词，告诉模型不要生成什么内容（可选）

> **注意：**
> - `filePath` 支持本地绝对/相对路径和图片URL。
> - 若指定 `filePath`，将自动进入图片混合/参考图生成模式，底层模型自动切换为 `jimeng-2.0-pro`。
> - 网络图片需保证可公开访问。

### 图片混合/参考图生成功能

如需基于图片进行混合生成，只需传入`filePath`参数（支持本地路径或图片URL），即可实现图片风格融合、参考图生成等高级玩法。

#### 示例：

```javascript
// 参考图片混合生成
client.callTool({
  name: "generateImage",
  arguments: {
    prompt: "梵高风格的猫",
    filePath: "./test.png", // 本地图片路径
    sample_strength: 0.6
  }
});
```

或

```javascript
// 使用网络图片作为参考
client.callTool({
  name: "generateImage",
  arguments: {
    prompt: "未来城市",
    filePath: "https://example.com/your-image.png"
  }
});
```

### 支持的模型

服务器支持以下即梦AI模型：

- `jimeng-3.0`：即梦第三代模型，效果更好，支持更强的图像生成能力
- `jimeng-2.1`：即梦2.1版本模型，默认模型
- `jimeng-2.0-pro`：即梦2.0 Pro版本
- `jimeng-2.0`：即梦2.0标准版本
- `jimeng-1.4`：即梦1.4版本
- `jimeng-xl-pro`：即梦XL Pro特殊版本

### 使用示例

通过MCP协议调用图像生成功能：

```javascript
// 生成图像（文本生成）
client.callTool({
  name: "generateImage",
  arguments: {
    prompt: "一只可爱的猫咪在草地上",
    model: "jimeng-3.0",
    width: 1024,
    height: 1024,
    sample_strength: 0.7,
    negative_prompt: "模糊，扭曲，低质量"
  }
});

// 生成图像（图片混合/参考图生成）
client.callTool({
  name: "generateImage",
  arguments: {
    prompt: "未来城市",
    filePath: "https://example.com/your-image.png"
  }
});
```

## 响应格式

API将返回生成的图像URL数组，可以直接在各类客户端中显示：

```javascript
[
  "https://example.com/generated-image-1.jpg",
  "https://example.com/generated-image-2.jpg",
  "https://example.com/generated-image-3.jpg",
  "https://example.com/generated-image-4.jpg"
]
```

## 资源

服务器还提供了以下信息资源：

- `greeting://{name}` - 提供个性化问候
- `info://server` - 提供服务器基本信息
- `jimeng-ai://info` - 提供即梦AI图像生成服务的使用说明

## 常见问题

1. **图像生成失败**
   - 检查JIMENG_API_TOKEN是否正确配置
   - 登录即梦官网检查账号积分是否充足
   - 尝试更换提示词，避免敏感内容
   - 若为图片混合，检查filePath路径/URL是否有效、图片是否可访问
   - 网络图片建议使用https直链，避免防盗链/权限问题

2. **服务器无法启动**
   - 确保已安装所有依赖
   - 确保环境变量正确设置
   - 检查Node.js版本是否为14.0或更高

## 部署到ModelScope

本项目已适配ModelScope平台，可以直接部署到ModelScope的MCP服务器上。部署时需要：

1. 确保Dockerfile配置正确
2. 在ModelScope平台配置JIMENG_API_TOKEN环境变量
3. 按照ModelScope平台的部署流程进行部署

## 许可证

MIT