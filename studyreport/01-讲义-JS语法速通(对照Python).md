# 讲义 01 · JavaScript 语法速通（对照 Python）

> Day 1 用。目标：**能读懂**这个产品里的 JS，不追求能默写。
> 你会 Python，所以我们用「Python 说法 → JS 说法」的对照来学，最快。

---

## 一、核心思路：JS 和 Python 是近亲

它们都是「动态类型、面向对象、解释执行」的语言，控制流（`if`/`for`/函数）思想一模一样。差别主要在**写法/标点**：

- Python 用**缩进**分层；JS 用**大括号 `{}`** 分层，语句结尾习惯加**分号 `;`**。
- Python 的 `dict`/`list` ≈ JS 的 `对象 {}`/`数组 []`。
- Python 的 f-string ≈ JS 的模板串（也可以用 `+` 拼接，本项目大量用 `+`）。

先记住一句话：**看到 `{}` 想到「代码块或字典」，看到 `[]` 想到「列表」，看到 `=>` 想到「lambda」。**

---

## 二、对照速查表（最常用的 20 个点）

| 你想做的事 | Python | JavaScript |
|---|---|---|
| 定义变量（不再变） | `PI = 3.14` | `const PI = 3.14;` |
| 定义变量（会变） | `x = 0` | `let x = 0;` |
| 打印 | `print(a)` | `console.log(a)` |
| 函数 | `def f(a, b): return a+b` | `function f(a, b){ return a+b; }` |
| 匿名函数/lambda | `lambda x: x*2` | `x => x*2` |
| 字典 | `{"a": 1, "b": 2}` | `{a: 1, b: 2}`（键名可不加引号） |
| 取字典值 | `d["a"]` | `d.a` 或 `d["a"]` |
| 列表 | `[1, 2, 3]` | `[1, 2, 3]` |
| 列表推导 | `[x*2 for x in xs]` | `xs.map(x => x*2)` |
| 过滤 | `[x for x in xs if x>0]` | `xs.filter(x => x>0)` |
| 求和 | `sum(xs)` | `xs.reduce((s,x)=>s+x, 0)` |
| 遍历 | `for x in xs:` | `xs.forEach(x => {...})` 或 `for(const x of xs)` |
| 带序号遍历 | `for i,x in enumerate(xs):` | `xs.forEach((x, i) => {...})` |
| 字符串拼接 | `f"年{y}"` 或 `"年"+str(y)` | `` `年${y}` `` 或 `"年"+y` |
| 幂 | `2 ** 3` | `Math.pow(2,3)` 或 `2 ** 3` |
| 取整/四舍五入 | `round(x)` | `Math.round(x)` |
| 最小/最大 | `min(a,b)` / `max(a,b)` | `Math.min(a,b)` / `Math.max(a,b)` |
| 判空/默认值 | `x or 默认` | `x || 默认` |
| 真/假/空 | `True/False/None` | `true/false/null`（还有 `undefined`） |
| 键是否存在 | `k in d` | `d[k] !== undefined` 或 `k in d` |

> 提醒一个坑：JS 里 `//` 是**注释**，不是整除；整除要用 `Math.floor(a/b)`。

---

## 三、对着真实代码读

### 例 1：一个最简单的函数（`rentcalc.js` 第 3 行）

```javascript
function r4(x){ return Math.round(x*10000)/10000; }
```

翻成 Python 就是：

```python
def r4(x):
    return round(x * 10000) / 10000   # 保留4位小数
```

一模一样，只是把 `def` 换成 `function`、加了 `{}` 和 `;`。全项目所有 `r4(...)` 都是「保留 4 位小数」。

### 例 2：箭头函数 + 求和（`rentcalc.js` 第 166 行）

```javascript
const sum = f => allYears.reduce((s,y)=>s+f(y), 0);
```

- `f => ...`：`f` 是参数，是个「函数」。整体是「接收一个函数 f，返回一个数」。
- `allYears.reduce((s,y)=>s+f(y), 0)`：从 0 开始，把每个年份 `y` 的 `f(y)` 累加起来。

对应 Python：

```python
def sum(f):
    return functools.reduce(lambda s, y: s + f(y), allYears, 0)
# 或更 Pythonic：
def sum(f):
    return builtins.sum(f(y) for y in allYears)
```

所以后面 `sum(y => income[y].total)` 意思就是「把每年的总收入加起来」。

### 例 3：对象 + 遍历赋值（`rentcalc.js` 第 22–37 行，收入计算）

```javascript
const income = {};
allYears.forEach(y=>{
  if(!isOp[y]){ income[y]={resi:0, park:0, other:0, total:0}; return; }
  const m = monthD[y];
  const resi = r4(p.area * resiRent[y] * resiOcc[y] * m / 10000);
  const park = r4(p.parkCount * p.parkPrice * parkOcc[y] * m * p.parkRatio / 10000);
  const other = (y===opStart)? r4(p.otherTotal||0) : 0;
  income[y]={resi, park, other, total:r4(resi+park+other)};
});
```

逐句读：
- `const income = {};`：建一个空「字典」，准备按年份存收入。对应 Python `income = {}`。
- `allYears.forEach(y=>{ ... })`：对每个年份 `y` 执行大括号里的逻辑。对应 `for y in allYears:`。
- `if(!isOp[y]){ ...; return; }`：如果这一年**不是**运营年（`!` 是「非」），收入全填 0，然后 `return` **提前结束这一次循环**（相当于 Python 的 `continue`）。
- `const other = (y===opStart)? r4(...) : 0;`：这是**三元表达式**，等价于 Python 的 `other = r4(...) if y==opStart else 0`。格式是 `条件 ? 成立时的值 : 否则的值`。
- `p.otherTotal||0`：如果 `p.otherTotal` 是空/0/undefined，就用 `0`。等价 Python `p.otherTotal or 0`。
- `income[y]={resi, park, other, total:...}`：`{resi, park}` 是简写，等价 `{resi: resi, park: park}`（键名和变量名相同可省略）。

### 例 4：字符串拼 HTML（`index.html` 里到处都是，如第 674 行）

```javascript
return '<div class="toc-item '+cls+'"><span class="num">'+String(i+1).padStart(2,'0')+'</span><span>'+s+'</span></div>';
```

这看着吓人，其实就是**用 `+` 把一堆字符串接起来，中间夹了几个变量**（`cls`、`i`、`s`），最终拼出一段 HTML 文本。对应 Python 的 f-string：

```python
return f'<div class="toc-item {cls}"><span class="num">{str(i+1).zfill(2)}</span><span>{s}</span></div>'
```

- `String(i+1)`：把数字转成字符串（≈ Python `str(i+1)`）。
- `.padStart(2,'0')`：左边补 0 到 2 位（≈ Python `.zfill(2)`），所以 `1` 显示成 `01`。

**这是理解整个前端的关键**：前端「画界面」的本质，就是**把状态数据拼成一大段 HTML 字符串**，讲义 02 会讲这段字符串怎么变成你看到的页面。

---

## 四、几个 JS 独有、但本项目常见的点

1. **`===` 而不是 `==`**：JS 里比较相等**要用三个等号 `===`**（严格相等，不做类型转换）。看到 `y===opStart` 就是「y 等于 opStart」。
2. **`async` / `await`**：函数前面有 `async`、里面有 `await` 的，是「异步函数」——通常在**等网络请求**。比如 `await fetch("/api/...")` = 「发个网络请求并等它回来」。对应 Python 的 `async def` / `await`，思想一样。
3. **`try{ ... }catch(e){ ... }`**：等价 Python 的 `try: ... except Exception as e: ...`。
4. **`.map` / `.filter` / `.forEach` / `.reduce` 链式调用**：这些是数组的「方法」，是 JS 里代替列表推导的主力，务必眼熟。
5. **`||` 和 `?.`**：`a || b` 取默认值；`a?.b` 是「安全取属性」（a 为空就不报错，返回 undefined），对应 Python 没有直接等价，理解成「防空」即可。

---

## 五、Day 1 自测题

对着答案前先自己答：

1. 把这句翻成 Python：`const arr = xs.filter(x=>x>0).map(x=>x*2);`
2. `const v = cond ? A : B;` 对应 Python 怎么写？
3. `d.a` 和 `d["a"]` 有区别吗？`k in d` 在 JS 里能用吗？
4. `p.otherTotal || 0` 在防什么？
5. 看 `rentcalc.js` 第 30–37 行，用中文说出「income[y] 里最终存了哪 4 个字段」。
6. `allYears.forEach(y=>{ ... return; ... })` 里的 `return` 起什么作用？（提示：不是结束整个函数）

<details>
<summary>参考答案</summary>

1. `arr = [x*2 for x in xs if x>0]`
2. `v = A if cond else B`
3. 大多数情况等价；`d.a` 更常用，但键名带特殊字符或是变量时用 `d["a"]`。`k in d` 在 JS 里也能用（判断键是否存在）。
4. 防止 `otherTotal` 为空/undefined 时参与计算出错，空则当 0。
5. `resi`（住宅收入）、`park`（车位收入）、`other`（其他收入）、`total`（三者之和）。
6. 提前结束**当前这一次**循环迭代，相当于 Python 的 `continue`（因为循环体是个箭头函数，`return` 只结束这个函数这一次调用）。
</details>
