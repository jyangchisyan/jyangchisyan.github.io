+++
date = '2026-11-13T15:30:00+08:00'
draft = false
title = '從基礎語法到完整專題：我的 C++ 56 課離線練習'
tags = ['C++', '程式設計', '教學', 'GitHub']
description = '介紹 cpp_lesson 的 56 課 C++ 離線學習路線，從基礎語法、STL 演算法與 lambda，一路練習到多檔案、例外處理及學生資料分析專題。'
keywords = ['C++ 教學', 'C++ 練習', 'STL', 'lambda', 'GitHub', '程式設計入門']
+++

學習 C++ 最容易遇到的問題，不是找不到語法說明，而是看完語法後，不知道下一步該練什麼。為了讓複習過程更有方向，我整理了一套可以完全離線使用的練習專案：[cpp_lesson](https://github.com/jyangchisyan/cpp_lesson)。

這套內容目前共有 56 課，依概念拆成 12 個資料夾。每一課包含一份約五分鐘能讀完的重點講義，以及一個範圍明確的小練習；最後再把前面學到的觀念組合成學生資料分析專題。

它不是要取代完整的 C++ 書籍，而是提供一條可以反覆實作的學習路線：讀一個觀念、自己重寫一次、編譯、執行，再對照範例確認差異。

## 課程涵蓋哪些內容

整體學習路線從最基本的輸入輸出開始，逐步增加程式的資料結構與控制能力：

1. 基本輸入輸出、變數與字串
2. 條件判斷與迴圈
3. 函式、參照與指標
4. 陣列與基本容器
5. `map` 與次數統計
6. 檔案讀寫
7. `struct`、`class` 與物件
8. 標準演算法、lambda 與迭代器
9. 函式模板
10. 多檔案程式與類別分檔
11. 例外與錯誤處理
12. 學生資料分析小專題

這樣的編排有一個重要原則：後面的練習會重用前面已經出現過的概念。學到 `vector` 後，不只做一次容器練習，而是繼續在排序、搜尋、物件集合與專題中使用它。語法因此不會停留在孤立的範例裡。

## 每一課怎麼使用

每個主題資料夾都有自己的 `README.md`，裡面列出課次、學習重點、練習程式和編譯方式。每一課另外有一份短講義，將內容拆成「五分鐘重點」和「一道練習」。

建議的使用方式是：

1. 先閱讀該課的短講義。
2. 關掉既有的 `.cpp` 範例，自己從空白檔案重寫。
3. 編譯並用不同輸入測試。
4. 遇到錯誤時先看編譯器訊息，再回頭對照範例。
5. 過幾天重新做一次，確認不是只記住答案。

先取得專案：

```bash
git clone https://github.com/jyangchisyan/cpp_lesson.git
cd cpp_lesson
```

一般練習不依賴額外套件，只要系統裡有 `g++` 就能編譯。例如執行「統計數字出現次數」：

```bash
cd 08-algorithms-lambdas
g++ count_occurrences.cpp -o count_occurrences
./count_occurrences
```

因為每個程式都刻意維持小而明確，初學時可以把注意力放在當課概念，不必先理解大型建置系統。

## 從迴圈進到 STL 演算法

前半段課程先用條件判斷、迴圈和函式建立基礎。到了第 26 至 38 課，則開始把同一類問題改用標準演算法處理，包括：

- `std::sort` 自訂排序
- `std::count_if` 條件計數
- `std::find` 與 `std::find_if` 搜尋元素
- `std::replace` 替換資料
- erase-remove idiom 刪除元素
- `std::transform` 轉換資料
- `std::accumulate` 累加結果
- `std::binary_search`、`lower_bound` 與 `upper_bound`

這一段不只是記函式名稱，也會練習 iterator 範圍、`end()` 的意義、lambda capture，以及容器修改後 iterator 失效等常見問題。

例如，依條件計算矩形數量時，可以把門檻值捕捉進 lambda：

```cpp
auto count = std::count_if(rectangles.begin(), rectangles.end(),
    [level](const Rectangle& rectangle) {
        return rectangle.area() >= level;
    });
```

和手寫迴圈相比，這段程式直接表達「計算符合條件的元素數量」。先理解迴圈，再學會用演算法描述意圖，是這組課程希望建立的能力。

## 從單一檔案走向實際程式結構

初學範例通常只有一個 `.cpp`，但真正的程式很快就需要拆分。第 40、41 課因此安排多檔案練習：先把一般函式拆成 header 與 implementation，再把 `Rectangle` 類別分成 `rectangle.h`、`rectangle.cpp` 和 `main.cpp`。

編譯時必須同時交給編譯器兩個 implementation 檔案：

```bash
cd 10-multi-file/rectangle
g++ main.cpp rectangle.cpp -o rectangle
./rectangle
```

這裡會實際碰到宣告與定義的差異、header 的用途，以及 `Rectangle::` 如何在類別外定義成員函式。這是從「會寫語法」走向「能組織程式」的重要一步。

## 錯誤處理也是正常流程的一部分

課程後段加入輸入驗證、檔案開啟檢查與例外處理。例如存取 `vector` 時，`at()` 會在索引越界時拋出 `std::out_of_range`，程式可以用 `try` / `catch` 給出清楚的結果：

```cpp
try {
    std::cout << numbers.at(index) << '\n';
} catch (const std::out_of_range& error) {
    std::cout << "索引超出範圍\n";
}
```

這些練習刻意讓失敗情境成為題目的一部分。程式不只要在正確輸入時得到答案，也需要知道資料錯誤、索引無效或檔案無法寫入時應該怎麼結束。

## 最後成果：學生資料分析專題

第 44 至 56 課會持續改進同一份 `student_average.cpp`。它從收集姓名與分數開始，逐步加入：

- 使用 `struct Student` 組合學生資料
- 計算平均分數與中位數
- 依分數排名，同分時再按名字排序
- 找出最高分學生
- 統計及格人數與及格率
- 將結果同時輸出到終端與 `report.txt`
- 檢查輸入和檔案是否有效
- 抽出計算與排序函式
- 統一小數格式

它的價值不在於功能複雜，而在於能看見一個程式如何逐步長大。每一課只增加一項要求，同時回頭整理重複程式碼與邊界條件。最後的版本已經把容器、物件、演算法、lambda、函式拆分、檔案輸出和錯誤處理串在一起。

可以這樣執行：

```bash
cd 12-student-project
g++ student_average.cpp -o student_average
./student_average
```

例如輸入三位學生：

```text
3
Bob 90
Amy 90
Cathy 80
```

程式會計算平均與中位數、顯示及格統計，並依「分數降序、同分名字升序」排列學生。相同報告也會寫入目前資料夾的 `report.txt`。

## 這套練習適合誰

如果你已經看過 C++ 基礎語法，但還不熟悉怎麼把觀念放進完整程式，這套練習可以當成一份複習地圖。它也適合：

- 想用短時間、固定範圍逐步練習的人
- 學過其他語言，準備轉入 C++ 的開發者
- 想加強 STL 演算法與 lambda 的初學者
- 希望從單檔程式進到多檔案結構的人
- 需要一個小專題整合基礎觀念的人

所有教材和範例都整理在 [jyangchisyan/cpp_lesson](https://github.com/jyangchisyan/cpp_lesson)。與其一次讀完 56 課，更推薦每天選一到兩課，自己重寫、測試，再記下不熟悉的部分。C++ 的內容很多，但只要讓每個觀念都經過一次實際編譯與執行，學習路線就會清楚許多。
