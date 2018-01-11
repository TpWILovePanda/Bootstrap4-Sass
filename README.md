# Bootstrap & Sass

使用內建的 Sass 變數客製化 Bootstrap 4，套用全域的樣式風格及主題、元件的調整。

## 安裝流程

```bash
# 檢查 node 版本
$ node -v

# 安裝 gulp 到 全域環境(-g) gulp

# Windows 版
$ npm install gulp -g

# Mac 版
$ sudo npm install gulp -g

# 安裝套件非常多，所以需要安裝時間
$ npm install

# 執行啟動
$ gulp

# 結束
Windows 版 & Mac 版: Ctrl + C

# 直接將檔案部屬到 GitHub 上
$ gulp deploy
```

## 資料架構

完整的檔案於 `source` 資料夾，`public` 資料夾為編譯後的檔案，`node_modules` 資料夾是 `npm install` 所安裝的檔案。

```text
your-project/
├── node_modules
│   └── bootstrap
│       ├── js
│       └── scss
├── public
│   ├── javascripts
│   └── stylesheets
└── source
    └── stylesheets
        ├── components
        │   └── _custom-components.scss
        └── helpers
            └── _custom-variables.scss
```

---

## [以下資料來源參考至 Bootstrap4 繁體中文手冊](http://bootstrap.hexschool.com/docs/4.0/getting-started/theming/)

## 簡介

在 Bootstrap 3 中主題更動是由 LESS 的變數覆寫、自訂 CSS 及分離的主題樣式表，這些包含在我們的 Dist 文件中，通過一些加強，現在可以完全重新設計 Bootstrap 3 的外觀無需修改核心文件，Bootstrap 提供一種熟悉但略有不同的作法。

現在主題由 Sass 變數、Sass Map 和自定義 CSS 完成，沒有專門的主題樣式表；但你也可以啟用內置主題來增加漸層、陰影等。

## Sass

利用我們的 Sass 原始檔來調整變數、Sass maps、mixins 還有其它等等。

### 檔案結構

盡可能避免修改 Bootstrap 核心文件。對於 Sass 來說，這是創建你的樣式表並導入 Bootstrap 核心，這便於你修改並擴增它。假設你使用套件管理工具像是 npm，那麼文件結構應該如下所示：

```text
your-project/
├── scss
│   └── custom.scss
└── node_modules/
    └── bootstrap
        ├── js
        └── scss
```

如果已經下載了原始檔案，並且沒有使用套件管理工具，那麼將需要手動設置類似於該結構的東西，使 Bootstrap 的原始碼與你自己的文件分開。

```text
your-project/
├── scss
│   └── custom.scss
└── bootstrap/
    ├── js
    └── scss
```

在您的 `custom.scss` 中，您將導入 Bootstrap 的 Sass 原始檔案。您有兩個選擇：包括所有 Bootstrap 或 選擇您需要的部分。我們鼓勵後者，但請注意，我們的組件有一些要求和相依性。您還需要為我們的插件加入一些 JavaScript。

```scss
// Custom.scss
// Option A: Include all of Bootstrap

@import "node_modules/bootstrap/scss/bootstrap";
```

```scss
// Custom.scss
// Option B: Include parts of Bootstrap

// Required
@import "node_modules/bootstrap/scss/functions";
@import "node_modules/bootstrap/scss/variables";
@import "node_modules/bootstrap/scss/mixins";

// Optional
@import "node_modules/bootstrap/scss/reboot";
@import "node_modules/bootstrap/scss/type";
@import "node_modules/bootstrap/scss/images";
@import "node_modules/bootstrap/scss/code";
@import "node_modules/bootstrap/scss/grid";
```

使用該設置，您可以開始修改 `custom.scss` 中的任何 Sass 變數和 Sass maps。您還可以依據需求開始在 `// Optional` 部分下增加 Bootstrap 的部分元件。

### 預設變數

Bootstrap 4 中的每個 Sass變數都包含 `!default`，允許您在自己的 Sass 中覆蓋變數的預設值，而無需修改 Bootstrap 的原始碼。 根據需要複製和貼上變數，修改它們的值並刪除 `!default`。如果你給予一個變數值，那麼它將不會被 Bootstrap 中的預設值重複定義。

相同 Sass 文件中的變數覆蓋可以在預設變數之前或之後。但是當覆寫 Sass 文件時，您的新變數必須在導入 Bootstrap 的 Sass 文件之前。

這是一個例子，通過 npm 導入和編譯 Bootstrap 時，可以更改 `<body>` 的 `background-color` 和 `color`。

```scss
// Your variable overrides
$body-bg: #000;
$body-color: #111;

// Bootstrap and its default variables
@import "node_modules/bootstrap/scss/bootstrap";
```

重複任何需要的變數在 Bootstrap 中，包含在下方的全域選項。

### Sass Maps 與循環

Bootstrap 4 包含一些 Sass maps、對應值，這使它更容易產生一連貫的 CSS。我們使用 Sass map 做為我們的顏色、中斷點還有更多，就像是 Sass 變數，所有 Sass map 都包含 `!default` 他更能夠覆蓋及擴增。

例如，要修改 `$theme-colors` mps 的現有顏色，請將以下內容加到您的自定義 Sass 文件中：

```scss
$theme-colors: (
  "primary": #0074d9,
  "danger": #ff4136
);
```

增加一個新色彩到 `$theme-colors`，並增加一個新的 key 及 值。

```scss
$theme-colors: (
  "custom-color": #900
);
```

### Functions

Bootstrap 使用多個 Sass 函式，但只有一個子集適用於一般主題。我們提供三個功能來獲取顏色 maps 的值：

```scss
@function color($key: "blue") {
  @return map-get($colors, $key);
}

@function theme-color($key: "primary") {
  @return map-get($theme-colors, $key);
}

@function gray($key: "100") {
  @return map-get($grays, $key);
}
```

這些允許您從 Sass maps 中選擇一種顏色，就像您如何使用 v3 中的顏色變數。

```scss
.custom-element {
  color: gray("100");
  background-color: theme-color("dark");
}
```

我們還有另一個功能，從 `$theme-colors` map 獲取一個特定 級別 顏色。負值會比較亮、更高的值則會變暗。

```scss
@function theme-color-level($color-name: "primary", $level: 0) {
  $color: theme-color($color-name);
  $color-base: if($level > 0, #000, #fff);
  $level: abs($level);

  @return mix($color-base, $color, $level * $theme-color-interval);
}
```

實際上你能使用這個函式並帶入兩個參數，從 `$theme-colors` 顏色名稱 (e.g., primary or danger) 及一個數值。

```scss
.custom-element {
  color: theme-color-level(primary, -10);
}
```

未來還可以增加其他功能，或你可以用自定義的 Sass 創建附加 Sass maps 的級別函式。

### 顏色對比

Bootstrap 中另外一個函式是色彩對比功能 `color-yiq`。 它使用 [YIQ 色彩空間](https://en.wikipedia.org/wiki/YIQ) 根據指定的基色自動回傳白色 (`#fff`) 或深色 (`#111`) 的對比度顏色。 此功能對於要生成多個 Class 的 mixins 或 循環 特別有用。

例如，從我們的 `$theme-colors` 生成顏色樣本：

```scss
@each $color, $value in $theme-colors {
  .swatch-#{$color} {
    color: color-yiq($value);
  }
}
```

它也可以用於一次性對比度需求：

```scss
.custom-element {
  color: color-yiq(#000); // returns `color: #fff`
}
```

您也可使用我們的 `$theme-colors` 功能指定基色：

```scss
.custom-element {
  color: color-yiq(theme-color("dark")); // returns `color: #fff`
}
```

## Sass 選項

使用我們內置的自定義變數文件自定義 Bootstrap 4，並使用新的 `$enable-*` Sass 變數切換全局 CSS 選項。覆寫變數的值，並依據需要重新編譯 `npm run test`。

你可以從 `_variables.scss` 檔案中的全域選項中找到這些自定義變數。

變數 | 值 | 描述
-- | -- | --
`$spacer` | `1rem` (default), or any value > 0 | 指定一個預設的空間值，用來產生我們的 spacer 通用類別。
`$enable-rounded` | `true` (default) or `false` | 啟用各元件上的 border-radius 樣式。
`$enable-shadows` | `true` or `false` (default) | 啟用各元件上的 box-shadow 樣式。
`$enable-gradients` | `true` or `false` (default) | 啟用各元件上的漸層 background-image 樣式。
`$enable-transitions` | `true` (default) or `false` | E啟用各元件上的 transition 。
`$enable-hover-media-query` | `true` or `false` (default) | …
`$enable-grid-classes` | `true` (default) or `false` | 啟用並產生 Grid System CSS Class (如 `.container`, `.row`, `.col-md-1`, 等)
`$enable-caret` | `true` (default) or `false` | 啟用偽元素圖示在 .dropdown-toggle 。
`$enable-print-styles` | `true` (default) or `false` | 啟用列印優化的樣式。

## 色彩

Bootstrap 的許多元件和通用類別都是透過 Sass map 中定義的一系列顏色建構的。該 maps 可以在 Sass 中循環，以快速生成一系列規則集。

### 全部色彩

Bootstrap 4 中提供的顏色都可用作我們的 `scss/_variables.scss` 文件中的 Sass 變數和 Sass maps。

![全部色彩](README_IMG/bootstrap4_1.png)

以下是您可以在 Sass 中使用的方法：

```scss
// With variable
.alpha { color: $purple; }

// From the Sass map with our `color()` function
.beta { color: color("purple"); }
```

色彩通用類別 也可設置 `color` 和 `background-color`。

> 將來，我們的目標是為每種顏色的色調提供 Sass maps 和變數，就像下面的灰階色彩一樣。

### 主題色彩

我們使用所有色彩的子集來創建一個小的色表，用來產生色彩主題，也同時能透過 `scss/_variables.scss` 使用 Sass 變數、Sass map。

![主題色彩](README_IMG/bootstrap4_2.png)

### 灰階

`scss/_variables.scss` 中設置了一組廣泛的灰色變數和一個 Sass maps，整個專案將有一致的灰色陰影。

![灰階](README_IMG/bootstrap4_3.png)

在 `_variables.scss` 中，你會發現我們的顏色變數和 Sass maps。以下是 `$colors` Sass maps 的範例：

```scss
$colors: (
  "blue": $blue,
  "indigo": $indigo,
  "purple": $purple,
  "pink": $pink,
  "red": $red,
  "orange": $orange,
  "yellow": $yellow,
  "green": $green,
  "teal": $teal,
  "cyan": $cyan,
  "white": $white,
  "gray": $gray-600,
  "gray-dark": $gray-800
) !default;
```

增加、刪除或修改 Sass maps 中的值，來更新在其他元件的顯示方式，可惜的是，並非所有組件都能使用 Sass maps，未來更新時將會改善這一點，在此之前計劃使用 `${color}` 變數和這個 Sass map。

## 元件

Bootstrap 的許多元件和通用類別都是用 `@each` 迴圈構建的，它們遍歷 Sass maps。 這對於透過我們的 `$theme-colors` 生成元件的變體特別有用，並為每個中斷點創建響應式元件。當您自定義這些 Sass maps 並重新編譯時，您將自動看到您在這些迴圈中反映更改。

### 修飾

Bootstrap 許多元件都使用基類修飾符 Class 的方法構建。這代表大部分樣式都包含基礎類別（例如`.btn`），而樣式變數則限制於修飾 Class（例如 `.btn-danger`）。這些修飾的 Class 是從 `$theme-colors` sass map 建構，可自定義修飾 Class 名稱及數量。

這裡舉兩個例子介紹我們如何透過 `$theme-colors` map 來產生包含修飾 Class 在 `.alert` 元件上，並且包含全部 `.bg-*` 背景通用類別。

```scss
// Generate alert modifier classes
@each $color, $value in $theme-colors {
  .alert-#{$color} {
    @include alert-variant(theme-color-level($color, -10), theme-color-level($color, -9), theme-color-level($color, 6));
  }
}

// Generate `.bg-*` color utilities
@each $color, $value in $theme-colors {
  @include bg-variant('.bg-#{$color}', $value);
}
```

### 響應式

這些 Sass 迴圈也不限於色彩 map。你也可以生成不同的響應式元件及通用類別，夏霏的範例是文字對齊通用類別的響應式。我們將包含 media query 的 `$grid-breakpoints` Sass map 透過 `@each` 來循環生成。

如果你修改你的 $grid-breakpoints，你將會套用 maps 中的所有迴圈。