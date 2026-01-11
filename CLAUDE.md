# 小米智能家居极客版色温曲线生成器

## 项目概述
基于昼夜节律生成米家极客版公式，用于自动调节智能灯的色温和亮度。

## 米家极客版公式限制

### 支持的函数
参考 `极客版支持函数.md`：
- 数学：abs, pow, log, sin, cos, tan, asin, acos, atan, max, min, round, floor, ceil
- 时间：now(), year(), month(), date(), day(), hours(), minutes(), seconds()
- 常量：pi(), e()
- 随机：rand(), randint(x,y)

### 关键限制
- 不支持变量定义，所有计算必须内联
- 不支持条件语句（如 if/else），需用数学方式模拟

## 当前实现

### 核心算法
- 使用 sigmoid 叠加实现平滑过渡
- 公式格式：`floor(v0 + delta1/(1+pow(e(),-k*(t-mid1)/w1)) + ...)`

### 日出日落计算
```javascript
function calcSun(lat, angle = -0.833) {
    // angle: -0.833 日出日落, -6 民用曙暮光, -12 航海, -18 天文
    const day = Math.floor((new Date() - new Date(new Date().getFullYear(), 0, 0)) / 864e5);
    const r = lat * Math.PI / 180;
    const d = 23.44 * Math.sin(2 * Math.PI * (day - 81) / 365) * Math.PI / 180;
    const cosH = (Math.sin(angle * Math.PI / 180) - Math.sin(r) * Math.sin(d)) / (Math.cos(r) * Math.cos(d));
    const h = Math.acos(cosH) * 180 / Math.PI / 15;
    return { rise: 12 - h, set: 12 + h };
}
```
