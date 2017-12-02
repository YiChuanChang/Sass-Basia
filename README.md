# Sass 基礎
Sass(Syntactically Awesome StyleSheet)是一種可編譯成css的腳本語言，編譯器由Ruby編寫而成。

相較於直接撰寫CSS的優點在於

 - 使用巢狀編寫方式
 - 可以定義變數
 - 可以定義函式

接下來會先介紹如何建立可以使用Sass的環境，以及如何使用sass。最後，會針對上述的三項優點，逐點做介紹
## Sass 基礎設定 

 首先，在 osX 上安裝 sass 需要在 terminal 中下以下指令，由於這是透過ruby來安裝，在 osX 中已經有內建安裝好的 ruby ，若是以其他作業系統安裝的話，需要要先安裝 ruby
 
 ```
 sudo gem install sass
 ```
 
 安裝完成後，可以使用以下的指令來確認版本
 
 ```
 sass -v
 ```
 若有正常安裝，應該會回覆
 ```
 Sass 3.5.3
 ```
 
 
 Sass的編譯是在 terminal 下以下的指令
 
 ```
 sass input.sass out.css
 ```
 
 input.sass 為你撰寫的 sass 檔， out.css 則是輸出的 css 檔，這個指令只會針對當下的 sass 檔編譯一次，若是你想要在每次儲存 sass 檔時，能夠自動偵測你儲存的動作，並將當前的 sass 編譯成 css 的話，可以使用以下的指令
 
 ```
 sass --watch input.sass:out.css
 ```
 
 接下來我們將針對 sass 的優點做更深入的解說
 
 

## 使用巢狀編寫
Sass 不同於 Css，並不使用大括號來定義範圍，Sass使用的空格或者是tab來定義（有點像是python，擁有同樣的Tab數量或者是擁有同樣空格數量的將被視為是在同一個範圍內)

例：
css版本

```css
div{
  width: 100px
  background-color: blue;
}
div .yellow-bg{
  background-color: yellow;
}
```

若是想要編譯出上述這一段css，我們的sass應該寫成這樣

```sass
div
  width: 100px
  background-color: blue
		
  .yellow-bg
    background-color: uellow
```
這裡我們可以看到 Sass 以 tab 或者是空白來定義範圍的特性，注意，空白與 tab 不可以混用，比如說在 div 中的 width 前使用了 tab ，卻在 div 中的 background-color 前使用四個空白鍵，即使視覺上看來縮排一樣，如此的做法卻會造成編譯錯誤（sublime 中可以反白整個段落檢查，tab會在反白中呈現一條線，空格則是一個點）

另外，在上面這一段範例程式碼中，我們也可以學習到sass的巢狀編寫特性，當我們想要選取 div 中 class 為 yellow-bg 的元素，只要將 .yellow-bg 像是屬性一樣寫在 div 中便可以簡單的選取，此項設計簡化了原本 css 需要一直重複撰寫前綴選取的麻煩。

### &
在上述的巢狀編寫中，我們並沒有辦法選取同時包含有兩個甚至兩個以上 class 的元素，因此在sass中出現了“＆”，我們直接來看他的使用方法

css版本

```css
div{
  width: 200px;
}
div.yellow-bg{
  background-color: yellow;
  opacity: 0.5;
}
div.yellow-bg.large-width{
  width: 100vw;
}
div.yellow-bg:hover{
  opacity: 1;
}
```

若要編譯出上述這一段css，我們的sass應該要寫成這樣

```sass
div
  width: 200px
  &.yellow-bg
    backgeound-color: yellow
    opacity: 0.5
    &.large-width
      width: 100vw
    &:hover
      opacity: 1
```
我們可以由上述的範例程式碼中觀察到，程式碼中的 & 就代表了上一層的選擇，比如 .yellow-bg 前的 ＆ 就代表了上一層的 div ，如此一來，我們便可以使用＆來選擇 class 是 yellow-bg 的 div 了。

## 可以定義變數
在一般的 css 中，我們無法定義變數，若是有十個 class 擁有相同的背景顏色，若是我們想要同時更改他們的顏色，我們只能夠一個一個慢慢的更改，但在 sass 中，我們可以定義變數，並且給予屬性一個變數值，如此一來，當有十個 class 都被給予了相同的變數值，只要變數值一更改，十個 class 的屬性也就同時更改了，

sass 定義的變數必須以 $ 符號作為開頭，以下我們來看範例

```sass
$blue: #0000ff
$mobile-width: 480px
$primary-font: 'sans-serif', 'Roboto, arial'

.test
  color: $blue
  widht: $mobile-width
  font-family: $primary-font
```

## 可以定義函式 
在sass中有兩種不同的函式，一種為 Mixin ，另外一種則是 function ，兩者的差是在於 Mixin 如同一次幫你做好多項屬性的設定，而function則是幫你做了特定的計算後回傳相對應的結果值。以下將更詳細的解說兩者的使用方式。
###Mixin
有時在 css 中，常常會有一連串相配合的屬性會設定，但只是使用的變數不太一樣，比如說，當我們在使用 css 製作動畫時，常常會使用到 transition 這個屬性，但是考慮到瀏覽器的兼容性，我們可能會再多設定幾個相關的的屬性，而在這些屬性上只有動畫秒數這一項，我們希望是可以變動的，因此，我們可以對照一下一般css的寫法以及sass的寫法。

```css
  .div1{
    -webkit-transition: all 2s ease-in-out; /* Safari 3.1 to 6.0 */
    transition: all 2s ease-in-out;
  }
  .div2{
    -webkit-transition: all 3s linear; /* Safari 3.1 to 6.0 */
    transition: all 3s linear;
  } 
```
如果是使用css的話，我們就可以定義一個 Mixin 來實現一樣的功能

```sass
@mixin animation($duration, $type)
  -webkit-transition: all $duration $type /* Safari 3.1 to 6.0 */
  transition: all $duration $type

div1
  @include animation;
div2
  @include animation;
```

### Function
我們可以透過 function 來計算出我們想要的數值，比如我們有10個相連的div，而我希望這10個div的width可以從10%到100%慢慢的遞增，如果是一般css我們會寫得像是這樣

```css
.div1{ width: 10%; }
.div2{ width: 20%; }
.div3{ width: 30%; }
.div4{ width: 40%; }
.div5{ width: 50%; }
.div6{ width: 60%; }
.div7{ width: 70%; }
.div8{ width: 80%; }
.div9{ width: 90%; }
.div10{ width: 100%; }
```

但若是使用function的話，我們則可以利用sass寫成像是下面這樣

``` sass
@function div_width($i)
  @return percentage($i/10)
.div1 
  width: div_width(1)
.div2 
  width: div_width(2)
.div3 
  width: div_width(3)
.div4 
  width: div_width(4)
.div5 
  width: div_width(5)
.div6 
  width: div_width(6)
.div7 
  width: div_width(7)
.div8
  width: div_width(8)
.div9 
  width: div_width(9)
.div10 
  width: div_width(10)
```

到目前為止，sass的寫法似乎沒有比較簡單易寫，但在sass中也定義了許多一般程式語言擁有的常用宣告，比如說是if-else或者是我們接下來要用的for loop，若是我們將上面這一段加上for loop的話，看起來就會像是

```sass
@function div_width($i)
  @return percentage($i/10)
	
@for $i from 1 through 10
  .div#{$i}
    width: div_width($i)
```

## 結語
此篇文章大致簡介了Sass的基礎的使用方法，以及一些基本的概念，下面也提供一些可以學習的資源

 - https://www.tutorialspoint.com/sass/
 - http://sass-lang.com/guide











