# 模組 \(Modules\)

## 裝載模組

![](img/modules.png)

Haskell 中的模組是含有一組相關的函數，型別和型別類的組合。而 Haskell 程序的本質便是從主模組中引用其它模組並呼叫其中的函數來執行操作。這樣可以把程式碼分成多塊，只要一個模組足夠的獨立，它裡面的函數便可以被不同的程序反覆重用。這就讓不同的程式碼各司其職，提高了程式碼的健壯性。

Haskell 的標準庫就是一組模組，每個模組都含有一組功能相近或相關的函數和型別。有處理 List 的模組，有處理並發的模組，也有處理複數的模組，等等。目前為止我們談及的所有函數,型別以及型別類都是 `Prelude` 模組的一部分，它預設自動裝載。在本章，我們看一下幾個常用的模組，在開始瀏覽其中的函數之前，我們先得知道如何裝載模組.

在 Haskell中，裝載模組的語法為 `import`，這必須得在函數的定義之前，所以一般都是將它置於程式碼的頂部。無疑，一段程式碼中可以裝載很多模組，只要將 `import` 語句分行寫開即可。裝載 `Data.List` 試下，它裡面有很多實用的 List 處理函數.

執行 `import Data.List`，這樣一來 `Data.List` 中包含的所有函數就都進入了全局命名空間。也就是說，你可以在程式碼的任意位置呼叫這些函數.`Data.List` 模組中有個 `nub` 函數，它可以篩掉一個 List 中的所有重複元素。用點號將 `length` 和 `nub` 組合: `length . nub`，即可得到一個與 `(\xs -> length (nub xs))` 等價的函數。

```haskell
import Data.List  

numUniques :: (Eq a) => [a] -> Int  
numUniques = length . nub
```

你也可以在 ghci 中裝載模組，若要呼叫 `Data.List` 中的函數，就這樣:

```haskell
ghci> :m Data.List
```

若要在 ghci 中裝載多個模組，不必多次 `:m` 命令，一下就可以全部搞定:

```haskell
ghci> :m Data.List Data.Map Data.Set
```

而你的程序中若已經有包含的程式碼，就不必再用 `:m` 了.

如果你只用得到某模組的兩個函數，大可僅包含它倆。若僅裝載 `Data.List` 模組 `nub` 和 `sort`，就這樣:

```haskell
import Data.List (nub，sort)
```

也可以只包含除去某函數之外的其它函數，這在避免多個模組中函數的命名衝突很有用。假設我們的程式碼中已經有了一個叫做 `nub` 的函數，而裝入 `Data.List` 模組時就要把它裡面的 `nub` 除掉.

```haskell
import Data.List hiding (nub)
```

避免命名衝突還有個方法，便是 `qualified import`，`Data.Map` 模組提供一了一個按鍵索值的資料結構，它裡面有幾個和 `Prelude` 模組重名的函數。如 `filter` 和 `null`，裝入 `Data.Map` 模組之後再呼叫 `filter`，Haskell 就不知道它究竟是哪個函數。如下便是解決的方法:

```haskell
import qualified Data.Map
```

這樣一來，再呼叫 `Data.Map` 中的 `filter` 函數，就必須得 `Data.Map.filter`，而 `filter` 依然是為我們熟悉喜愛的樣子。但是要在每個函數前面都加 `個Data.Map` 實在是太煩人了! 那就給它起個別名，讓它短些:

```haskell
import qualified Data.Map as M
```

好，再呼叫 `Data.Map` 模組的 `filter` 函數的話僅需 `M.filter` 就行了

要瀏覽所有的標準庫模組，參考這個手冊。翻閲標準庫中的模組和函數是提升個人 Haskell 水平的重要途徑。你也可以各個模組的原始碼，這對 Haskell 的深入學習及掌握都是大有好處的.

檢索函數或搜尋函數位置就用 \[[http://www.Haskell.org/hoogle/](http://www.Haskell.org/hoogle/) Hoogle\]，相當了不起的 Haskell 搜索引擎! 你可以用函數名，模組名甚至型別聲明來作為檢索的條件.

## Data.List

顯而易見，`Data.List` 是關於 List 操作的模組，它提供了一組非常有用的 List 處理函數。在前面我們已經見過了其中的幾個函數\(如 `map` 和 `filter`\)，這是 `Prelude` 模組出於方便起見，導出了幾個 `Data.List` 裡的函數。因為這幾個函數是直接引用自 `Data.List`，所以就無需使用 `qualified import`。在下面，我們來看看幾個以前沒見過的函數:

**intersperse** 取一個元素與 List 作參數，並將該元素置於 List 中每對元素的中間。如下是個例子:

```haskell
ghci> intersperse '.' "MONKEY"  
"M.O.N.K.E.Y"  
ghci> intersperse 0 [1,2,3,4,5,6]  
[1,0,2,0,3,0,4,0,5,0,6]
```

**intercalate** 取兩個 List 作參數。它會將第一個 List 交叉插入第二個 List 中間，並返回一個 List.

```haskell
ghci> intercalate " " ["hey","there","guys"]  
"hey there guys"  
ghci> intercalate [0,0,0] [[1,2,3],[4,5,6],[7,8,9]]  
[1,2,3,0,0,0,4,5,6,0,0,0,7,8,9]
```

**transpose** 函數可以反轉一組 List 的 List。你若把一組 List 的 List 看作是個 2D 的矩陣，那 `transpose` 的操作就是將其列為行。

```haskell
ghci> transpose [[1,2,3],[4,5,6],[7,8,9]]  
[[1,4,7],[2,5,8],[3,6,9]]  
ghci> transpose ["hey","there","guys"]  
["htg","ehu","yey","rs","e"]
```

假如有兩個多項式 `3x<sup>2</sup> + 5x + 9`，`10x<sup>3</sup> + 9` 和 `8x<sup>3</sup> + 5x<sup>2</sup> + x - 1`，將其相加，我們可以列三個 List: `[0,3,5,9]`，`[10,0,0,9]` 和 `[8,5,1,-1]` 來表示。再用如下的方法取得結果.

```haskell
ghci> map sum $ transpose [[0,3,5,9],[10,0,0,9],[8,5,1,-1]]  
[18,8,6,17]
```

![](img/legolists.png)

使用 `transpose` 處理這三個 List 之後，三次冪就到了第一行，二次冪到了第二行，以此類推。在用 `sum` 函數將其映射，即可得到正確的結果。

**foldl'** 和 **foldl1'** 是它們各自惰性實現的嚴格版本。在用 `fold` 處理較大的 List 時，經常會遇到堆棧溢出的問題。而這罪魁禍首就是 `fold` 的惰性: 在執行 `fold` 時，累加器的值並不會被立即更新，而是做一個"在必要時會取得所需的結果"的承諾。每過一遍累加器，這一行為就重複一次。而所有的這堆"承諾"最終就會塞滿你的堆棧。嚴格的 `fold` 就不會有這一問題，它們不會作"承諾"，而是直接計算中間值的結果並繼續執行下去。如果用惰性 `fold` 時經常遇到溢出錯誤，就應換用它們的嚴格版。

**concat** 把一組 List 連接為一個 List。

```haskell
ghci> concat ["foo","bar","car"]  
"foobarcar"  
ghci> concat [[3,4,5],[2,3,4],[2,1,1]]  
[3,4,5,2,3,4,2,1,1]
```

它相當於移除一級嵌套。若要徹底地連接其中的元素，你得 `concat` 它兩次才行.

**concatMap** 函數與 `map` 一個 List 之後再 `concat` 它等價.

```haskell
ghci> concatMap (replicate 4) [1..3]  
[1,1,1,1,2,2,2,2,3,3,3,3]
```

**and** 取一組布林值 List 作參數。只有其中的值全為 `True` 的情況下才會返回 `True`。

```haskell
ghci> and $ map (>4) [5,6,7,8]  
True  
ghci> and $ map (==4) [4,4,4,3,4]  
False
```

**or** 與 `and` 相似，一組布林值 List 中若存在一個 `True` 它就返回 `True`.

```haskell
ghci> or $ map (==4) [2,3,4,5,6,1]  
True  
ghci> or $ map (>4) [1,2,3]  
False
```

**any** 和 **all** 取一個限制條件和一組布林值 List 作參數，檢查是否該 List 的某個元素或每個元素都符合該條件。通常較 `map` 一個 List 到 `and` 或 `or` 而言，使用 `any` 或 `all` 會更多些。

```haskell
ghci> any (==4) [2,3,5,6,1,4]  
True  
ghci> all (>4) [6,9,10]  
True  
ghci> all (`elem` ['A'..'Z']) "HEYGUYSwhatsup"  
False  
ghci> any (`elem` ['A'..'Z']) "HEYGUYSwhatsup"  
True
```

**iterate** 取一個函數和一個值作參數。它會用該值去呼叫該函數並用所得的結果再次呼叫該函數，產生一個無限的 List.

```haskell
ghci> take 10 $ iterate (*2) 1  
[1,2,4,8,16,32,64,128,256,512]  
ghci> take 3 $ iterate (++ "haha") "haha"  
["haha","hahahaha","hahahahahaha"]
```

**splitAt** 取一個 List 和數值作參數，將該 List 在特定的位置斷開。返回一個包含兩個 List 的二元組.

```haskell
ghci> splitAt 3 "heyman"  
("hey","man")  
ghci> splitAt 100 "heyman"  
("heyman","")  
ghci> splitAt (-3) "heyman"  
("","heyman")  
ghci> let (a,b) = splitAt 3 "foobar" in b ++ a  
"barfoo"
```

**takeWhile** 這一函數十分的實用。它從一個 List 中取元素，一旦遇到不符合條件的某元素就停止.

```haskell
ghci> takeWhile (>3) [6,5,4,3,2,1,2,3,4,5,4,3,2,1]  
[6,5,4]  
ghci> takeWhile (/=' ') "This is a sentence"  
"This"
```

如果要求所有三次方小於 1000 的數的和，用 `filter` 來過濾 `map (^3) [1..]` 所得結果中所有小於 1000 的數是不行的。因為對無限 List 執行的 `filter` 永遠都不會停止。你已經知道了這個 List 是單增的，但 Haskell 不知道。所以應該這樣：

```haskell
ghci> sum $ takeWhile (<10000) $ map (^3) [1..]  
53361
```

用 `(^3)` 處理一個無限 List，而一旦出現了大於 10000 的元素這個 List 就被切斷了，sum 到一起也就輕而易舉.

**dropWhile** 與此相似，不過它是扔掉符合條件的元素。一旦限制條件返回 `False`，它就返回 List 的餘下部分。方便實用!

```haskell
ghci> dropWhile (/=' ') "This is a sentence"  
" is a sentence"  
ghci> dropWhile (<3) [1,2,2,2,3,4,5,4,3,2,1]  
[3,4,5,4,3,2,1]
```

給一 `Tuple` 組成的 List，這 Tuple 的首项表示股票價格，第二三四項分別表示年,月,日。我們想知道它是在哪天首次突破 $1000 的!

```haskell
ghci> let stock = [(994.4,2008,9,1),(995.2,2008,9,2),(999.2,2008,9,3),(1001.4,2008,9,4),(998.3,2008,9,5)]  
ghci> head (dropWhile (\(val,y,m,d) -> val < 1000) stock)  
(1001.4,2008,9,4)
```

**span** 與 `takeWhile` 有點像，只是它返回兩個 List。第一個 List 與同參數呼叫 `takeWhile` 所得的結果相同，第二個 List 就是原 List 中餘下的部分。

```haskell
ghci> let (fw，rest) = span (/=' ') "This is a sentence" in "First word:" ++ fw ++ "，the rest:" ++ rest  
"First word: This，the rest: is a sentence"
```

**span** 是在條件首次為 `False` 時斷開 List，而 `break` 則是在條件首次為 `True` 時斷開 `List`。`break p` 與 `span (not . p)` 是等價的.

```haskell
ghci> break (==4) [1,2,3,4,5,6,7]  
([1,2,3],[4,5,6,7])  
ghci> span (/=4) [1,2,3,4,5,6,7]  
([1,2,3],[4,5,6,7])
```

**break** 返回的第二個 List 就會以第一個符合條件的元素開頭。

**sort** 可以排序一個 List，因為只有能夠作比較的元素才可以被排序，所以這一 List 的元素必須是 Ord 型別類的實例型別。

```haskell
ghci> sort [8,5,3,2,1,6,4,2]  
[1,2,2,3,4,5,6,8]  
ghci> sort "This will be sorted soon"  
" Tbdeehiillnooorssstw"
```

**group** 取一個 List 作參數，並將其中相鄰並相等的元素各自歸類，組成一個個子 List.

```haskell
ghci> group [1,1,1,1,2,2,2,2,3,3,2,2,2,5,6,7]  
[[1,1,1,1],[2,2,2,2],[3,3],[2,2,2],[5],[6],[7]]
```

若在 `group` 一個 List 之前給它排序就可以得到每個元素在該 List 中的出現次數。

```haskell
ghci> map (\l@(x:xs) -> (x,length l)) . group . sort $ [1,1,1,1,2,2,2,2,3,3,2,2,2,5,6,7]  
[(1,4),(2,7),(3,2),(5,1),(6,1),(7,1)]
```

**inits** 和 **tails** 與 `init` 和 `tail` 相似，只是它們會遞歸地呼叫自身直到什麼都不剩，看:

```haskell
ghci> inits "w00t"  
["","w","w0","w00","w00t"]  
ghci> tails "w00t"  
["w00t","00t","0t","t",""]  
ghci> let w = "w00t" in zip (inits w) (tails w)  
[("","w00t"),("w","00t"),("w0","0t"),("w00","t"),("w00t","")]
```

我們用 `fold` 實現一個搜索子 List 的函數:

```haskell
search :: (Eq a) => [a] -> [a] -> Bool  
search needle haystack =  
  let nlen = length needle  
  in foldl (\acc x -> if take nlen x == needle then True else acc) False (tails haystack)
```

首先，對搜索的 List 呼叫 `tails`，然後遍歷每個 List 來檢查它是不是我們想要的.

由此我們便實現了一個類似 **isInfixOf** 的函數，**isInfixOf** 從一個 List 中搜索一個子 List，若該 List 包含子 List，則返回 `True`.

```haskell
ghci> "cat" `isInfixOf` "im a cat burglar"  
True  
ghci> "Cat" `isInfixOf` "im a cat burglar"  
False  
ghci> "cats" `isInfixOf` "im a cat burglar"  
False
```

**isPrefixOf** 與 **isSuffixOf** 分別檢查一個 List 是否以某子 List 開頭或者結尾.

```haskell
ghci> "hey" `isPrefixOf` "hey there!"  
True  
ghci> "hey" `isPrefixOf` "oh hey there!"  
False  
ghci> "there!" `isSuffixOf` "oh hey there!"  
True  
ghci> "there!" `isSuffixOf` "oh hey there"  
False
```

**elem** 與 **notElem** 檢查一個 List 是否包含某元素.

**partition** 取一個限制條件和 List 作參數，返回兩個 List，第一個 List 中包含所有符合條件的元素，而第二個 List 中包含餘下的.

```haskell
ghci> partition (`elem` ['A'..'Z']) "BOBsidneyMORGANeddy"  
("BOBMORGAN","sidneyeddy")  
ghci> partition (>3) [1,3,5,6,3,2,1,0,3,7]  
([5,6,7],[1,3,3,2,1,0,3])
```

瞭解這個與 `span` 和 `break` 的差異是很重要的.

```haskell
ghci> span (`elem` ['A'..'Z']) "BOBsidneyMORGANeddy"  
("BOB","sidneyMORGANeddy")
```

`span` 和 `break` 會在遇到第一個符合或不符合條件的元素處斷開，而 `partition` 則會遍歷整個 List。

**find** 取一個 List 和限制條件作參數，並返回首個符合該條件的元素，而這個元素是個 `Maybe` 值。在下章，我們將深入地探討相關的算法和資料結構，但在這裡你只需瞭解 `Maybe` 值是 `Just something` 或 `Nothing` 就夠了。與一個 List 可以為空也可以包含多個元素相似，一個 `Maybe` 可以為空，也可以是單一元素。同樣與 List 類似，一個 Int 型的 List 可以寫作 `[Int]`，`Maybe`有個 Int 型可以寫作 `Maybe Int`。先試一下 `find` 函數再說.

```haskell
ghci> find (>4) [1,2,3,4,5,6]  
Just 5  
ghci> find (>9) [1,2,3,4,5,6]  
Nothing  
ghci> :t find  
find :: (a -> Bool) -> [a] -> Maybe a
```

注意一下 `find` 的型別，它的返回結果為 `Maybe a`，這與 `[a]` 的寫法有點像，只是 `Maybe` 型的值只能為空或者單一元素，而 List 可以為空,一個元素，也可以是多個元素.

想想前面那段找股票的程式碼，`head (dropWhile (\(val,y,m,d) -> val < 1000) stock)` 。但 `head` 並不安全! 如果我們的股票沒漲過 $1000 會怎樣? `dropWhile` 會返回一個空 List，而對空 List 取 `head` 就會引發一個錯誤。把它改成 `find (\(val,y,m,d) -> val > 1000) stock` 就安全多啦，若存在合適的結果就得到它, 像 `Just (1001.4,2008,9,4)`，若不存在合適的元素\(即我們的股票沒有漲到過 $1000\)，就會得到一個 `Nothing`.

**elemIndex** 與 `elem` 相似，只是它返回的不是布林值，它只是'可能' \(Maybe\)返回我們找的元素的索引，若這一元素不存在，就返回 `Nothing`。

```haskell
ghci> :t elemIndex  
elemIndex :: (Eq a) => a -> [a] -> Maybe Int  
ghci> 4 `elemIndex` [1,2,3,4,5,6]  
Just 3  
ghci> 10 `elemIndex` [1,2,3,4,5,6]  
Nothing
```

**elemIndices** 與 `elemIndex` 相似，只不過它返回的是 List，就不需要 `Maybe` 了。因為不存在用空 List 就可以表示，這就與 `Nothing` 相似了.

```haskell
ghci> ' ' `elemIndices` "Where are the spaces?"  
[5,9,13]
```

**findIndex** 與 `find` 相似，但它返回的是可能存在的首個符合該條件元素的索引。**findIndices** 會返回所有符合條件的索引.

```haskell
ghci> findIndex (==4) [5,3,2,1,6,4]  
Just 5  
ghci> findIndex (==7) [5,3,2,1,6,4]  
Nothing  
ghci> findIndices (`elem` ['A'..'Z']) "Where Are The Caps?"  
[0,6,10,14]
```

在前面，我們講過了 `zip` 和 `zipWith`，它們只能將兩個 List 組到一個二元組數或二參函數中，但若要組三個 List 該怎麼辦? 好說~ 有 `zip3`,`zip4`...,和 `zipWith3`, `zipWith4`...直到 7。這看起來像是個 hack，但工作良好。連着組 8 個 List 的情況很少遇到。還有個聰明辦法可以組起無限多個 List，但限於我們目前的水平，就先不談了.

```haskell
ghci> zipWith3 (\x y z -> x + y + z) [1,2,3] [4,5,2,2] [2,2,3]  
[7,9,8]  
ghci> zip4 [2,3,3] [2,2,2] [5,5,3] [2,2,2]  
[(2,2,5,2),(3,2,5,2),(3,2,3,2)]
```

與普通的 `zip` 操作相似，以返回的 List 中長度最短的那個為準.

在處理來自檔案或其它地方的輸入時，**lines** 會非常有用。它取一個字串作參數。並返回由其中的每一行組成的 List.

```haskell
ghci> lines "first line\nsecond line\nthird line"  
["first line","second line","third line"]
```

`'\n'` 表示unix下的換行符，在 Haskell 的字元中，反斜杠表示特殊字元.

**unlines** 是 `lines` 的反函數，它取一組字串的 List，並將其通過 `'\n'`合併到一塊.

```haskell
ghci> unlines ["first line"，"second line"，"third line"]  
"first line\nsecond line\nthird line\n"
```

**words** 和 **unwords** 可以把一個字串分為一組單詞或執行相反的操作，很有用.

```haskell
ghci> words "hey these are the words in this sentence"  
["hey","these","are","the","words","in","this","sentence"]  
ghci> words "hey these are the words in this\nsentence"  
["hey","these","are","the","words","in","this","sentence"]  
ghci> unwords ["hey","there","mate"]  
"hey there mate"
```

我們前面講到了 **nub**，它可以將一個 List 中的重複元素全部篩掉，使該 List 的每個元素都如雪花般獨一無二，'nub' 的含義就是'一小塊'或'一部分'，用在這裡覺得很古怪。我覺得，在函數的命名上應該用更確切的詞語，而避免使用老掉牙的過時詞彙.

```haskell
ghci> nub [1,2,3,4,3,2,1,2,3,4,3,2,1]  
[1,2,3,4]  
ghci> nub "Lots of words and stuff"  
"Lots fwrdanu"
```

**delete** 取一個元素和 List 作參數，會刪掉該 List 中首次出現的這一元素.

```haskell
ghci> delete 'h' "hey there ghang!"  
"ey there ghang!"  
ghci> delete 'h' . delete 'h' $ "hey there ghang!"  
"ey tere ghang!"  
ghci> delete 'h' . delete 'h' . delete 'h' $ "hey there ghang!"  
"ey tere gang!"
```

**\** 表示 List 的差集操作，這與集合的差集很相似，它會從左邊 List 中的元素扣除存在於右邊 List 中的元素一次.

```haskell
ghci> [1..10] \\ [2,5,9]  
[1,3,4,6,7,8,10]  
ghci> "Im a big baby" \\ "big"  
"Im a  baby"
```

**union** 與集合的並集也是很相似，它返回兩個 List 的並集，即遍歷第二個 List 若存在某元素不屬於第一個 List，則追加到第一個 List。看，第二個 List 中的重複元素就都沒了!

```haskell
ghci> "hey man" `union` "man what's up"  
"hey manwt'sup"  
ghci> [1..7] `union` [5..10]  
[1,2,3,4,5,6,7,8,9,10]
```

**intersection** 相當於集合的交集。它返回兩個 List 的相同部分.

```haskell
ghci> [1..7] `intersect` [5..10]  
[5,6,7]
```

**insert** 可以將一個元素插入一個可排序的 List，並將其置於首個大於等於它的元素之前，如果使用 `insert` 來給一個排過序的 List 插入元素，返回的結果依然是排序的.

```haskell
ghci> insert 4 [1,2,3,5,6,7]  
[1,2,3,4,5,6,7]  
ghci> insert 'g' $ ['a'..'f'] ++ ['h'..'z']  
"abcdefghijklmnopqrstuvwxyz"  
ghci> insert 3 [1,2,4,3,2,1]  
[1,2,3,4,3,2,1]
```

`length`，`take`，`drop`，`splitAt`，`!!` 和 `replicate` 之類的函數有個共同點。那就是它們的參數中都有個 Int 值（或者返回Int值），我覺得使用 Intergal 或 Num 型別類會更好，但出於歷史原因，修改這些會破壞掉許多既有的程式碼。在 `Data.List` 中包含了更通用的替代版，如: `genericLength，genericTake，genericDrop，genericSplitAt，genericIndex` 和 `genericReplicate`。`length` 的型別聲明為 `length :: [a] -> Int`，而我們若要像這樣求它的平均值，`let xs = [1..6] in sum xs / length xs` ，就會得到一個型別錯誤，因為 `/` 運算符不能對 Int 型使用! 而 `genericLength` 的型別聲明則為 `genericLength :: (Num a) => [b] -> a`，Num 既可以是整數又可以是浮點數，`let xs = [1..6] in sum xs / genericLength xs` 這樣再求平均數就不會有問題了.

`nub`, `delete`, `union`, `intsect` 和 `group` 函數也有各自的通用替代版 `nubBy`，`deleteBy`，`unionBy`，`intersectBy` 和 `groupBy`，它們的區別就是前一組函數使用 `(==)` 來測試是否相等，而帶 `By` 的那組則取一個函數作參數來判定相等性，`group` 就與 `groupBy (==)` 等價.

假如有個記錄某函數在每秒的值的 List，而我們要按照它小於零或者大於零的交界處將其分為一組子 List。如果用 `group`，它只能將相鄰並相等的元素組到一起，而在這裡我們的標準是它們是否互爲相反數。`groupBy` 登場! 它取一個含兩個參數的函數作為參數來判定相等性.

```haskell
ghci> let values = [-4.3，-2.4，-1.2，0.4，2.3，5.9，10.5，29.1，5.3，-2.4，-14.5，2.9，2.3]  
ghci> groupBy (\x y -> (x > 0) == (y > 0)) values  
[[-4.3,-2.4,-1.2],[0.4,2.3,5.9,10.5,29.1,5.3],[-2.4,-14.5],[2.9,2.3]]
```

這樣一來我們就可以很清楚地看出哪部分是正數，哪部分是負數，這個判斷相等性的函數會在兩個元素同時大於零或同時小於零時返回 `True`。也可以寫作 `\x y -> (x > 0) && (y > 0) || (x <= 0) && (y <= 0)`。但我覺得第一個寫法的可讀性更高。`Data.Function` 中還有個 `on` 函數可以讓它的表達更清晰，其定義如下:

```haskell
on :: (b -> b -> c) -> (a -> b) -> a -> a -> c  
f `on` g = \x y -> f (g x) (g y)
```

執行 ``(==) `on` (> 0)`` 得到的函數就與 `\x y -> (x > 0) == (y > 0)` 基本等價。`on` 與帶 `By` 的函數在一起會非常好用，你可以這樣寫:

```haskell
ghci> groupBy ((==) `on` (> 0)) values  
[[-4.3,-2.4,-1.2],[0.4,2.3,5.9,10.5,29.1,5.3],[-2.4,-14.5],[2.9,2.3]]
```

可讀性很高! 你可以大聲念出來: 按照元素是否大於零，給它分類！

同樣，`sort`，`insert`，`maximum` 和 `min` 都有各自的通用版本。如 `groupBy` 類似，**sortBy**，**insertBy**，**maximumBy** 和 **minimumBy** 都取一個函數來比較兩個元素的大小。像 `sortBy` 的型別聲明為: `sortBy :: (a -> a -> Ordering) -> [a] -> [a]`。前面提過，`Ordering` 型別可以有三個值,`LT`，`EQ` 和 `GT`。`compare` 取兩個 `Ord` 型別類的元素作參數，所以 `sort` 與 `sortBy compare` 等價.

List 是可以比較大小的，且比較的依據就是其中元素的大小。如果按照其子 List 的長度為標準當如何? 很好，你可能已經猜到了，`sortBy` 函數.

```haskell
ghci> let xs = [[5,4,5,4,4],[1,2,3],[3,5,4,3],[],[2],[2,2]]  
ghci> sortBy (compare `on` length) xs  
[[],[2],[2,2],[1,2,3],[3,5,4,3],[5,4,5,4,4]]
```

太絶了! ``compare `on` length``，乖乖，這簡直就是英文! 如果你搞不清楚 `on` 在這裡的原理，就可以認為它與 ``\x y -> length x `compare` length y`` 等價。通常，與帶 `By` 的函數打交道時，若要判斷相等性，則 ``(==) `on` something``。若要判定大小，則 ``compare `on` something``.

## Data.Char

如其名，`Data.Char` 模組包含了一組用於處理字元的函數。由於字串的本質就是一組字元的 List，所以往往會在 `filter` 或是 `map` 字串時用到它.

`Data.Char`模組中含有一系列用於判定字元範圍的函數，如下:

![](img/legochar.png)

**isControl** 判斷一個字元是否是控制字元。 **isSpace** 判斷一個字元是否是空格字元，包括空格，tab，換行符等. **isLower** 判斷一個字元是否為小寫. **isUper** 判斷一個字元是否為大寫。 **isAlpha** 判斷一個字元是否為字母. **isAlphaNum** 判斷一個字元是否為字母或數字. **isPrint** 判斷一個字元是否是可打印的. **isDigit** 判斷一個字元是否為數字. **isOctDigit** 判斷一個字元是否為八進制數字. **isHexDigit** 判斷一個字元是否為十六進制數字. **isLetter** 判斷一個字元是否為字母. **isMark** 判斷是否為 unicode 注音字元，你如果是法國人就會經常用到的. **isNumber** 判斷一個字元是否為數字. **isPunctuation** 判斷一個字元是否為標點符號. **isSymbol**判斷一個字元是否為貨幣符號. **isSeperater** 判斷一個字元是否為 unicode 空格或分隔符. **isAscii** 判斷一個字元是否在 unicode 字母表的前 128 位。 **isLatin1** 判斷一個字元是否在 unicode 字母表的前 256 位. **isAsciiUpper** 判斷一個字元是否為大寫的 ascii 字元. **isAsciiLower** 判斷一個字元是否為小寫的 ascii 字元.

以上所有判斷函數的型別聲明皆為 `Char -> Bool`，用到它們的絶大多數情況都無非就是過濾字串或類似操作。假設我們在寫個程序，它需要一個由字元和數字組成的用戶名。要實現對用戶名的檢驗，我們可以結合使用 `Data.List` 模組的 `all` 函數與 `Data.Char` 的判斷函數.

```haskell
ghci> all isAlphaNum "bobby283"  
True  
ghci> all isAlphaNum "eddy the fish!"  
False
```

Kewl~ 免得你忘記，`all` 函數取一個判斷函數和一個 List 做參數，若該 List 的所有元素都符合條件，就返回 `True`.

也可以使用 `isSpace` 來實現 `Data.List` 的 `words` 函數.

```haskell
ghci> words "hey guys its me"  
["hey","guys","its","me"]  
ghci> groupBy ((==) `on` isSpace) "hey guys its me"  
["hey"," ","guys"," ","its"," ","me"]  
ghci>
```

Hmm，不錯，有點 `words` 的樣子了。只是還有空格在裡面，恩，該怎麼辦? 我知道，用 `filter` 濾掉它們!

```haskell
ghci> filter (not . any isSpace) . groupBy ((==) `on` isSpace) $ "hey guys its me"  
["hey","guys","its","me"]
```

啊哈.

`Data.Char` 中也含有與 `Ordering` 相似的型別。`Ordering` 可以有三個值，`LT`，`GT` 和 `EQ`。這就是個枚舉，它表示了兩個元素作比較可能的結果. `GeneralCategory` 型別也是個枚舉，它表示了一個字元可能所在的分類。而得到一個字元所在分類的主要方法就是使用 `generalCategory` 函數.它的型別為: `generalCategory :: Char -> GeneralCategory`。那 31 個分類就不在此一一列出了，試下這個函數先:

```haskell
ghci> generalCategory ' '  
Space  
ghci> generalCategory 'A'  
UppercaseLetter  
ghci> generalCategory 'a'  
LowercaseLetter  
ghci> generalCategory '.'  
OtherPunctuation  
ghci> generalCategory '9'  
DecimalNumber  
ghci> map generalCategory " \t\nA9?|"  
[Space,Control,Control,UppercaseLetter,DecimalNumber,OtherPunctuation,MathSymbol]
```

由於 `GeneralCategory` 型別是 `Eq` 型別類的一部分，使用類似 `generalCategory c == Space` 的程式碼也是可以的.

**toUpper** 將一個字元轉為大寫字母，若該字元不是小寫字母，就按原值返回. **toLower** 將一個字元轉為小寫字母，若該字元不是大寫字母，就按原值返回. **toTitle** 將一個字元轉為 title-case，對大多數字元而言，title-case 就是大寫. **digitToInt** 將一個字元轉為 Int 值，而這一字元必須得在 `'1'..'9','a'..'f'`或`'A'..'F'` 的範圍之內.

```haskell
ghci> map digitToInt "34538"  
[3,4,5,3,8]  
ghci> map digitToInt "FF85AB"  
[15,15,8,5,10,11]
```

`intToDigit` 是 `digitToInt` 的反函數。它取一個 `0` 到 `15` 的 `Int` 值作參數，並返回一個小寫的字元.

```haskell
ghci> intToDigit 15  
'f'  
ghci> intToDigit 5  
'5'
```

**ord** 與 **char** 函數可以將字元與其對應的數字相互轉換.

```haskell
ghci> ord 'a'  
97  
ghci> chr 97  
'a'  
ghci> map ord "abcdefgh"  
[97,98,99,100,101,102,103,104]
```

兩個字元的 `ord` 值之差就是它們在 unicode 字元表上的距離.

_Caesar ciphar_ 是加密的基礎算法，它將消息中的每個字元都按照特定的字母表進行替換。它的實現非常簡單，我們這裡就先不管字母表了.

```haskell
encode :: Int -> String -> String  
encode shift msg = 
  let ords = map ord msg  
      shifted = map (+ shift) ords  
  in map chr shifted
```

先將一個字串轉為一組數字，然後給它加上某數，再轉回去。如果你是標準的組合牛仔，大可將函數寫為: `map (chr . (+ shift) . ord) msg`。試一下它的效果:

```haskell
ghci> encode 3 "Heeeeey"  
"Khhhhh|"  
ghci> encode 4 "Heeeeey"  
"Liiiii}"  
ghci> encode 1 "abcd"  
"bcde"  
ghci> encode 5 "Marry Christmas! Ho ho ho!"  
"Rfww~%Hmwnxyrfx&%Mt%mt%mt&"
```

不錯。再簡單地將它轉成一組數字，減去某數後再轉回來就是解密了.

```haskell
decode :: Int -> String -> String  
decode shift msg = encode (negate shift) msg
```

```haskell
ghci> encode 3 "Im a little teapot"  
"Lp#d#olwwoh#whdsrw"  
ghci> decode 3 "Lp#d#olwwoh#whdsrw"  
"Im a little teapot"  
ghci> decode 5 . encode 5 $ "This is a sentence"  
"This is a sentence"
```

## Data.Map

關聯列表\(也叫做字典\)是按照鍵值對排列而沒有特定順序的一種 List。例如，我們用關聯列表儲存電話號碼，號碼就是值，人名就是鍵。我們並不關心它們的存儲順序，只要能按人名得到正確的號碼就好.在 Haskell 中表示關聯列表的最簡單方法就是弄一個二元組的 List，而這二元組就首項為鍵，後項為值。如下便是個表示電話號碼的關聯列表:

```haskell
phoneBook = [("betty","555-2938") ,
             ("bonnie","452-2928") ,
             ("patsy","493-2928") ,
             ("lucille","205-2928") ,
             ("wendy","939-8282") ,
             ("penny","853-2492") ]
```

不理這貌似古怪的縮進，它就是一組二元組的 List 而已。話說對關聯列表最常見的操作就是按鍵索值，我們就寫個函數來實現它。

```haskell
findKey :: (Eq k) => k -> [(k,v)] -> v 
findKey key xs = snd . head . filter (\(k,v) -> key == k) $ xs
```

![](img/legomap.png)

簡潔漂亮。這個函數取一個鍵和 List 做參數，過濾這一 List 僅保留鍵匹配的項，並返迴首個鍵值對。但若該關聯列表中不存在這個鍵那會怎樣? 哼，那就會在試圖從空 List 中取 `head` 時引發一個運行時錯誤。無論如何也不能讓程序就這麼輕易地崩潰吧，所以就應該用 `Maybe` 型別。如果沒找到相應的鍵，就返回 `Nothing`。而找到了就返回 `Just something`。而這 `something` 就是鍵對應的值。

```haskell
findKey :: (Eq k) => k -> [(k,v)] -> Maybe v 
findKey key [] = Nothing
findKey key ((k,v):xs) = 
     if key == k then 
         Just v 
     else 
         findKey key xs
```

看這型別聲明，它取一個可判斷相等性的鍵和一個關聯列表做參數，可能 \(Maybe\) 得到一個值。聽起來不錯.這便是個標準的處理 List 的遞歸函數，邊界條件，分割 List，遞歸呼叫，都有了 -- 經典的 `fold` 模式。 看看用 `fold` 怎樣實現吧。

```haskell
findKey :: (Eq k) => k -> [(k,v)] -> Maybe v 
findKey key = foldr (\(k,v) acc -> if key == k then Just v else acc) Nothing
```

```text
*Note*: 通常，使用 ``fold`` 來替代類似的遞歸函數會更好些。用 ``fold`` 的程式碼讓人一目瞭然，而看明白遞歸則得多花點腦子。
```

```haskell
ghci> findKey "penny" phoneBook 
Just "853-2492" 
ghci> findKey "betty" phoneBook 
Just "555-2938" 
ghci> findKey "wilma" phoneBook 
Nothing
```

如魔咒般靈驗! 只要我們有這姑娘的號碼就 `Just` 可以得到，否則就是 `Nothing`. 方纔我們實現的函數便是 `Data.List` 模組的 `lookup`，如果要按鍵去尋找相應的值，它就必須得遍歷整個 List，直到找到為止。而 `Data.Map` 模組提供了更高效的方式\(通過樹實現\)，並提供了一組好用的函數。從現在開始，我們扔掉關聯列表，改用map.由於`Data.Map`中的一些函數與Prelude和`Data.List` 模組存在命名衝突，所以我們使用 `qualified import`。`import qualified Data.Map as Map` 在程式碼中加上這句，並 `load` 到 ghci 中.繼續前進，看看 `Data.Map` 是如何的一座寶庫! 如下便是其中函數的一瞥:

**fromList** 取一個關聯列表，返回一個與之等價的 Map。

```haskell
ghci> Map.fromList [("betty","555-2938"),("bonnie","452-2928"),("lucille","205-2928")] 
fromList [("betty","555-2938"),("bonnie","452-2928"),("lucille","205-2928")] 
ghci> Map.fromList [(1,2),(3,4),(3,2),(5,5)] 
fromList [(1,2),(3,2),(5,5)]
```

若其中存在重複的鍵,就將其忽略。如下即 `fromList` 的型別聲明。

```haskell
Map.fromList :: (Ord k) => [(k，v)] -> Map.Map k v
```

這表示它取一組鍵值對的 List，並返回一個將 `k` 映射為 `v` 的 `map`。注意一下，當使用普通的關聯列表時，只需要鍵的可判斷相等性就行了。而在這裡，它還必須得是可排序的。這在 `Data.Map` 模組中是強制的。因為它會按照某順序將其組織在一棵樹中.在處理鍵值對時，只要鍵的型別屬於 `Ord` 型別類，就應該儘量使用`Data.Map`.`empty` 返回一個空 `map`.

```haskell
ghci> Map.empty 
fromList []
```

**insert** 取一個鍵，一個值和一個 `map` 做參數，給這個 `map` 插入新的鍵值對，並返回一個新的 `map`。

```haskell
ghci> Map.empty 
fromList [] 
ghci> Map.insert 3 100 Map.empty
fromList [(3,100)] 
ghci> Map.insert 5 600 (Map.insert 4 200 ( Map.insert 3 100  Map.empty)) 
fromList [(3,100),(4,200),(5,600)]
ghci> Map.insert 5 600 . Map.insert 4 200 . Map.insert 3 100 $ Map.empty 
fromList [(3,100),(4,200),(5,600)]
```

通過 `empty`，`insert` 與 `fold`，我們可以編寫出自己的 `fromList`。

```haskell
fromList' :: (Ord k) => [(k,v)] -> Map.Map k v 
fromList' = foldr (\(k,v) acc -> Map.insert k v acc) Map.empty
```

簡潔明瞭的 `fold`！ 從一個空的 `map` 開始，然後從右摺疊，隨着遍歷不斷地往 `map` 中插入新的鍵值對.

**null** 檢查一個 `map` 是否為空.

```haskell
ghci> Map.null Map.empty 
True 
ghci> Map.null $ Map.fromList [(2,3),(5,5)] 
False
```

**size** 返回一個 `map` 的大小。

```haskell
ghci> Map.size Map.empty 
0 
ghci> Map.size $ Map.fromList [(2,4),(3,3),(4,2),(5,4),(6,4)] 
5
```

**singleton** 取一個鍵值對做參數,並返回一個只含有一個映射的 `map`.

```haskell
ghci> Map.singleton 3 9 
fromList [(3,9)] 
ghci> Map.insert 5 9 $ Map.singleton 3 9 
fromList [(3,9),(5,9)]
```

**lookup** 與 `Data.List` 的 `lookup` 很像,只是它的作用對象是 `map`，如果它找到鍵對應的值。就返回 `Just something`，否則返回 `Nothing`。

**member** 是個判斷函數，它取一個鍵與 `map` 做參數，並返回該鍵是否存在於該 `map`。

```haskell
ghci> Map.member 3 $ Map.fromList [(3,6),(4,3),(6,9)] 
True 
ghci> Map.member 3 $ Map.fromList [(2,5),(4,5)] 
False
```

**map** 與 **filter** 與其對應的 `List` 版本很相似:

```haskell
ghci> Map.map (*100) $ Map.fromList [(1,1),(2,4),(3,9)] 
fromList [(1,100),(2,400),(3,900)] 
ghci> Map.filter isUpper $ Map.fromList [(1,'a'),(2,'A'),(3,'b'),(4,'B')] 
fromList [(2,'A'),(4,'B')]
```

`toList` 是 `fromList` 的反函數。

```haskell
ghci> Map.toList . Map.insert 9 2 $ Map.singleton 4 3 
[(4,3),(9,2)]
```

**keys** 與 **elems** 各自返回一組由鍵或值組成的 List，`keys` 與 `map fst . Map.toList` 等價，`elems` 與 `map snd . Map.toList`等價. `fromListWith` 是個很酷的小函數，它與 `fromList` 很像，只是它不會直接忽略掉重複鍵，而是交給一個函數來處理它們。假設一個姑娘可以有多個號碼，而我們有個像這樣的關聯列表:

```haskell
phoneBook =   
    [("betty","555-2938")  
    ,("betty","342-2492")  
    ,("bonnie","452-2928")  
    ,("patsy","493-2928")  
    ,("patsy","943-2929")  
    ,("patsy","827-9162")  
    ,("lucille","205-2928")  
    ,("wendy","939-8282")  
    ,("penny","853-2492")  
    ,("penny","555-2111")  
    ]
```

如果用 `fromList` 來生成 `map`，我們會丟掉許多號碼! 如下才是正確的做法:

```haskell
phoneBookToMap :: (Ord k) => [(k, String)] -> Map.Map k String  
phoneBookToMap xs = Map.fromListWith (\number1 number2 -> number1 ++ ", " ++ number2) xs
```

```haskell
ghci> Map.lookup "patsy" $ phoneBookToMap phoneBook 
"827-9162, 943-2929, 493-2928" 
ghci> Map.lookup "wendy" $ phoneBookToMap phoneBook
"939-8282" 
ghci> Map.lookup "betty" $ phoneBookToMap phoneBook 
"342-2492，555-2938"
```

一旦出現重複鍵，這個函數會將不同的值組在一起，同樣，也可以預設地將每個值放到一個單元素的 List 中，再用 `++` 將他們都連接在一起。

```haskell
phoneBookToMap :: (Ord k) => [(k，a)] -> Map.Map k [a] 
phoneBookToMap xs = Map.fromListWith (++) $ map (\(k,v) -> (k,[v])) xs 
ghci> Map.lookup "patsy" $ phoneBookToMap phoneBook 
["827-9162","943-2929","493-2928"]
```

很簡潔! 它還有別的玩法，例如在遇到重複元素時，單選最大的那個值.

```haskell
ghci> Map.fromListWith max [(2,3),(2,5),(2,100),(3,29),(3,22),(3,11),(4,22),(4,15)] 
fromList [(2,100),(3,29),(4,22)]
```

或是將相同鍵的值都加在一起.

```haskell
ghci> Map.fromListWith (+) [(2,3),(2,5),(2,100),(3,29),(3,22),(3,11),(4,22),(4,15)] 
fromList [(2,108),(3,62),(4,37)]
```

**insertWith** 之於 `insert`，恰如 `fromListWith` 之於 `fromList`。它會將一個鍵值對插入一個 `map` 之中，而該 `map` 若已經包含這個鍵，就問問這個函數該怎麼辦。

```haskell
ghci> Map.insertWith (+) 3 100 $ Map.fromList [(3,4),(5,103),(6,339)] 
fromList [(3,104),(5,103),(6,339)]
```

`Data.Map` 裡面還有不少函數，\[[http://www.haskell.org/ghc/docs/latest/html/libraries/containers/Data-Map.html](http://www.haskell.org/ghc/docs/latest/html/libraries/containers/Data-Map.html) 這個文檔\]中的列表就很全了.

## Data.Set

![](img/legosets.png)

`Data.Set` 模組提供了對數學中集合的處理。集合既像 List 也像 `Map`: 它裡面的每個元素都是唯一的，且內部的數據由一棵樹來組織\(這和 `Data.Map` 模組的 `map` 很像\)，必須得是可排序的。同樣是插入,刪除,判斷從屬關係之類的操作，使用集合要比 List 快得多。對一個集合而言，最常見的操作莫過于並集，判斷從屬或是將集合轉為 List.

由於 `Data.Set` 模組與 `Prelude` 模組和 `Data.List` 模組中存在大量的命名衝突，所以我們使用 `qualified import`

將 `import` 語句至于程式碼之中:

```haskell
import qualified Data.Set as Set
```

然後在 ghci 中裝載

假定我們有兩個字串，要找出同時存在於兩個字串的字元

```haskell
text1 = "I just had an anime dream. Anime... Reality... Are they so different?"  
text2 = "The old man left his garbage can out and now his trash is all over my lawn!"
```

**fromList** 函數同你想的一樣，它取一個 List 作參數並將其轉為一個集合

```haskell
ghci> let set1 = Set.fromList text1  
ghci> let set2 = Set.fromList text2  
ghci> set1  
fromList " .?AIRadefhijlmnorstuy"  
ghci> set2  
fromList " !Tabcdefghilmnorstuvwy"
```

如你所見，所有的元素都被排了序。而且每個元素都是唯一的。現在我們取它的交集看看它們共同包含的元素:

```haskell
ghci> Set.intersection set1 set2  
fromList " adefhilmnorstuy"
```

使用 `difference` 函數可以得到存在於第一個集合但不在第二個集合的元素

```haskell
ghci> Set.difference set1 set2  
fromList ".?AIRj"  
ghci> Set.difference set2 set1  
fromList "!Tbcgvw"
```

也可以使用 `union` 得到兩個集合的並集

```haskell
ghci> Set.union set1 set2  
fromList " !.?AIRTabcdefghijlmnorstuvwy"
```

`null`，`size`，`member`，`empty`，`singleton`，`insert`，`delete` 這幾個函數就跟你想的差不多啦

```haskell
ghci> Set.null Set.empty  
True  
ghci> Set.null $ Set.fromList [3,4,5,5,4,3]  
False  
ghci> Set.size $ Set.fromList [3,4,5,3,4,5]  
3  
ghci> Set.singleton 9  
fromList [9]  
ghci> Set.insert 4 $ Set.fromList [9,3,8,1]  
fromList [1,3,4,8,9]  
ghci> Set.insert 8 $ Set.fromList [5..10]  
fromList [5,6,7,8,9,10]  
ghci> Set.delete 4 $ Set.fromList [3,4,5,4,3,4,5]  
fromList [3,5]
```

也可以判斷子集與真子集，如果集合 A 中的元素都屬於集合 B，那麼 A 就是 B 的子集, 如果 A 中的元素都屬於 B 且 B 的元素比 A 多，那 A 就是 B 的真子集

```haskell
ghci> Set.fromList [2,3,4] `Set.isSubsetOf` Set.fromList [1,2,3,4,5]  
True  
ghci> Set.fromList [1,2,3,4,5] `Set.isSubsetOf` Set.fromList [1,2,3,4,5]  
True  
ghci> Set.fromList [1,2,3,4,5] `Set.isProperSubsetOf` Set.fromList [1,2,3,4,5]  
False  
ghci> Set.fromList [2,3,4,8] `Set.isSubsetOf` Set.fromList [1,2,3,4,5]  
False
```

對集合也可以執行 `map` 和 `filter`:

```haskell
ghci> Set.filter odd $ Set.fromList [3,4,5,6,7,2,3,4]  
fromList [3,5,7]  
ghci> Set.map (+1) $ Set.fromList [3,4,5,6,7,2,3,4]  
fromList [3,4,5,6,7,8]
```

集合有一常見用途，那就是先 `fromList` 刪掉重複元素後再 `toList` 轉回去。儘管 `Data.List` 模組的 `nub` 函數完全可以完成這一工作，但在對付大 List 時則會明顯的力不從心。使用集合則會快很多，`nub` 函數只需 List 中的元素屬於 `Eq` 型別類就行了，而若要使用集合，它必須得屬於 `Ord` 型別類

```haskell
ghci> let setNub xs = Set.toList $ Set.fromList xs  
ghci> setNub "HEY WHATS CRACKALACKIN"  
" ACEHIKLNRSTWY"  
ghci> nub "HEY WHATS CRACKALACKIN"  
"HEY WATSCRKLIN"
```

在處理較大的 List 時，`setNub` 要比 `nub` 快，但也可以從中看出，`nub` 保留了 List 中元素的原有順序，而 `setNub` 不。

## 建立自己的模組

我們已經見識過了幾個很酷的模組，但怎樣才能構造自己的模組呢? 几乎所有的編程語言都允許你將程式碼分成多個檔案，Haskell 也不例外。在編程時，將功能相近的函數和型別至于同一模組中會是個很好的習慣。這樣一來，你就可以輕鬆地一個 `import` 來重用其中的函數.

接下來我們將構造一個由計算機幾何圖形體積和面積組成的模組，先從新建一個 `Geometry.hs` 的檔案開始.

在模組的開頭定義模組的名稱，如果檔案名叫做 `Geometry.hs` 那它的名字就得是 `Geometry`。在聲明出它含有的函數名之後就可以編寫函數的實現啦，就這樣寫:

```haskell
module Geometry  
( sphereVolume  
，sphereArea  
，cubeVolume  
，cubeArea  
，cuboidArea  
，cuboidVolume  
) where
```

如你所見，我們提供了對球體,立方體和立方體的面積和體積的解法。繼續進發，定義函數體:

```haskell
module Geometry  
( sphereVolume  
，sphereArea  
，cubeVolume  
，cubeArea  
，cuboidArea  
，cuboidVolume  
) where  

sphereVolume :: Float -> Float  
sphereVolume radius = (4.0 / 3.0) * pi * (radius ^ 3)  

sphereArea :: Float -> Float  
sphereArea radius = 4 * pi * (radius ^ 2)  

cubeVolume :: Float -> Float  
cubeVolume side = cuboidVolume side side side  

cubeArea :: Float -> Float  
cubeArea side = cuboidArea side side side  

cuboidVolume :: Float -> Float -> Float -> Float  
cuboidVolume a b c = rectangleArea a b * c  

cuboidArea :: Float -> Float -> Float -> Float  
cuboidArea a b c = rectangleArea a b * 2 + rectangleArea a c * 2 + rectangleArea c b * 2  

rectangleArea :: Float -> Float -> Float  
rectangleArea a b = a * b
```

![](img/making_modules.png)

標準的幾何公式。有幾個地方需要注意一下，由於立方體只是長方體的特殊形式，所以在求它面積和體積的時候我們就將它當作是邊長相等的長方體。在這裡還定義了一個 `helper`函數，`rectangleArea` 它可以通過長方體的兩條邊計算出長方體的面積。它僅僅是簡單的相乘而已，份量不大。但請注意我們可以在這一模組中呼叫這個函數，而它不會被導出! 因為我們這個模組只與三維圖形打交道.

當構造一個模組的時候，我們通常只會導出那些行為相近的函數，而其內部的實現則是隱蔽的。如果有人用到了 `Geometry` 模組，就不需要關心它的內部實現是如何。我們作為編寫者，完全可以隨意修改這些函數甚至將其刪掉，沒有人會注意到裡面的變動，因為我們並不把它們導出.

要使用我們的模組，只需:

```haskell
import Geometry
```

將 `Geometry.hs` 檔案至于用到它的程序檔案的同一目錄之下.

模組也可以按照分層的結構來組織，每個模組都可以含有多個子模組。而子模組還可以有自己的子模組。我們可以把 `Geometry` 分成三個子模組，而一個模組對應各自的圖形對象.

首先，建立一個 `Geometry` 檔案夾，注意首字母要大寫，在裡面新建三個檔案

如下就是各個檔案的內容:

sphere.hs

```haskell
module Geometry.Sphere  
( volume  
，area  
) where  

volume :: Float -> Float  
volume radius = (4.0 / 3.0) * pi * (radius ^ 3)  

area :: Float -> Float  
area radius = 4 * pi * (radius ^ 2)
```

cuboid.hs

```haskell
module Geometry.Cuboid  
( volume  
，area  
) where  

volume :: Float -> Float -> Float -> Float  
volume a b c = rectangleArea a b * c  

area :: Float -> Float -> Float -> Float  
area a b c = rectangleArea a b * 2 + rectangleArea a c * 2 + rectangleArea c b * 2  

rectangleArea :: Float -> Float -> Float  
rectangleArea a b = a * b
```

cube.hs

```haskell
module Geometry.Cube  
( volume  
，area  
) where  

import qualified Geometry.Cuboid as Cuboid  

volume :: Float -> Float  
volume side = Cuboid.volume side side side  

area :: Float -> Float  
area side = Cuboid.area side side side
```

好的! 先是 `Geometry.Sphere`。注意，我們將它置於 `Geometry` 檔案夾之中並將它的名字定為 `Geometry.Sphere`。對 Cuboid 也是同樣，也注意下，在三個模組中我們定義了許多名稱相同的函數，因為所在模組不同，所以不會產生命名衝突。若要在 `Geometry.Cube` 使用 `Geometry.Cuboid` 中的函數，就不能直接 `import Geometry.Cuboid`，而必須得 `qualified import`。因為它們中間的函數名完全相同.

```haskell
import Geometry.Sphere
```

然後，呼叫 `area` 和 `volume`，就可以得到球體的面積和體積，而若要用到兩個或更多此類模組，就必須得 `qualified import` 來避免重名。所以就得這樣寫:

```haskell
import qualified Geometry.Sphere as Sphere  
import qualified Geometry.Cuboid as Cuboid  
import qualified Geometry.Cube as Cube
```

然後就可以呼叫 `Sphere.area`，`Sphere.volume`，`Cuboid.area` 了，而每個函數都只計算其對應物體的面積和體積.

以後你若發現自己的程式碼體積龐大且函數眾多，就應該試着找找目的相近的函數能否裝入各自的模組，也方便日後的重用.

