---
title: Quick Sort
publishDate: "2025-02-01T00:00:00Z"
updateDate: "2025-02-01T00:00:00Z"
---

# Quick Sort 快速排序
> 平均時間複雜度為: O(n log n)

快速排序法(Quick Sort)又稱分割交換排序法，是目前公認效率極佳的演算法，使用了分治法(Divide and Conquer)的概念。

先從取出一個基準值(Pivot)，接著逐一將資料與基準值比較，小於 Pivot 的資料放在左邊，大於 Pivot 的資料放在右邊，再將兩邊區塊分別再找出 Pivot ，重複前面的步驟，直到排序完為止。

- 選擇基準值（Pivot）
    - 常見選擇方式：第一個元素、最後一個元素、隨機選擇、中位數
- 分區（Partitioning）
    - 小於 Pivot 的數字放左邊
    - 大於 Pivot 的數字放右邊
    - 等於 Pivot 的數字保持在中間

## 適用場景
- 大數據集（大型陣列），因為 時間複雜度平均是 O(n log n)，效率較高。
- 需要原地排序（In-Place Sorting），因為 Quick Sort 不需要額外的陣列儲存（不像 Merge Sort）。
- 一般應用程式的排序需求，例如陣列排序、資料庫排序、數據分析等。
- 平均情況下，效能比 Merge Sort 更快，因為 Quick Sort 通常使用較少的記憶體。
## 執行步驟（遞迴版本）
遞迴版本雖然容易理解，但會影響到空間複雜度！
因為每次都需要申請兩個子數列`(左右兩側陣列)`的記憶體空間！
**遞迴的深度越多，需要記憶體空間就越大**！

![image](https://hackmd.io/_uploads/S1cvfMsdyg.png)
1. `Pivot`：資料列隨機挑選，通常會選**第一筆**
2. 分類：
    - 小於 `Pivot` 的資料放在左邊
    - 大於 `Pivot` 的資料放在右邊
3. 左右兩邊資料分別重複1~2步驟，直到剩下1筆資料
4. 最後合併


## 範例
```javascript=
function quickSort(array) {
  if (array.length <= 1) return array;
  let [pivot, ...restArray] = array;
  const leftArray = [];
  const rightArray = [];
  restArray.forEach((item) => {
    if (item < pivot) {
      leftArray.push(item);
    } else {
      rightArray.push(item);
    }
  });
  return quickSort(leftArray).concat(pivot, quickSort(rightArray));
}
console.log(quickSort([8, 9, 2, 5, 1])); // [ 1, 2, 5, 8, 9 ]
```
- 當陣列長度 ≤ 1，表示該陣列已經是排序好的（單個元素或空陣列不需要排序），直接回傳該陣列。
- 選擇基準值（Pivot）：
    - `pivot`：取出陣列的第一個元素作為基準值
    - `restArray`：剩下的所有元素
- 巡迴 `restArray`，將每個元素去比對 `Pivot` 
    - 比 `Pivor` 小的放左邊
    - 比 `Pivor` 大的放右邊
- 遞迴排序：
    - 對 『左邊陣列』 進行 Quick Sort，確保左邊的數據排序完畢。
    - 對 『右邊陣列』 進行 Quick Sort，確保右邊的數據排序完畢。
    - 合併結果：`[排序後的左半邊, pivot, 排序後的右半邊]`


## 執行步驟（原地交換版本(In-Place)）
原始資料列使用一個`指標(T)`與`索引(i)`，當`索引(i)`的資料小於 `Pivot` 時，索引的資料與指標位置資料交換。

遞迴版本會需要額外的記憶體空間，但 In-Place 版本不需要額外的子數列記憶體空間，因為只會更改原本的數列，切割的同時也就等同合併了，所以只需花費常數O(1)的空間複雜度。

實作時會需要用到 Partition 輔助函式，來直接分割原本的數列。
### 每回合簡略圖
![image](https://hackmd.io/_uploads/r1fANIsOyx.png)

### 單一回合的細節
![image](https://hackmd.io/_uploads/HklalPjOkl.png)
初始：
- `Pivot`：資料列的**最後一筆**
- `指標(T)`：資料列第一筆，負責用來記錄小於 `Pivot` 的資料位置

開始步驟：
1. `索引(i)` 逐一與 `Pivot` 比較
2. `索引(i)` 小於 `Pivot`
    - 將 `索引(i)` 與 `指標(T)` 位置的資料做交換
    - `指標(T)` 往下一個位置移動
3. `索引(i)` 大於等於 `Pivot`
    - 跳過不做任何動作
4. 所有資料比較過後
    - 將 `Pivot` 與 `指標(T)` 位置的資料交換


## 範例
```javascript
function quickSort(array) {
  if (array.length <= 1) return array;
  const stack = [];

  stack.push(0);
  stack.push(array.length - 1);

  while (stack.length) {
    const endIndex = stack.pop();
    const startIndex = stack.pop();
    const pivotIndex = partition(array, startIndex, endIndex);

    if (startIndex < pivotIndex - 1) {
      stack.push(startIndex);
      stack.push(pivotIndex - 1);
    }
    if (endIndex > pivotIndex) {
      stack.push(pivotIndex);
      stack.push(endIndex);
    }
  }
    
  return array;
}

function partition(array, startIndex, endIndex) {
  const pivot = array[endIndex];
  let target = startIndex;

  for (let i = startIndex; i < endIndex; i++) {
    if (array[i] < pivot) {
      swap(array, target, i);
      target++;
    }
  }
  swap(array, target, endIndex);
  return target;
}

function swap(array, firstIndex, secondIndex) {
  let tempValue = array[firstIndex];
  array[firstIndex] = array[secondIndex];
  array[secondIndex] = tempValue;
}

console.log(quickSort([8, 9, 2, 5, 1, 7])); // [ 1, 2, 5, 7, 8, 9 ]
```
## 拆解範例
### quickSort 快速排列
```javascript=
function quickSort(array) {
  if (array.length <= 1) return array;
  const stack = [];

  // 將陣列的起始索引和結束索引推入 stack
  stack.push(0);
  stack.push(array.length - 1);

  while (stack.length) {
    const endIndex = stack.pop();
    const startIndex = stack.pop();
    const pivotIndex = partition(array, startIndex, endIndex);

    // 將左側的部分推入 stack
    if (startIndex < pivotIndex - 1) {
      stack.push(startIndex);
      stack.push(pivotIndex - 1);
    }
    // 將右側的部分推入 stack
    if (endIndex > pivotIndex) {
      stack.push(pivotIndex);
      stack.push(endIndex);
    }
  }
  return array;
}
```
- 當陣列長度 ≤ 1，表示該陣列已經是排序好的（單個元素或空陣列不需要排序），直接回傳該陣列。
- `stack`：負責暫存尚未排列的資料
    - 初始：儲存陣列的**起始索引**和**結束索引**
        - 起始索引：負責當 `指標(T)`，也就是區間的起點
        - 結束索引：負責當 `Pivot`，也就是區間的終點
- `while` 迴圈：處理 `stack` 的過程，直到 `stack` 被清空為止
    - 迴圈條件：當 `stack` 還有未處理的區間
    - 取出待處理的區間：
        - 右邊：`endIndex = stack.pop();`
        - 左邊：`startIndex = stack.pop();`
        - `Pivot` 真正的位置：根據分割陣列`(partition)`取得最終的 `Pivot` 位置
   - 將尚未處理的資料放入 `stack` 中
       - 左邊：
           - 如果 `(startIndex < pivotIndex - 1)`，還有未處理的，放入 `stack` 中
           - 目的：後續再處理 `指標(T)`(`startIndex`) 到 `Pivot`(`pivotIndex - 1`) 這個範圍
       - 右邊：
           - 如果 `(endIndex > pivotIndex)`，還有未處理的，放入 `stack` 中
           - 目的：後續再處理 `指標(T)`(`endIndex`) 到 `Pivot`(`pivotIndex`) 這個範圍

### partition 分割陣列，並且回傳 Pivot 位置
```javascript=
function partition(array, startIndex, endIndex) {
  const pivot = array[endIndex];
  let target = startIndex; // target 負責紀錄下一個小於 `Pivot` 的元素要交換到的位置

  for (let i = startIndex; i < endIndex; i++) {
    if (array[i] < pivot) { // 只交換小於 pivot 的值
      swap(array, target, i);
      target++; // 交換後 target 右移，準備下一個交換
    }
  }
  // 最後，把 pivot 交換到正確位置（target 位置）
  swap(array, target, endIndex);
  return target;
}
```
1. `Pivot`：陣列中的最後一個元素
2. `target` 為 `指標(T)`：紀錄下一個小於 `Pivot` 的元素要交換到的位置
    - 初始：為區域範圍的起始位置
    - 過程：在 `for` 迴圈內，當遇到比 `Pivot` 小的資料時，將資料交換到 `指標(T)` 的位置，然後再將 `指標(T)`加一 `(目的是往右移，準備下一次交換)`。
3. `for` 迴圈：負責分類，檢查每一個元素是否有小於 `Pivot`
    - 沒有：不動
    - 有：
        - 將元素移動至左邊
        - `指標(T)` 往右移一位，目的是準備下一次的交換
4. 將真正的 `Pivot` 移動到正確的位置：
    - 取得真正的 `Pivot` 位置，也就是 `指標(T)` 的位置
    - 原本 `Pivot` 會是在最後一個，將他與真正 `Pivot` 位置的資料互相交換位置
5. 回傳真正的 `Pivot` 位置

### swap 負責交換位置
```javascript=
function swap(array, firstIndex, secondIndex) {
  // 1. 先暫存第一個位置，防止後面被替換時消失
  let temp = array[firstIndex];
  // 2. 第一個位置被第二個位置取代
  array[firstIndex] = array[secondIndex];
  // 3. 第二個位置被暫存的第一個位置取代
  array[secondIndex] = temp;
}
```
1. 暫存第一個位置，防止後面替換資料時消失。如：`temp = 2`
2. 將第二個位置的資料賦予給第一個位置。如：`[2, 1] → [1, 1]`
3. 將剛剛暫存的第一個位置的資料賦予給第二個位置。如：`[1, temp] → [1, 2]`

> [【Day26】[演算法]-快速排序法Quick Sort](https://ithelp.ithome.com.tw/articles/10278644)