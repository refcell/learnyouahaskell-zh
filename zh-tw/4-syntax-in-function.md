# 函數的語法

## 模式匹配 \(Pattern matching\)

![](img/pattern.png)

本章講的就是 Haskell 那套獨特的語法結構，先從模式匹配開始。模式匹配通過檢查數據的特定結構來檢查其是否匹配，並按模式從中取得數據。

在定義函數時，你可以為不同的模式分別定義函數本身，這就讓程式碼更加簡潔易讀。你可以匹配一切數據型別 --- 數字，字元，List，元組，等等。我們弄個簡單函數，讓它檢查我們傳給它的數字是不是 7。

```haskell
lucky :: (Integral a) => a -> String  
lucky 7 = "LUCKY NUMBER SEVEN!"  
lucky x = "Sorry, you're out of luck, pal!"
```

在呼叫 `lucky` 時，模式會從上至下進行檢查，一旦有匹配，那對應的函數體就被應用了。這個模式中的唯一匹配是參數為 7，如果不是 7，就轉到下一個模式，它匹配一切數值並將其綁定為 `x` 。這個函數完全可以使用 `if` 實現，不過我們若要個分辨 1 到 5 中的數字，而無視其它數的函數該怎麼辦？要是沒有模式匹配的話，那可得好大一棵 `if-else` 樹了！

```haskell
sayMe :: (Integral a) => a -> String  
sayMe 1 = "One!"  
sayMe 2 = "Two!"  
sayMe 3 = "Three!"  
sayMe 4 = "Four!"  
sayMe 5 = "Five!"  
sayMe x = "Not between 1 and 5"
```

注意下，如果我們把最後匹配一切的那個模式挪到最前，它的結果就全都是 `"Not between 1 and 5"` 了。因為它自己匹配了一切數字，不給後面的模式留機會。

記得前面實現的那個階乘函數麼？當時是把 `n` 的階乘定義成了 `product [1..n]`。也可以寫出像數學那樣的遞迴實現，先說明 0 的階乘是 1 ，再說明每個正整數的階乘都是這個數與它前驅 \(predecessor\) 對應的階乘的積。如下便是翻譯到 Haskell 的樣子：

```haskell
factorial :: (Integral a) => a -> a  
factorial 0 = 1  
factorial n = n * factorial (n - 1)
```

這就是我們定義的第一個遞迴函數。遞迴在 Haskell 中十分重要，我們會在後面深入理解。如果拿一個數\(如 3\)呼叫 `factorial` 函數，這就是接下來的計算步驟：先計算 `3*factorial 2`，`factorial 2` 等於 `2*factorial 1`，也就是 `3*(2*(factorial 1))`。`factorial 1` 等於 `1*factorial 0`，好，得 `3*(2*(1*factorial 0))`，遞迴在這裡到頭了，嗯 --- 我們在萬能匹配前面有定義，0 的階乘是 1。於是最終的結果等於 `3*(2*(1*1))`。若是把第二個模式放在前面，它就會捕獲包括 0 在內的一切數字，這一來我們的計算就永遠都不會停止了。這便是為什麼說模式的順序是如此重要：它總是優先匹配最符合的那個，最後才是那個萬能的。

模式匹配也會失敗。假如這個函數：

```haskell
charName :: Char -> String  
charName 'a' = "Albert"  
charName 'b' = "Broseph"  
charName 'c' = "Cecil"
```

拿個它沒有考慮到的字元去呼叫它，你就會看到這個：

```haskell
ghci> charName 'a'  
"Albert"  
ghci> charName 'b'  
"Broseph"  
ghci> charName 'h'  
"*** Exception: tut.hs:(53,0)-(55,21): Non-exhaustive patterns in function charName
```

它告訴我們說，這個模式不夠全面。因此，在定義模式時，一定要留一個萬能匹配的模式，這樣我們的程序就不會為了不可預料的輸入而崩潰了。

對 Tuple 同樣可以使用模式匹配。寫個函數，將二維空間中的向量相加該如何？將它們的 `x` 項和 `y` 項分別相加就是了。如果不瞭解模式匹配，我們很可能會寫出這樣的程式碼：

```haskell
addVectors :: (Num a) => (a, a) -> (a, a) -> (a, a)  
addVectors a b = (fst a + fst b, snd a + snd b)
```

嗯，可以運行。但有更好的方法，上模式匹配：

```haskell
addVectors :: (Num a) => (a, a) -> (a, a) -> (a, a)  
addVectors (x1, y1) (x2, y2) = (x1 + x2, y1 + y2)
```

there we go！好多了！注意，它已經是個萬能的匹配了。兩個 `addVector` 的型別都是 `addVectors:: (Num a) => (a,a) -> (a,a) -> (a,a)`，我們就能夠保證，兩個參數都是序對 \(Pair\) 了。

`fst` 和 `snd` 可以從序對中取出元素。三元組 \(Tripple\) 呢？嗯，沒現成的函數，得自己動手：

```haskell
first :: (a, b, c) -> a  
first (x, _, _) = x  

second :: (a, b, c) -> b  
second (_, y, _) = y  

third :: (a, b, c) -> c  
third (_, _, z) = z
```

這裡的 `_` 就和 List Comprehension 中一樣。表示我們不關心這部分的具體內容。

說到 List Comprehension，我想起來在 List Comprehension 中也能用模式匹配：

```haskell
ghci> let xs = [(1,3), (4,3), (2,4), (5,3), (5,6), (3,1)]  
ghci> [a+b | (a,b) <- xs]  
[4,7,6,8,11,4]
```

一旦模式匹配失敗，它就簡單挪到下個元素。

對 List 本身也可以使用模式匹配。你可以用 `[]` 或 `:` 來匹配它。因為 `[1,2,3]` 本質就是 `1:2:3:[]` 的語法糖。你也可以使用前一種形式，像 `x:xs` 這樣的模式可以將 List 的頭部綁定為 `x`，尾部綁定為 `xs`。如果這 List 只有一個元素，那麼 `xs` 就是一個空 List。

```text
*Note*：``x:xs`` 這模式的應用非常廣泛，尤其是遞迴函數。不過它只能匹配長度大於等於 1 的 List。
```

如果你要把 List 的前三個元素都綁定到變數中，可以使用類似 `x:y:z:xs` 這樣的形式。它只能匹配長度大於等於 3 的 List。

我們已經知道了對 List 做模式匹配的方法，就實現個我們自己的 `head` 函數。

```haskell
head' :: [a] -> a  
head' [] = error "Can't call head on an empty list, dummy!"  
head' (x:_) = x
```

看看管不管用：

```haskell
ghci> head' [4,5,6]  
4  
ghci> head' "Hello"  
'H'
```

漂亮！注意下，你若要綁定多個變數\(用 `_` 也是如此\)，我們必須用括號將其括起。同時注意下我們用的這個 `error` 函數，它可以生成一個運行時錯誤，用參數中的字串表示對錯誤的描述。它會直接導致程序崩潰，因此應謹慎使用。可是對一個空 List 取 `head` 真的不靠譜哇。

弄個簡單函數，讓它用非標準的英語給我們展示 List 的前幾項。

```haskell
tell :: (Show a) => [a] -> String  
tell [] = "The list is empty"  
tell (x:[]) = "The list has one element: " ++ show x  
tell (x:y:[]) = "The list has two elements: " ++ show x ++ " and " ++ show y  
tell (x:y:_) = "This list is long. The first two elements are: " ++ show x ++ " and " ++ show y
```

這個函數顧及了空 List，單元素 List，雙元素 List 以及較長的 List，所以這個函數很安全。`(x:[])` 與 `(x:y:[])` 也可以寫作 `[x]` 和 `[x,y]` \(有了語法糖，我們不必多加括號\)。不過 `(x:y:_)` 這樣的模式就不行了，因為它匹配的 List 長度不固定。

我們曾用 List Comprehension 實現過自己的 `length` 函數，現在用模式匹配和遞迴重新實現它：

```haskell
length' :: (Num b) => [a] -> b  
length' [] = 0  
length' (_:xs) = 1 + length' xs
```

這與先前寫的那個 `factorial` 函數很相似。先定義好未知輸入的結果 --- 空 List，這也叫作邊界條件。再在第二個模式中將這 List 分割為頭部和尾部。說，List 的長度就是其尾部的長度加 1。匹配頭部用的 `_`，因為我們並不關心它的值。同時也應明確，我們顧及了 List 所有可能的模式：第一個模式匹配空 List，第二個匹配任意的非空 List。

看下拿 `"ham"` 呼叫 `length'` 會怎樣。首先它會檢查它是否為空 List。顯然不是，於是進入下一模式。它匹配了第二個模式，把它分割為頭部和尾部並無視掉頭部的值，得長度就是 `1+length' "am"`。ok。以此類推，`"am"` 的 `length` 就是 `1+length' "m"`。好，現在我們有了 `1+(1+length' "m")`。`length' "m"` 即 `1+length ""` \(也就是 `1+length' []` \)。根據定義，`length' []` 等於 `0`。最後得 `1+(1+(1+0))`。

再實現 `sum`。我們知道空 List 的和是 0，就把它定義為一個模式。我們也知道一個 List 的和就是頭部加上尾部的和的和。寫下來就成了：

```haskell
sum' :: (Num a) => [a] -> a  
sum' [] = 0  
sum' (x:xs) = x + sum' xs
```

還有個東西叫做 `as` 模式，就是將一個名字和 `@` 置於模式前，可以在按模式分割什麼東西時仍保留對其整體的引用。如這個模式 `xs@(x:y:ys)`，它會匹配出與 `x:y:ys` 對應的東西，同時你也可以方便地通過 `xs` 得到整個 List，而不必在函數體中重複 `x:y:ys`。看下這個 quick and dirty 的例子：

```haskell
capital :: String -> String  
capital "" = "Empty string, whoops!"  
capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]
```

```haskell
ghci> capital "Dracula"  
"The first letter of Dracula is D"
```

我們使用 `as` 模式通常就是為了在較大的模式中保留對整體的引用，從而減少重複性的工作。

還有——你不可以在模式匹配中使用 `++`。若有個模式是 `(xs++ys)`，那麼這個 List 該從什麼地方分開呢？不靠譜吧。而 `(xs++[x,y,z])` 或只一個 `(xs++[x])` 或許還能說的過去，不過出於 List 的本質，這樣寫也是不可以的。

## 什麼是 Guards

模式用來檢查一個值是否合適並從中取值，而 guard 則用來檢查一個值的某項屬性是否為真。咋一聽有點像是 `if` 語句，實際上也正是如此。不過處理多個條件分支時 guard 的可讀性要高些，並且與模式匹配契合的很好。

![](img/guards.png)

在講解它的語法前，我們先看一個用到 guard 的函數。它會依據你的 BMI 值 \(body mass index，身體質量指數\)來不同程度地侮辱你。BMI 值即為體重除以身高的平方。如果小於 18.5，就是太瘦；如果在 18.5 到 25 之間，就是正常；25 到 30 之間，超重；如果超過 30，肥胖。這就是那個函數\(我們目前暫不為您計算 BMI，它只是直接取一個 BMI 值\)。

```haskell
bmiTell :: (RealFloat a) => a -> String  
bmiTell bmi  
    | bmi <= 18.5 = "You're underweight, you emo, you!"  
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise   = "You're a whale, congratulations!"
```

guard 由跟在函數名及參數後面的豎綫標誌，通常他們都是靠右一個縮進排成一列。一個 guard 就是一個布爾表達式，如果為真，就使用其對應的函數體。如果為假，就送去見下一個 guard，如之繼續。如果我們用 24.3 呼叫這個函數，它就會先檢查它是否小於等於 18.5，顯然不是，於是見下一個 guard。24.3 小於 25.0，因此通過了第二個 guard 的檢查，就返回第二個字串。

在這裡則是相當的簡潔，不過不難想象這在命令式語言中又會是怎樣的一棵 if-else 樹。由於 if-else 的大樹比較雜亂，若是出現問題會很難發現，guard 對此則十分清楚。

最後的那個 guard 往往都是 `otherwise`，它的定義就是簡單一個 `otherwise = True` ，捕獲一切。這與模式很相像，只是模式檢查的是匹配，而它們檢查的是布爾表達式 。如果一個函數的所有 guard 都沒有通過\(而且沒有提供 `otherwise` 作萬能匹配\)，就轉入下一模式。這便是 guard 與模式契合的地方。如果始終沒有找到合適的 guard 或模式，就會發生一個錯誤。

當然，guard 可以在含有任意數量參數的函數中使用。省得用戶在使用這函數之前每次都自己計算 `bmi`。我們修改下這個函數，讓它取身高體重為我們計算。

```haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"  
    | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise                 = "You're a whale, congratulations!"
```

你可以測試自己胖不胖。

```haskell
ghci> bmiTell 85 1.90  
"You're supposedly normal. Pffft, I bet you're ugly!"
```

運行的結果是我不太胖。不過程式卻說我很醜。

要注意一點，函數的名字和參數的後面並沒有 `=`。許多初學者會造成語法錯誤，就是因為在後面加上了 `=`。

另一個簡單的例子：寫個自己的 `max` 函數。應該還記得，它是取兩個可比較的值，返回較大的那個。

```haskell
max' :: (Ord a) => a -> a -> a  
max' a b   
    | a > b     = a  
    | otherwise = b
```

guard 也可以塞在一行裡面。但這樣會喪失可讀性，因此是不被鼓勵的。即使是較短的函數也是如此，不過出於展示，我們可以這樣重寫 `max'`：

```haskell
max' :: (Ord a) => a -> a -> a  
max' a b | a > b = a | otherwise = b
```

這樣的寫法根本一點都不容易讀。

我們再來試試用 guard 實現我們自己的 `compare` 函數：

```haskell
myCompare :: (Ord a) => a -> a -> Ordering  
a `myCompare` b  
    | a > b     = GT  
    | a == b    = EQ  
    | otherwise = LT
```

```haskell
ghci> 3 `myCompare` 2  
GT
```

```text
*Note*：通過反單引號，我們不僅可以以中綴形式呼叫函數，也可以在定義函數的時候使用它。有時這樣會更易讀。
```

## 關鍵字 Where

前一節中我們寫了這個 `bmi` 計算函數：

```haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"  
    | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise                   = "You're a whale, congratulations!"
```

注意，我們重複了 3 次。我們重複了 3 次。程式設計師的字典裡不應該有"重複"這個詞。既然發現有重複，那麼給它一個名字來代替這三個表達式會更好些。嗯，我們可以這樣修改：

```haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | bmi <= 18.5 = "You're underweight, you emo, you!"  
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise   = "You're a whale, congratulations!"  
    where bmi = weight / height ^ 2
```

我們的 `where` 關鍵字跟在 guard 後面\(最好是與豎綫縮進一致\)，可以定義多個名字和函數。這些名字對每個 guard 都是可見的，這一來就避免了重複。如果我們打算換種方式計算 `bmi`，只需進行一次修改就行了。通過命名，我們提升了程式碼的可讀性，並且由於 `bmi` 只計算了一次，函數的執行效率也有所提升。我們可以再做下修改：

```haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | bmi <= skinny = "You're underweight, you emo, you!"  
    | bmi <= normal = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= fat    = "You're fat! Lose some weight, fatty!"  
    | otherwise     = "You're a whale, congratulations!"  
    where bmi = weight / height ^ 2  
          skinny = 18.5  
          normal = 25.0  
          fat = 30.0
```

函數在 `where` 綁定中定義的名字只對本函數可見，因此我們不必擔心它會污染其他函數的命名空間。注意，其中的名字都是一列垂直排開，如果不這樣規範，Haskell 就搞不清楚它們在哪個地方了。

`where` 綁定不會在多個模式中共享。如果你在一個函數的多個模式中重複用到同一名字，就應該把它置於全局定義之中。

`where` 綁定也可以使用_模式匹配_！前面那段程式碼可以改成：

```haskell
...  
where bmi = weight / height ^ 2  
      (skinny, normal, fat) = (18.5, 25.0, 30.0)
```

我們再搞個簡單函數，讓它告訴我們姓名的首字母：

```haskell
initials :: String -> String -> String  
initials firstname lastname = [f] ++ ". " ++ [l] ++ "."  
    where (f:_) = firstname  
          (l:_) = lastname
```

我們完全按可以在函數的參數上直接使用模式匹配\(這樣更短更簡潔\)，在這裡只是為了演示在 `where` 語句中同樣可以使用模式匹配：

`where` 綁定可以定義名字，也可以定義函數。保持健康的程式語言風格，我們搞個計算一組 `bmi` 的函數：

```haskell
calcBmis :: (RealFloat a) => [(a, a)] -> [a]  
calcBmis xs = [bmi w h | (w, h) <- xs] 
    where bmi weight height = weight / height ^ 2
```

這就全了！在這裡將 `bmi` 搞成一個函數，是因為我們不能依據參數直接進行計算，而必須先從傳入函數的 List 中取出每個序對並計算對應的值。

`where` 綁定還可以一層套一層地來使用。 有個常見的寫法是，在定義一個函數的時候也寫幾個輔助函數擺在 `where` 綁定中。 而每個輔助函數也可以透過 `where` 擁有各自的輔助函數。

## 關鍵字 Let

`let` 綁定與 `where` 綁定很相似。`where` 綁定是在函數底部定義名字，對包括所有 guard 在內的整個函數可見。`let` 綁定則是個表達式，允許你在任何位置定義局部變數，而對不同的 guard 不可見。正如 Haskell 中所有賦值結構一樣，`let` 綁定也可以使用模式匹配。看下它的實際應用！這是個依據半徑和高度求圓柱體表面積的函數：

```haskell
cylinder :: (RealFloat a) => a -> a -> a  
cylinder r h = 
    let sideArea = 2 * pi * r * h  
        topArea = pi * r ^2  
    in  sideArea + 2 * topArea
```

![](img/letitbe.png)

`let` 的格式為 `let [bindings] in [expressions]`。在 `let` 中綁定的名字僅對 `in` 部分可見。`let` 裡面定義的名字也得對齊到一列。不難看出，這用 `where` 綁定也可以做到。那麼它倆有什麼區別呢？看起來無非就是，`let` 把綁定放在語句前面而 `where` 放在後面嘛。

不同之處在於，`let` 綁定本身是個表達式，而 `where` 綁定則是個語法結構。還記得前面我們講if語句時提到它是個表達式，因而可以隨處安放？

```haskell
ghci> [if 5 > 3 then "Woo" else "Boo", if 'a' > 'b' then "Foo" else "Bar"]  
["Woo", "Bar"]  
ghci> 4 * (if 10 > 5 then 10 else 0) + 2  
42
```

用 `let` 綁定也可以實現：

```haskell
ghci> 4 * (let a = 9 in a + 1) + 2  
42
```

`let` 也可以定義局部函數：

```haskell
ghci> [let square x = x * x in (square 5, square 3, square 2)]  
[(25,9,4)]
```

若要在一行中綁定多個名字，再將它們排成一列顯然是不可以的。不過可以用分號將其分開。

```haskell
ghci> (let a = 100; b = 200; c = 300 in a*b*c, let foo="Hey "; bar = "there!" in foo ++ bar)  
(6000000,"Hey there!")
```

最後那個綁定後面的分號不是必須的，不過加上也沒關係。如我們前面所說，你可以在 `let` 綁定中使用模式匹配。這在從 Tuple 取值之類的操作中很方便。

```haskell
ghci> (let (a,b,c) = (1,2,3) in a+b+c) * 100  
600
```

你也可以把 `let` 綁定放到 List Comprehension 中。我們重寫下那個計算 `bmi` 值的函數，用個 `let` 替換掉原先的 `where`。

```haskell
calcBmis :: (RealFloat a) => [(a, a)] -> [a]  
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2]
```

List Comprehension 中 `let` 綁定的樣子和限制條件差不多，只不過它做的不是過濾，而是綁定名字。`let` 中綁定的名字在輸出函數及限制條件中都可見。這一來我們就可以讓我們的函數隻返回胖子的 `bmi` 值：

```haskell
calcBmis :: (RealFloat a) => [(a, a)] -> [a]  
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2, bmi >= 25.0]
```

在 `(w, h) <- xs` 這裡無法使用 `bmi` 這名字，因為它在 `let` 綁定的前面。

在 List Comprehension 中我們忽略了 `let` 綁定的 `in` 部分，因為名字的可見性已經預先定義好了。不過，把一個 `let...in` 放到限制條件中也是可以的，這樣名字只對這個限制條件可見。在 ghci 中 `in` 部分也可以省略，名字的定義就在整個交互中可見。

```haskell
ghci> let zoot x y z = x * y + z  
ghci> zoot 3 9 2  
29  
ghci> let boot x y z = x * y + z in boot 3 4 2  
14  
ghci> boot  
< interactive>:1:0: Not in scope: `boot'
```

你說既然 `let` 已經這麼好了，還要 `where` 幹嘛呢？嗯，`let` 是個表達式，定義域限制的相當小，因此不能在多個 guard 中使用。一些朋友更喜歡 `where`，因為它是跟在函數體後面，把主函數體距離型別聲明近一些會更易讀。

## Case expressions

![](img/case.png)

有命令式程式語言 \(C, C++, Java, etc.\) 的經驗的同學一定會有所瞭解，很多命令式語言都提供了 `case` 語句。就是取一個變數，按照對變數的判斷選擇對應的程式碼塊。其中可能會存在一個萬能匹配以處理未預料的情況。

Haskell 取了這一概念融合其中。如其名，`case` 表達式就是，嗯，一種表達式。跟 `if..else` 和 `let` 一樣的表達式。用它可以對變數的不同情況分別求值，還可以使用模式匹配。Hmm，取一個變數，對它模式匹配，執行對應的程式碼塊。好像在哪兒聽過？啊，就是函數定義時參數的模式匹配！好吧，模式匹配本質上不過就是 `case` 語句的語法糖而已。這兩段程式碼就是完全等價的：

```haskell
head' :: [a] -> a  
head' [] = error "No head for empty lists!"  
head' (x:_) = x
```

```haskell
head' :: [a] -> a  
head' xs = case xs of [] -> error "No head for empty lists!"  
                      (x:_) -> x
```

看得出，_case_表達式的語法十分簡單：

```haskell
case expression of pattern -> result  
                   pattern -> result  
                   pattern -> result  
                   ...
```

expression 匹配合適的模式。 一如預期地，第一個模式若匹配，就執行第一個區塊的程式碼；否則就接下去比對下一個模式。如果到最後依然沒有匹配的模式，就會產生運行時錯誤。

函數參數的模式匹配只能在定義函數時使用，而 `case` 表達式可以用在任何地方。例如：

```haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ case xs of [] -> "empty."  
                                               [x] -> "a singleton list."   
                                               xs -> "a longer list."
```

這在表達式中作模式匹配很方便，由於模式匹配本質上就是 `case` 表達式的語法糖，那麼寫成這樣也是等價的：

```haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ what xs  
    where what [] = "empty."  
          what [x] = "a singleton list."  
          what xs = "a longer list."
```

