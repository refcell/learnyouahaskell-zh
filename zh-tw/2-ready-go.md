# 從零開始

## 準備好了嗎？

![](img/startingout.png)

準備來開始我們的旅程！如果你就是那種從不看說明書的人，我推薦你還是回頭看一下簡介的最後一節。那裡面講了這個教學中你需要用到的工具及基本用法。我們首先要做的就是進入 ghc 的互動模式，接着就可以寫幾個函數體驗一下 Haskell 了。打開終端機，輸入 `ghci`，你會看到下列歡迎訊息：

```haskell
GHCi, version 6.8.2: http://www.haskell.org/ghc/
:? for help  Loading package base ... linking ... done.
Prelude>
```

恭喜您已經進入了 ghci 了！目前它的命令列提示是 `prelude>`，不過它在你裝載一些模組之後會變的比較長。為了美觀起見，我們會輸入指令 `:set prompt "ghci> "` 把它改成 `ghci>`。

首先來看一些簡單的運算

```haskell
ghci> 2 + 15
17
ghci> 49 * 100
4900
ghci> 1892 - 1472
420
ghci> 5 / 2
2.5
ghci>
```

很簡單吧！你也可以在一行中使用多個運算子，他們會按照運算子優先順序執行計算，而使用括號可以改變執行的優先順序。

```haskell
ghci> (50 * 100) - 4999
1  
ghci> 50 * 100 - 4999
1  
ghci> 50 * (100 - 4999)
-244950
```

但注意處理負數的時候有個小陷阱：我們執行 `5 * -3` 會 ghci 會回報錯誤。所以說，使用負數時最好將其置於括號之中，像 `5*(-3)` 就不會有問題。

要進行布林代數 \(Boolean Algebra\) 的演算也是很直覺的。你也許早就會猜，`&&` 指的是布林代數上的 AND，而 `||` 指的是布林代數上的 OR，`not` 會把 `True` 變成 `False`，`False` 變成 `True`。

```haskell
ghci> True && False
False
ghci> True && True
True
ghci> False || True
True
ghci> not False
True
ghci> not (True && True)
False
```

相等性可以這樣判定

```haskell
ghci> 5 == 5
True
ghci> 1 == 0
False
ghci> 5 /= 5
False
ghci> 5 /= 4
True
ghci> "hello" == "hello"
True
```

那執行 `5+"llama"` 或者 `5==True` 會怎樣？ 如果我們真的試著在 ghci 中跑，會得到下列的錯誤訊息：

```haskell
No instance for (Num [Char])
arising from a use of `+' at :1:0-9
Possible fix: add an instance declaration for (Num [Char])
In the expression: 5 + "llama"
In the definition of `it': it = 5 + "llama"
```

這邊 ghci 提示說 `"llama"` 並不是數值型別，所以它不知道該怎樣才能給它加上 5。即便是 “four” 甚至是 “4” 也不可以，Haskell 不拿它當數值。執行 `True==5`, ghci 就會提示型別不匹配。`+` 運算子要求兩端都是數值，而 `==` 運算子僅對兩個可比較的值可用。這就要求他們的型別都必須一致，蘋果和橘子就無法做比較。我們會在後面深入地理解型別的概念。Note: `5+4.0` 是可以執行的，5 既可以做被看做整數也可以被看做浮點數，但 4.0 則不能被看做整數。

![](img/ringring.png)

也許你並未察覺，不過從始至終我們一直都在使用函數。`*` 就是一個將兩個數相乘的函數，就像三明治一樣，用兩個參數將它夾在中央，這被稱作中綴函數。而其他大多數不能與數夾在一起的函數則被稱作前綴函數。絶大部分函數都是前綴函數，在接下來我們就不多做區別。大多數命令式程式語言中的函數呼叫形式通常就是函數名，括號，由逗號分隔的參數列。而在 Haskell 中，函數呼叫的形式是函數名，空格，空格分隔的參數列。簡單舉個例子，我們呼叫 Haskell 中最無趣的函數：

```haskell
ghci> succ 8
9
```

`succ` 函數返回一個數的後繼 \(successor\)。而且如你所見，在 Haskell 中是用空格來將函數與參數分隔的。至於呼叫多個參數的函數也很容易，`min` 和 `max` 接受兩個可比較大小的參數，並返回較大或者較小的那個數。

```haskell
ghci> min 9 10
9
ghci> min 3.4 3.2
3.2
ghci> max 100 101
101
```

函數呼叫擁有最高的優先順序，如下兩句是等效的

```haskell
ghci> succ 9 + max 5 4 + 1
16
ghci> (succ 9) + (max 5 4) + 1
16
```

若要取 9 乘 10 的後繼，`succ 9*10` 是不行的，程式會先取 9 的後繼，然後再乘以 10 得 100。正確的寫法應該是 `succ(9*10)`，得 91。如果某函數有兩個參數，也可以用 `````  符號將它括起，以中綴函數的形式呼叫它。

例如取兩個整數相除所得商的 `div` 函數, `div 92 10` 可得 9，但這種形式不容易理解：究竟是哪個數是除數，哪個數被除？使用中綴函數的形式 ``92 `div` 10`` 就更清晰了。

從命令式程式語言走過來的人們往往會覺得函數呼叫與括號密不可分，在 C 中，呼叫函數必加括號，就像 `foo()`, `bar(1)`,或者 `baz(3,"haha")`。而在 Haskell 中，函數的呼叫使用空格，例如 `bar (bar 3)`，它並不表示以 `bar` 和 3 兩個參數去呼叫 `bar`，而是以 `bar 3` 所得的結果作為參數去呼叫 `bar`。在 C 中，就相當於 `bar(bar(3))`。

## 初學者的第一個函數

在前一節中我們簡單介紹了函數的呼叫，現在讓我們編寫我們自己的函數！打開你最喜歡的編輯器，輸入如下程式碼，它的功能就是將一個數字乘以 2。

```haskell
doubleMe x = x + x
```

函數的聲明與它的呼叫形式大致相同，都是先函數名，後跟由空格分隔的參數表。但在聲明中一定要在 `=` 後面定義函數的行為。

保存為 `baby.hs` 或任意名稱，然後轉至保存的位置，打開 ghci，執行 `:l baby.hs`。這樣我們的函數就裝載成功，可以呼叫了。

```haskell
ghci> :l baby  
[1 of 1] Compiling Main             ( baby.hs, interpreted )
Ok, modules loaded: Main.
ghci> doubleMe 9
18
ghci> doubleMe 8.3
16.6
```

`+` 運算子對整數和浮點都可用\(實際上所有有數字特徵的值都可以\)，所以我們的函數可以處理一切數值。聲明一個包含兩個參數的函數如下：

```haskell
doubleUs x y = x*2 + y*2
```

很簡單。將其寫成 `doubleUs x y = x + x + y + y` 也可以。測試一下\(記住要保存為 `baby.hs` 併到 ghci 下邊執行 `:l baby.hs`\)

```haskell
ghci> doubleUs 4 9
26
ghci> doubleUs 2.3 34.2
73.0
ghci> doubleUs 28 88 + doubleMe 123
478
```

你可以在其他函數中呼叫你編寫的函數，如此一來我們可以將 `doubleUs` 函數改為：

```haskell
doubleUs x y = doubleMe x + doubleMe y
```

![](img/baby.png)

這種情形在 Haskell 下邊十分常見：編寫一些簡單的函數，然後將其組合，形成一個較為複雜的函數，這樣可以減少重複工作。設想若是哪天有個數學家驗證說 2 應該是 3，我們只需要將 `doubleMe` 改為 `x+x+x` 即可，由於 `doubleUs` 呼叫到 `doubleMe`，於是整個程式便進入了 2 即是 3 的古怪世界。

Haskell 中的函數並沒有順序，所以先聲明 `doubleUs` 還是先聲明 `doubleMe` 都是同樣的。如下，我們編寫一個函數，它將小於 100 的數都乘以 2，因為大於 100 的數都已經足夠大了！

```haskell
doubleSmallNumber x = if x > 100
                      then x
                      else  x*2
```

接下來介紹 Haskell 的 `if` 語句。你也許會覺得和其他語言很像，不過存在一些不同。Haskell 中 `if` 語句的 `else` 部分是不可省略。在命令式語言中，你可以通過 `if` 語句來跳過一段程式碼，而在 `Haskell` 中，每個函數和表達式都要返回一個結果。對於這點我覺得將 `if` 語句置於一行之中會更易理解。Haskell 中的 `if` 語句的另一個特點就是它其實是個表達式，表達式就是返回一個值的一段程式碼：5 是個表達式，它返回 5；`4+8` 是個表達式；`x+y` 也是個表達式，它返回 `x+y` 的結果。正由於 `else` 是強制的，`if` 語句一定會返回某個值，所以說 `if` 語句也是個表達式。如果要給剛剛定義的函數的結果都加上 1，可以如此修改：

```haskell
doubleSmallNumber' x = (if x > 100 then x else x*2) + 1
```

若是去掉括號，那就會只在小於 100 的時候加 1。注意函數名最後的那個單引號，它沒有任何特殊含義，只是一個函數名的合法字元罷了。通常，我們使用單引號來區分一個稍經修改但差別不大的函數。定義這樣的函數也是可以的：

```haskell
conanO'Brien = "It's a-me, Conan O'Brien!"
```

在這裡有兩點需要注意。首先就是我們沒有大寫 `conan` 的首字母，因為首字母大寫的函數是不允許的，稍後我們將討論其原因；另外就是這個函數並沒有任何參數。沒有參數的函數通常被稱作“定義”\(或者“名字”\)，一旦定義，`conanO'Brien` 就與字串 `"It's a-me, Conan O'Brien!"` 完全等價，且它的值不可以修改。

## List 入門

![](img/list.png)

在 Haskell 中，List 就像現實世界中的購物單一樣重要。它是最常用的資料結構，並且十分強大，靈活地使用它可以解決很多問題。本節我們將對 List，字串和 list comprehension 有個初步瞭解。 在 Haskell 中，List 是一種單型別的資料結構，可以用來存儲多個型別相同的元素。我們可以在裡面裝一組數字或者一組字元，但不能把字元和數字裝在一起。

```text
*Note*: 在 ghci 下，我們可以使用 ``let`` 關鍵字來定義一個常量。在 ghci 下執行 ``let a=1`` 與在腳本中編寫 ``a=1`` 是等價的。
```

```haskell
ghci> let lostNumbers = [4,8,15,16,23,48]  
ghci> lostNumbers  
[4,8,15,16,23,48]
```

如你所見，一個 List 由方括號括起，其中的元素用逗號分隔開來。若試圖寫 `[1,2,'a',3,'b','c',4]` 這樣的 List，Haskell 就會報出這幾個字元不是數字的錯誤。字串實際上就是一組字元的 List，"Hello" 只是 `['h','e','l','l','o']` 的語法糖而已。所以我們可以使用處理 List 的函數來對字串進行操作。 將兩個 List 合併是很常見的操作，這可以通過 `++` 運算子實現。

```haskell
ghci> [1,2,3,4] ++ [9,10,11,12]  
[1,2,3,4,9,10,11,12]  
ghci> "hello" ++ " " ++ "world"  
"hello world"  
ghci> ['w','o'] ++ ['o','t']  
"woot"
```

在使用 `++` 運算子處理長字串時要格外小心\(對長 List 也是同樣\)，Haskell 會遍歷整個的 List\(`++` 符號左邊的那個\)。在處理較短的字串時問題還不大，但要是在一個 5000 萬長度的 List 上追加元素，那可得執行好一會兒了。所以說，用 `:` 運算子往一個 List 前端插入元素會是更好的選擇。

```haskell
ghci> 'A':" SMALL CAT"  
"A SMALL CAT"  
ghci> 5:[1,2,3,4,5] 
[5,1,2,3,4,5]
```

`:` 運算子可以連接一個元素到一個 List 或者字串之中，而 `++` 運算子則是連接兩個 List。若要使用 `++` 運算子連接單個元素到一個 List 之中，就用方括號把它括起使之成為單個元素的 List。`[1,2,3]` 實際上是 `1:2:3:[]` 的語法糖。`[]` 表示一個空 List,若要從前端插入 3，它就成了 `[3]`, 再插入 2，它就成了 `[2,3]`，以此類推。

```text
*Note*: ``[],[[]],[[],[],[]]`` 是不同的。第一個是一個空的 List，第二個是含有一個空 List 的 List，第三個是含有三個空 List 的 List。
```

若是要按照索引取得 List 中的元素，可以使用 `!!` 運算子，索引的下標為 0。

```haskell
ghci> "Steve Buscemi" !! 6  
'B'  
ghci> [9.4,33.2,96.2,11.2,23.25] !! 1  
33.2
```

但你若是試圖在一個只含有 4 個元素的 List 中取它的第 6 個元素，就會報錯。要小心！

List 同樣也可以用來裝 List，甚至是 List 的 List 的 List：

```haskell
ghci> let b = [[1,2,3,4],[5,3,3,3],[1,2,2,3,4],[1,2,3]]
ghci> b
[[1,2,3,4],[5,3,3,3],[1,2,2,3,4],[1,2,3]]
ghci> b ++ [[1,1,1,1]]
[[1,2,3,4],[5,3,3,3],[1,2,2,3,4],[1,2,3],[1,1,1,1]]
ghci> [6,6,6]:b
[[6,6,6],[1,2,3,4],[5,3,3,3],[1,2,2,3,4],[1,2,3]]
ghci> b !! 2
[1,2,2,3,4]
```

List 中的 List 可以是不同長度，但必須得是相同的型別。如不可以在 List 中混合放置字元和數組相同，混合放置數值和字元的 List 也是同樣不可以的。當 List 內裝有可比較的元素時，使用 `>` 和 `>=` 可以比較 List 的大小。它會先比較第一個元素，若它們的值相等，則比較下一個，以此類推。

```haskell
ghci> [3,2,1] > [2,1,0]  
True  
ghci> [3,2,1] > [2,10,100]  
True  
ghci> [3,4,2] > [3,4]  
True  
ghci> [3,4,2] > [2,4]  
True  
ghci> [3,4,2] == [3,4,2]  
True
```

還可以對 List 做啥？如下是幾個常用的函數:

**head** 返回一個 List 的頭部，也就是 List 的首個元素。

```haskell
ghci> head [5,4,3,2,1] 
5
```

**tail** 返回一個 List 的尾部，也就是 List 除去頭部之後的部分。

```haskell
ghci> tail [5,4,3,2,1]  
[4,3,2,1]
```

**last** 返回一個 List 的最後一個元素。

```haskell
ghci> last [5,4,3,2,1]  
1
```

**init** 返回一個 List 除去最後一個元素的部分。

```haskell
ghci> init [5,4,3,2,1]
[5,4,3,2]
```

如果我們把 List 想象為一頭怪獸，那這就是它的樣子：

![](img/listmonster.png)

試一下，若是取一個空 List 的 `head` 又會怎樣？

```haskell
ghci> head []  
*** Exception: Prelude.head: empty list
```

糟糕，程式直接跳出錯誤。如果怪獸都不存在的話，那他的頭也不會存在。在使用 `head`，`tail`，`last` 和 `init` 時要小心別用到空的 List 上，這個錯誤不會在編譯時被捕獲。所以說做些工作以防止從空 List 中取值會是個好的做法。

**length** 返回一個 List 的長度。

```haskell
ghci> length [5,4,3,2,1]  
5
```

**null** 檢查一個 List 是否為空。如果是，則返回 `True`，否則返回 `False`。應當避免使用 `xs==[]` 之類的語句來判斷 List 是否為空，使用 null 會更好。

```haskell
ghci> null [1,2,3]  
False  
ghci> null []  
True
```

**reverse** 將一個 List 反轉:

```haskell
ghci> reverse [5,4,3,2,1]  
[1,2,3,4,5]
```

**take** 返回一個 List 的前幾個元素，看：

```haskell
ghci> take 3 [5,4,3,2,1]  
[5,4,3]  
ghci> take 1 [3,9,3]  
[3]  
ghci> take 5 [1,2]  
[1,2]  
ghci> take 0 [6,6,6] 
[]
```

如上，若是圖取超過 List 長度的元素個數，只能得到原 List。若 `take 0` 個元素，則會得到一個空 List！ `drop` 與 `take` 的用法大體相同，它會刪除一個 List 中的前幾個元素。

```haskell
ghci> drop 3 [8,4,2,1,5,6]  
[1,5,6]  
ghci> drop 0 [1,2,3,4]  
[1,2,3,4]  
ghci> drop 100 [1,2,3,4]  
[]
```

**maximum** 返回一個 List 中最大的那個元素。`minimun` 返回最小的。

```haskell
ghci> minimum [8,4,2,1,5,6]  
1  
ghci> maximum [1,9,2,3,4]  
9
```

**sum** 返回一個 List 中所有元素的和。`product` 返回一個 List 中所有元素的積。

```haskell
ghci> sum [5,2,1,6,3,2,5,7]  
31  
ghci> product [6,2,1,2]  
24  
ghci> product [1,2,5,6,7,9,2,0]  
0
```

**elem** 判斷一個元素是否在包含于一個 List，通常以中綴函數的形式呼叫它。

```haskell
ghci> 4 `elem` [3,4,5,6]  
True  
ghci> 10 `elem` [3,4,5,6]  
False
```

這就是幾個基本的 List 操作函數，我們會在往後的一節中瞭解更多的函數。

## 使用 Range

![](img/cowboy.png)

今天如果想得到一個包含 1 到 20 之間所有數的 List，你會怎麼做? 我們可以將它們一個一個用鍵盤打出來，但很明顯地這不是一個完美的方案，特別是你追求一個好的程式語言的時候。我們想用的是區間 \(Range\)。Range 是構造 List 方法之一，而其中的值必須是可枚舉的，像 1、2、3、4...字元同樣也可以枚舉，字母表就是 `A..Z` 所有字元的枚舉。而名字就不可以枚舉了，`"john"` 後面是誰？我不知道。

要得到包含 1 到 20 中所有自然數的 List，只要 `[1..20]` 即可，這與用手寫 `[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]` 是完全等價的。其實用手寫一兩個還不是什麼大事，但若是手寫一個非常長的 List 那就鐵定是個笨方法。

```haskell
ghci> [1..20]
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
ghci> ['a'..'z']
"abcdefghijklmnopqrstuvwxyz"
ghci> ['K'..'Z']  
"KLMNOPQRSTUVWXYZ"
```

Range 的特點是他還允許你指定每一步該跨多遠。譬如說，今天的問題換成是要得到 1 到 20 間所有的偶數或者 3 的倍數該怎樣？

```haskell
ghci> [2,4..20]
[2,4,6,8,10,12,14,16,18,20]
ghci> [3,6..20]
[3,6,9,12,15,18]
```

僅需用逗號將前兩個元素隔開，再標上上限即可。儘管 Range 很聰明，但它恐怕還滿足不了一些人對它的期許。你就不能通過 `[1,2,4..100]`這樣的語句來獲得所有 2 的冪。一方面是因為步長只能標明一次，另一方面就是僅憑前幾項，數組的後項是不能確定的。要得到 20 到 1 的 List，`[20..1]` 是不可以的。必須得 `[20,19..1]`。 在 Range 中使用浮點數要格外小心！出於定義的原因，浮點數並不精確。若是使用浮點數的話，你就會得到如下的糟糕結果

```haskell
ghci> [0.1, 0.3 .. 1]
[0.1,0.3,0.5,0.7,0.8999999999999999,1.0999999999999999]
```

我的建議就是避免在 Range 中使用浮點數。

你也可以不標明 Range 的上限，從而得到一個無限長度的 List。在後面我們會講解關於無限 List 的更多細節。取前 24 個 13 的倍數該怎樣？恩，你完全可以 `[13,26..24*13]`，但有更好的方法： `take 24 [13,26..]`。

由於 Haskell 是惰性的，它不會對無限長度的 List 求值，否則會沒完沒了的。它會等着，看你會從它那兒取多少。在這裡它見你只要 24 個元素，便欣然交差。如下是幾個生成無限 List 的函數 `cycle` 接受一個 List 做參數並返回一個無限 List 。如果你只是想看一下它的運算結果而已，它會運行個沒完的。所以應該在某處劃好範圍。

```haskell
ghci> take 10 (cycle [1,2,3])
[1,2,3,1,2,3,1,2,3,1]
ghci> take 12 (cycle "LOL ")
"LOL LOL LOL "
```

**repeat** 接受一個值作參數，並返回一個僅包含該值的無限 List。這與用 `cycle` 處理單元素 List 差不多。

```haskell
ghci> take 10 (repeat 5)
[5,5,5,5,5,5,5,5,5,5]
```

其實，你若只是想得到包含相同元素的 List ，使用 `replicate` 會更簡單，如 `replicate 3 10`，得 `[10,10,10]`。

## List Comprehension

![](img/kermit.png)

學過數學的你對集合的 comprehension \(Set Comprehension\) 概念一定不會陌生。通過它，可以從既有的集合中按照規則產生一個新集合。前十個偶數的 set comprehension 可以表示為![](img/setnotation.png)，豎綫左端的部分是輸出函數，`x` 是變數，`N` 是輸入集合。在 Haskell 下，我們可以通過類似 `take 10 [2,4..]` 的程式碼來實現。但若是把簡單的乘 2 改成更複雜的函數操作該怎麼辦呢？用 list comprehension，它與 set comprehension 十分的相似，用它取前十個偶數輕而易舉。這個 list comprehension 可以表示為：

```haskell
ghci> [x*2 | x <- [1..10]]
[2,4,6,8,10,12,14,16,18,20]
```

如你所見，結果正確。給這個 comprehension 再添個限制條件 \(predicate\)，它與前面的條件由一個逗號分隔。在這裡，我們要求只取乘以 2 後大於等於 12 的元素。

```haskell
ghci> [x*2 | x <- [1..10], x*2 >= 12]
[12,14,16,18,20]
```

cool，靈了。若是取 50 到 100 間所有除7的餘數為 3 的元素該怎麼辦？簡單：

```haskell
ghci> [ x | x <- [50..100], x `mod` 7 == 3]
[52,59,66,73,80,87,94]
```

成功！從一個 List 中篩選出符合特定限制條件的操作也可以稱為過濾 \(filtering\)。即取一組數並且按照一定的限制條件過濾它們。再舉個例子 吧，假如我們想要一個 comprehension，它能夠使 List 中所有大於 10 的奇數變為 `"BANG"`，小於 10 的奇數變為 `"BOOM"`，其他則統統扔掉。方便重用起見，我們將這個 comprehension 置於一個函數之中。

```haskell
boomBangs xs = [ if x < 10 then "BOOM!" else "BANG!" | x <- xs, odd x]
```

這個 comprehension 的最後部分就是限制條件，使用 `odd` 函數判斷是否為奇數：返回 `True`，就是奇數，該 List 中的元素才被包含。

```haskell
ghci> boomBangs [7..13]
["BOOM!","BOOM!","BANG!","BANG!"]
```

也可以加多個限制條件。若要達到 10 到 20 間所有不等於 13，15 或 19 的數，可以這樣：

```haskell
ghci> [ x | x <- [10..20], x /= 13, x /= 15, x /= 19]
[10,11,12,14,16,17,18,20]
```

除了多個限制條件之外，從多個 List 中取元素也是可以的。這樣的話 comprehension 會把所有的元素組合交付給我們的輸出函數。在不過濾的前提 下，取自兩個長度為 4 的集合的 comprehension 會產生一個長度為 16 的 List。假設有兩個 List，`[2,5,10]` 和 `[8,10,11]`， 要取它們所有組合的積，可以這樣：

```haskell
ghci> [ x*y | x <- [2,5,10], y <- [8,10,11]]
[16,20,22,40,50,55,80,100,110]
```

意料之中，得到的新 List 長度為 9。若只取乘積大于 50 的結果該如何？

```haskell
ghci> [ x*y | x <-[2,5,10], y <- [8,10,11], x*y > 50]
[55,80,100,110]
```

取個包含一組名詞和形容詞的 List comprehension 吧，寫詩的話也許用得着。

```haskell
ghci> let nouns = ["hobo","frog","pope"]
ghci> let adjectives = ["lazy","grouchy","scheming"]
ghci> [adjective ++ " " ++ noun | adjective <- adjectives, noun <- nouns]
["lazy hobo","lazy frog","lazy pope","grouchy hobo","grouchy frog", "grouchy pope","scheming hobo",
"scheming frog","scheming pope"]
```

明白！讓我們編寫自己的 `length` 函數吧！就叫做 `length'`!

```haskell
length' xs = sum [1 | _ <- xs]
```

`_` 表示我們並不關心從 List 中取什麼值，與其弄個永遠不用的變數，不如直接一個 `_`。這個函數將一個 List 中所有元素置換為 1，並且使其相加求和。得到的結果便是我們的 List 長度。友情提示：字串也是 List，完全可以使用 list comprehension 來處理字串。如下是個除去字串中所有非大寫字母的函數：

```haskell
removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]
```

測試一下：

```haskell
ghci> removeNonUppercase "Hahaha! Ahahaha!"
"HA"
ghci> removeNonUppercase "IdontLIKEFROGS"
"ILIKEFROGS"
```

在這裡，限制條件做了所有的工作。它說：只有在 `['A'..'Z']` 之間的字元才可以被包含。

若操作含有 List 的 List，使用嵌套的 List comprehension 也是可以的。假設有個包含許多數值的 List 的 List，讓我們在不拆開它的前提下除去其中的所有奇數：

```haskell
ghci> let xxs = [[1,3,5,2,3,1,2,4,5],[1,2,3,4,5,6,7,8,9],[1,2,4,2,1,6,3,1,3,2,3,6]]
ghci> [ [ x | x <- xs, even x ] | xs <- xxs]
[[2,2,4],[2,4,6,8],[2,4,2,6,2,6]]
```

將 List Comprehension 分成多行也是可以的。若非在 ghci 之下，還是將 List Comprehension 分成多行好，尤其是需要嵌套的時候。

## Tuple

![](img/tuple.png)

從某種意義上講，Tuple \(元組\)很像 List --都是將多個值存入一個個體的容器。但它們卻有着本質的不同，一組數字的 List 就是一組數字，它們的型別相同，且不關心其中包含元素的數量。而 Tuple 則要求你對需要組合的數據的數目非常的明確，它的型別取決於其中項的數目與其各自的型別。Tuple 中的項由括號括起，並由逗號隔開。

另外的不同之處就是 Tuple 中的項不必為同一型別，在 Tuple 裡可以存入多型別項的組合。

動腦筋，在 Haskell 中表示二維向量該如何？使用 List 是一種方法，它倒也工作良好。若要將一組向量置於一個 List 中來表示平面圖形又該怎樣？我們可以寫類似 `[[1,2],[8,11],[4,5]]` 的程式碼來實現。但問題在於，`[[1,2],[8,11,5],[4,5]]` 也是同樣合法的，因為其中元素的型別都相同。儘管這樣並不靠譜，但編譯時並不會報錯。然而一個長度為 2 的 Tuple \(也可以稱作序對，Pair\) ，是一個獨立的類型，這便意味着一個包含一組序對的 List 不能再加入一個三元組，所以說把原先的方括號改為圓括號使用 Tuple 會 更好: `[(1,2),(8,11),(4,5)]`。若試圖表示這樣的圖形： `[(1,2),(8,11,5),(4,5)]`，就會報出以下的錯誤：

```haskell
Couldn't match expected type `(t, t1)'
against inferred type `(t2, t3, t4)'
In the expression: (8, 11, 5)
In the expression: [(1, 2), (8, 11, 5), (4, 5)]
In the definition of `it': it = [(1, 2), (8, 11, 5), (4, 5)]
```

這告訴我們說程式在試圖將序對和三元組置於同一 List 中，而這是不允許的。同樣 `[(1,2),("one",2)]` 這樣的 List 也不行，因為 其中的第一個 Tuple 是一對數字，而第二個 Tuple 卻成了一個字串和一個數字。Tuple 可以用來儲存多個數據，如，我們要表示一個人的名字與年 齡，可以使用這樣的 Tuple: `("Christopher", "Walken", 55)`。從這個例子裡也可以看出，Tuple 中也可以存儲 List。

使用 Tuple 前應當事先明確一條數據中應該由多少個項。每個不同長度的 Tuple 都是獨立的型別，所以你就不可以寫個函數來給它追加元素。而唯一能做的，就是通過函數來給一個 List 追加序對，三元組或是四元組等內容。

可以有單元素的 List，但 Tuple 不行。想想看，單元素的 Tuple 本身就只有一個值，對我們又有啥意義？不靠譜。

同 List 相同，只要其中的項是可比較的，Tuple 也可以比較大小，只是你不可以像比較不同長度的 List 那樣比較不同長度的 Tuple 。如下是兩個有用的序對操作函數：

**fst** 返回一個序對的首項。

```haskell
ghci> fst (8,11)
8
ghci> fst ("Wow", False)
"Wow"
```

**snd** 返回序對的尾項。

```haskell
ghci> snd (8,11)
11
ghci> snd ("Wow", False)
False
```

```text
*Note*：這兩個函數僅對序對有效，而不能應用於三元組，四元組和五元組之上。稍後，我們將過一遍從 Tuple 中取數據的所有方式。
```

有個函數很 cool，它就是 `zip`。它可以用來生成一組序對 \(Pair\) 的 List。它取兩個 List，然後將它們交叉配對，形成一組序對的 List。它很簡單，卻很實用，尤其是你需要組合或是遍歷兩個 List 時。如下是個例子：

```haskell
ghci> zip [1,2,3,4,5] [5,5,5,5,5]
[(1,5),(2,5),(3,5),(4,5),(5,5)]
ghci> zip [1 .. 5] ["one", "two", "three", "four", "five"]
[(1,"one"),(2,"two"),(3,"three"),(4,"four"),(5,"five")]
```

它把元素配對並返回一個新的 List。第一個元素配第一個，第二個元素配第二個..以此類推。注意，由於序對中可以含有不同的型別，`zip` 函數可能會將不同型別的序對組合在一起。若是兩個不同長度的 List 會怎麼樣？

```haskell
ghci> zip [5,3,2,6,2,7,2,5,4,6,6] ["im","a","turtle"]
[(5,"im"),(3,"a"),(2,"turtle")]
```

較長的那個會在中間斷開，去匹配較短的那個。由於 Haskell 是惰性的，使用 `zip` 同時處理有限和無限的 List 也是可以的：

```haskell
ghci> zip [1..] ["apple", "orange", "cherry", "mango"]
[(1,"apple"),(2,"orange"),(3,"cherry"),(4,"mango")]
```

接下來考慮一個同時應用到 List 和 Tuple 的問題：如何取得所有三邊長度皆為整數且小於等於 10，周長為 24 的直角三角形？首先，把所有三遍長度小於等於 10 的三角形都列出來：

```haskell
ghci> let triangles = [ (a,b,c) | c <- [1..10], b <- [1..10], a <- [1..10] ]
```

剛才我們是從三個 List 中取值，並且通過輸出函數將其組合為一個三元組。只要在 ghci 下邊呼叫 triangle，你就會得到所有三邊都小於等於 10 的三角形。我們接下來給它添加一個限制條件，令其必須為直角三角形。同時也考慮上 `b` 邊要短於斜邊，`a` 邊要短於 `b` 邊情況：

```haskell
ghci> let rightTriangles = [ (a,b,c) | c <- [1..10], b <- [1..c], a <- [1..b], a^2 + b^2 == c^2]
```

已經差不多了。最後修改函數，告訴它只要周長為 24 的三角形。

```haskell
ghci> let rightTriangles' = [ (a,b,c) | c <- [1..10], b <- [1..c], a <- [1..b], a^2 + b^2 == c^2, a+b+c == 24]
ghci> rightTriangles'
[(6,8,10)]
```

得到正確結果！這便是函數式程式語言的一般思路：先取一個初始的集合併將其變形，執行過濾條件，最終取得正確的結果。

