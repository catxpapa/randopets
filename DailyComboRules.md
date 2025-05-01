# 日期对应公式

好的，我们来将之前设计的规则转换为 JavaScript 公式，并将纪元（Epoch）设置为 2020 年 1 月 1 日。

**1. 计算自纪元以来的天数 (JavaScript 函数)**

这个函数接收一个 `Date` 对象，计算它距离 2020 年 1 月 1 日有多少天。

```javascript
/**
 * 计算给定日期距离 2020 年 1 月 1 日的天数。
 * @param {Date} targetDate - 目标日期对象。
 * @returns {number} 自 2020-01-01 以来的天数（向下取整）。
 */
function getDaysSinceEpoch(targetDate) {
  // 纪元日期：2020年1月1日 UTC
  const epochDate = new Date(Date.UTC(2020, 0, 1)); // 月份从0开始，所以0代表1月

  // 确保目标日期也在 UTC 下比较，避免时区问题
  const targetUtc = Date.UTC(targetDate.getUTCFullYear(), targetDate.getUTCMonth(), targetDate.getUTCDate());

  // 计算毫秒差
  const diffMilliseconds = targetUtc - epochDate.getTime();

  // 转换为天数 (1天 = 24 * 60 * 60 * 1000 毫秒)
  const diffDays = Math.floor(diffMilliseconds / (1000 * 60 * 60 * 24));

  return diffDays;
}
```

**2. 定义常量和公式 (JavaScript)**

这里我们定义类别数量、质数乘数，并实现计算索引的函数。

```javascript
// --- 常量定义 ---
const Ns = 16; // 种族/生物 数量 (S0-S15) 
const Na = 13; // 能力/属性 数量 (A0-A12) 
const Nc = 10; // 职业/身份 数量 (C0-C9) 

const p1 = 17; // 种族乘数
const p2 = 23; // 能力乘数
const p3 = 31; // 职业乘数

// --- 索引计算公式 ---

/**
 * 根据日期计算种族索引 (0-15)。
 * @param {Date} date - 目标日期。
 * @returns {number} 种族索引。
 */
function getSpeciesIndex(date) {
  const daysSinceEpoch = getDaysSinceEpoch(date);
  const year = date.getUTCFullYear();
  // 使用 Math.abs 确保结果非负，因为 JS 的 % 对负数结果可能为负
  const index = Math.abs(daysSinceEpoch * p1 + (year % Ns)) % Ns;
  return index;
}

/**
 * 根据日期计算能力索引 (0-12)。
 * @param {Date} date - 目标日期。
 * @returns {number} 能力索引。
 */
function getAbilityIndex(date) {
  const daysSinceEpoch = getDaysSinceEpoch(date);
  const year = date.getUTCFullYear();
  const index = Math.abs(daysSinceEpoch * p2 + (year % Na)) % Na;
  return index;
}

/**
 * 根据日期计算职业索引 (0-9)。
 * @param {Date} date - 目标日期。
 * @returns {number} 职业索引。
 */
function getProfessionIndex(date) {
  const daysSinceEpoch = getDaysSinceEpoch(date);
  const year = date.getUTCFullYear();
  const index = Math.abs(daysSinceEpoch * p3 + (year % Nc)) % Nc;
  return index;
}

// --- 示例用法 ---
const today = new Date(); // 获取当前日期
const speciesIndex = getSpeciesIndex(today);
const abilityIndex = getAbilityIndex(today);
const professionIndex = getProfessionIndex(today);

console.log(`今天的日期: ${today.toLocaleDateString()}`);
console.log(`自 2020-01-01 天数: ${getDaysSinceEpoch(today)}`);
console.log(`种族索引 (S): ${speciesIndex}`); // 0 代表问号, 1-15 代表 S1-S15
console.log(`能力索引 (A): ${abilityIndex}`); // 0 代表问号, 1-12 代表 A1-A12
console.log(`职业索引 (C): ${professionIndex}`); // 0 代表问号, 1-9 代表 C1-C9

// 你可以将这些索引映射回具体的图标数据
// 例如: const selectedSpecies = speciesData[speciesIndex];
```

**代码说明：**

* `getDaysSinceEpoch`: 计算核心的天数差，使用 UTC 来避免时区问题。

* `getSpeciesIndex`, `getAbilityIndex`, `getProfessionIndex`: 分别实现了三个类别的索引计算公式。

* `Math.abs(...) % N`: 使用 `Math.abs` 确保取模运算前的数字是正数，这样 `%` 运算符在 JavaScript 中能稳定地得到 `0` 到 `N-1` 之间的非负结果。

* **索引映射:** 计算出的索引 `0` 对应每个类别的“问号”图标，索引 `i > 0` 对应编号为 `i` 的图标（例如，`speciesIndex = 5` 对应 S5 图标）。

你可以将这些 JavaScript 函数集成到你的网页或应用中，根据用户选择的日期或当前日期来生成确定的每日图标组合。