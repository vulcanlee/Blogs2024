# 使用新執行緒，而不是透過執行緒集區執行緒完成非同步工作

![](../Images/cs2024-9831.png)

當在使用多執行緒程式設計的時候，我們可以透過執行緒集區來執行非同步工作，這樣可以避免建立新的執行緒，而是使用執行緒集區中的執行緒來完成工作。

但是，有時候我們需要建立新的執行緒，而不是使用執行緒集區中的執行緒，這樣可以避免執行緒集區中的執行緒被長時間佔用，而執行緒集區內的執行緒數量不足，而間接需要等候執行緒集區來生成新的可用執行緒的等候時間的問題。在這篇文章中，將會介紹如何使用新執行緒，而不是透過執行緒集區執行緒完成非同步工作。

## 建立測試專案

請依照底下的操作，建立起這篇文章需要用到的練習專案

* 打開 Visual Studio 2022 IDE 應用程式
* 從 [Visual Studio 2022] 對話窗中，點選右下方的 [建立新的專案] 按鈕
* 在 [建立新專案] 對話窗右半部
  * 切換 [所有語言 (L)] 下拉選單控制項為 [C#]
  * 切換 [所有專案類型 (T)] 下拉選單控制項為 [主控台]
* 在中間的專案範本清單中，找到並且點選 [主控台應用程式] 專案範本選項
  > 專案，用於建立可在 Windows、Linux 及 macOS 於 .NET 執行的命令列應用程式
* 點選右下角的 [下一步] 按鈕
* 在 [設定新的專案] 對話窗
* 找到 [專案名稱] 欄位，輸入 `csReadExcelSyncfusion` 作為專案名稱
* 在剛剛輸入的 [專案名稱] 欄位下方，確認沒有勾選 [將解決方案與專案至於相同目錄中] 這個檢查盒控制項
* 點選右下角的 [下一步] 按鈕
* 現在將會看到 [其他資訊] 對話窗
* 在 [架構] 欄位中，請選擇最新的開發框架，這裡選擇的 [架構] 是 : `.NET 8.0 (長期支援)`
* 在這個練習中，需要去勾選 [不要使用最上層陳述式(T)] 這個檢查盒控制項
  > 這裡的這個操作，可以由讀者自行決定是否要勾選這個檢查盒控制項
* 請點選右下角的 [建立] 按鈕

稍微等候一下，這個 背景工作服務 專案將會建立完成

## 修改 Program.cs 類別內容

在這篇文章中，將會把會用到的新類別與程式碼，都寫入到 [Program.cs] 這個檔案中，請依照底下的操作，修改 [Program.cs] 這個檔案的內容

* 在專案中找到並且打開 [Program.cs] 檔案
* 將底下的程式碼取代掉 `Program.cs` 檔案中內容

```csharp
namespace csAlwaysNewThread;

internal class Program
{
    static async Task Main(string[] args)
    {
        Console.WriteLine($"---- 採用非同步的委派方法");
        await Test1();
        Console.WriteLine($"---- 採用同步的委派方法");
        await Test2();
        Console.WriteLine($"---- 採用同步的委派方法與第一層呼叫也是同步、第二層為非同步");
        await Test3();
        Console.WriteLine($"---- 採用同步的委派方法與第一層呼叫也是同步、第二層為同步");
        await Test4();
    }

    static async Task Test1()
    {
        SomeAsyncTask someAsyncTask = new();
        var task = Task.Factory.StartNew(async (x) =>
        {
            await someAsyncTask.Level1Async(1);
        }, CancellationToken.None, TaskCreationOptions.LongRunning);
        Console.WriteLine($"等候 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        await task.Result;
        Console.WriteLine($"完成 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    static async Task Test2()
    {
        SomeAsyncTask someAsyncTask = new();
        var task = Task.Factory.StartNew((x) =>
        {
            someAsyncTask.Level1Async(1).Wait();
        }, CancellationToken.None, TaskCreationOptions.LongRunning);
        Console.WriteLine($"等候 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        await task;
        Console.WriteLine($"完成 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    static async Task Test3()
    {
        SomeAsyncTask someAsyncTask = new();
        var task = Task.Factory.StartNew((x) =>
        {
            someAsyncTask.Level1(1);
        }, CancellationToken.None, TaskCreationOptions.LongRunning);
        Console.WriteLine($"等候 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        await task;
        Console.WriteLine($"完成 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    static async Task Test4()
    {
        SomeAsyncTask someAsyncTask = new();
        var task = Task.Factory.StartNew((x) =>
        {
            someAsyncTask.Level1All(1);
        }, CancellationToken.None, TaskCreationOptions.LongRunning);
        Console.WriteLine($"等候 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        await task;
        Console.WriteLine($"完成 await SomeAsyncTask 非同步工作, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }
}

public class SomeAsyncTask
{
    public async Task Level1Async(int level)
    {
        Console.WriteLine($"{new string(' ', level * 3)}進入 Level1Async 非同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        await Level2Async(level + 1);
        Console.WriteLine($"{new string(' ', level * 3)}離開 Level1Async 非同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    public async Task Level2Async(int level)
    {
        Console.WriteLine($"{new string(' ', level * 3)}進入 Level2Async 非同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        await Task.Delay(3000);
        Console.WriteLine($"{new string(' ', level * 3)}離開 Level2Async 非同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    public void Level1(int level)
    {
        Console.WriteLine($"{new string(' ', level * 3)}進入 Level1 同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        Level2Async(level + 1).Wait();
        Console.WriteLine($"{new string(' ', level * 3)}離開 Level1 同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    public void Level2(int level)
    {
        Console.WriteLine($"{new string(' ', level * 3)}進入 Level2 同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        Task.Delay(3000).Wait();
        Console.WriteLine($"{new string(' ', level * 3)}離開 Level2 同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

    public void Level1All(int level)
    {
        Console.WriteLine($"{new string(' ', level * 3)}進入 Level1All 同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
        Level2(level + 1);
        Console.WriteLine($"{new string(' ', level * 3)}離開 Level1All 同步方法, " +
            $"TId:{Thread.CurrentThread.ManagedThreadId} " +
            $"(from ThreadPool {Thread.CurrentThread.IsThreadPoolThread})");
    }

}
```

在這個範例程式碼中，定義的一個 [SomeAsyncTask] 類別，裡面有 [Level1Async] 和 [Level2Async] 兩個非同步方法，以及 [Level1] 和 [Level2] 兩個同步方法，以及 [Level1All] 這個同步方法，內部呼叫 [Level2] 這個同步方法。透過這個類別內的這些方法，說明在實際進行多執行緒與非同步程式設計中，如何針對這些同步與非同步的方法來進行呼叫。

對於 [Level1Async] 方法，這是一個使用 [async] 修飾詞的非同步方法，首先會顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區，然後使用 [await] 呼叫 [Level2Async] 方法，等待 [Level2Async] 方法完成後，再顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區。

在 [Level2Async] 方法，這也是一個使用［async] 修飾詞的非同步方法，同樣的，首先會顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區，然後使用 [await] 關鍵字來等候 [Task.Delay] 方法來進行非同步操作，等待 3 秒後，再顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區。

[Level1] 方法，這是一個同步方法，首先會顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區，然後使用 [Wait] 方法來等候 [Level2Async] 方法，因為使用了 [Wait] 方法，這表示這裡使用了同步方式的呼叫，完成後，再顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區。

[Level2] 方法，這是一個同步方法，首先會顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區，然後使用 [Wait] 方法來等候 [Task.Delay] 方法，等待 3 秒後，再顯示出當前的執行緒 Id 與 該執行緒是否來自於執行緒集區。同樣的，這裡都是使用同步方式的呼叫。

最後則是 [Level1All] 這個同步方法，這個方法內部呼叫了 [Level2] 這個同步方法，這裡展示了在同步方法內部呼叫同步方法的情況。

在這個程式進入點的 [Main] 方法內，主要是展示了如何在 C# 中使用非同步和同步方法來執行任務。程式包含四個測試方法 (Test1, Test2, Test3, Test4)，每個方法都展示了不同的非同步和同步方法的使用方式。以下是每個方法的簡要描述：
1.	Test1:
•	使用 Task.Factory.StartNew 來啟動一個非同步委派方法 Level1Async。
•	等待這個非同步任務完成後，輸出相關訊息。
2.	Test2:
•	使用 Task.Factory.StartNew 來啟動一個同步委派方法，該方法內部呼叫 Level1Async 並等待其完成。
•	等待這個同步任務完成後，輸出相關訊息。
3.	Test3:
•	使用 Task.Factory.StartNew 來啟動一個同步委派方法 Level1，該方法內部呼叫 Level2Async 並等待其完成。
•	等待這個同步任務完成後，輸出相關訊息。
4.	Test4:
•	使用 Task.Factory.StartNew 來啟動一個同步委派方法 Level1All，該方法內部呼叫 Level2。
•	等待這個同步任務完成後，輸出相關訊息。
SomeAsyncTask 類別包含了以下方法：
•	Level1Async 和 Level2Async：這些是非同步方法，分別模擬了不同層級的非同步操作。
•	Level1 和 Level2：這些是同步方法，分別模擬了不同層級的同步操作。
•	Level1All：這是一個同步方法，內部呼叫另一個同步方法 Level2。
程式的主要目的是展示如何在不同的情境下使用非同步和同步方法，以及如何在非同步和同步方法之間進行切換。

因為這裡使用了 Task.Factory.StartNew 來取得執行緒，所以，每個執行緒將會是新的執行緒，而不是使用執行緒集區中的執行緒。

## 執行程式

* 按下 `F5` 鍵，開始執行這個程式
* 程式將會開始執行，並且在主控台視窗內，將會看到類似下圖的輸出結果

從底下的執行結果可以看出

[Test1] 方法使用非同步的委派方法，呼叫 [Level1Async] 方法，這裡是使用 await 來非同步等候該方法結束執行，在 [Level1Async] 方法內，也使用了 await 關鍵字等候 [Level2Async] 方法完成後，當進入到 [Level1Async] 方法後，看到執行緒將會從 Id : 1 變成 Id : 11，因為，Id=11 的執行緒，是透過 Task.Factory.StartNew 來建立的新執行緒，而不是使用執行緒集區中的執行緒。當 [Level2Async] 方法完成後，執行緒 Id 會變回 7，這代表此 Id=7 的執行緒，是透過執行緒集區取得的，這可以從 (from ThreadPool True) 這個訊息看的出來，而由於在 [Test1] 方法內，也有一個 `await task.Result;` 非同步呼叫，因此，可以看到這個敘述執行完成後，其執行緒 Id 變成 7，而且該執行緒是從執行緒集區取得的。

```plaintext
---- 採用非同步的委派方法
等候 await SomeAsyncTask 非同步工作, TId:1 (from ThreadPool False)
   進入 Level1Async 非同步方法, TId:11 (from ThreadPool False)
      進入 Level2Async 非同步方法, TId:11 (from ThreadPool False)
      離開 Level2Async 非同步方法, TId:7 (from ThreadPool True)
   離開 Level1Async 非同步方法, TId:7 (from ThreadPool True)
完成 await SomeAsyncTask 非同步工作, TId:7 (from ThreadPool True)
```

[Test2] 方法使用非同步的委派方法，當前的執行緒 Id 為 7，呼叫 [Level1Async] 方法，這裡使用的 `.Wait()` 方法，採取的是同步呼叫方式，而在 [Level1Async] 方法內，同樣的使用 awiat 等候 [Level2Async] 方法完成後，當進入到 [Level1Async] 方法後，看到執行緒 Id 會從 7 變成 13，因為，Id=13 的執行緒，是透過 Task.Factory.StartNew 來建立的新執行緒，而不是使用執行緒集區中的執行緒。當 [Level2Async] 方法完成後，執行緒 Id 會變回 9，這代表此 Id=9 的執行緒，是透過執行緒集區取得的，這可以從 (from ThreadPool True) 這個訊息看的出來，而由於在 [Test2] 方法內，也有一個 `await task;` 非同步呼叫，因此，可以看到這個敘述執行完成後，其執行緒 Id 變成 13，而且該執行緒不是從執行緒集區取得的。

```plaintext
---- 採用同步的委派方法
等候 await SomeAsyncTask 非同步工作, TId:7 (from ThreadPool True)
   進入 Level1Async 非同步方法, TId:13 (from ThreadPool False)
      進入 Level2Async 非同步方法, TId:13 (from ThreadPool False)
      離開 Level2Async 非同步方法, TId:9 (from ThreadPool True)
   離開 Level1Async 非同步方法, TId:9 (from ThreadPool True)
完成 await SomeAsyncTask 非同步工作, TId:13 (from ThreadPool False)
```

將過上述的程式碼，在 [Main] 方法內使用的執行緒會是執行緒 Id=13，對於 [Test3()] 方法，這裡是使用同步的委派方法，呼叫 [Level1] 方法，這裡是使用同步的呼叫方式，而在 [Level1] 方法內，同樣的使用 awiat 等候 [Level2Async] 方法完成後，當進入到 [Level1] 方法後，看到執行緒 Id 會從 13 變成 14，因為，Id=14 的執行緒，是透過 Task.Factory.StartNew 來建立的新執行緒，而不是使用執行緒集區中的執行緒。當 [Level2Async] 方法完成後，執行緒 Id 會變回 10，這代表此 Id=10 的執行緒，是透過執行緒集區取得的，這可以從 (from ThreadPool True) 這個訊息看的出來，而由於在 [Test3] 方法內，也有一個 `await task;` 非同步呼叫，因此，可以看到這個敘述執行完成後，其執行緒 Id 變成 14，而且該執行緒不是是從執行緒集區取得的。

```plaintext
---- 採用同步的委派方法與第一層呼叫也是同步、第二層為非同步
等候 await SomeAsyncTask 非同步工作, TId:13 (from ThreadPool False)
   進入 Level1 同步方法, TId:14 (from ThreadPool False)
      進入 Level2Async 非同步方法, TId:14 (from ThreadPool False)
      離開 Level2Async 非同步方法, TId:10 (from ThreadPool True)
   離開 Level1 同步方法, TId:14 (from ThreadPool False)
完成 await SomeAsyncTask 非同步工作, TId:14 (from ThreadPool False)
```

最後則是 [Test4] 方法，這裡是使用同步的委派方法，呼叫 [Level1All] 方法，這裡是使用同步的呼叫方式，而在 [Level1All] 方法內，同樣的使用 awiat 等候 [Level2] 方法完成後，當進入到 [Level1All] 方法後，看到執行緒 Id 會從 14 變成 15，因為，Id=15 的執行緒，是透過 Task.Factory.StartNew 來建立的新執行緒，而不是使用執行緒集區中的執行緒。當 [Level2] 方法完成後，執行緒 Id 維持在 15，這因為這些都是採用同步方式來呼叫，所以，使用的都是同一個執行緒，而由於在 [Test4] 方法內，也有一個 `await task;` 非同步呼叫，因此，可以看到這個敘述執行完成後，其執行緒 Id 變成 15，而且該執行緒不是是從執行緒集區取得的(會有這樣情況，是因為這裡 `await task` 所等待的工作已經完成了，因為，沒有進入到非同步執行模式，直接採用同步方式來繼續執行)。

```plaintext
---- 採用同步的委派方法與第一層呼叫也是同步、第二層為同步
等候 await SomeAsyncTask 非同步工作, TId:14 (from ThreadPool False)
   進入 Level1All 同步方法, TId:15 (from ThreadPool False)
      進入 Level2 同步方法, TId:15 (from ThreadPool False)
      離開 Level2 同步方法, TId:15 (from ThreadPool False)
   離開 Level1All 同步方法, TId:15 (from ThreadPool False)
完成 await SomeAsyncTask 非同步工作, TId:15 (from ThreadPool False)
```