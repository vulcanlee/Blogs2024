# 將 Inner, Aggregation 等所有例外異常物件，轉成字串

![](../Images/cs2024-9874.png)

當在進行 .NET 應用專案開發的時候，面對執行時期的例外異常發生的時候，我們通常會透過 `try-catch` 區塊來捕捉這些例外異常，並且透過 `ex.ToString()` 方法，將例外異常物件轉換成字串，以便於在程式碼中進行記錄或是顯示。有了這些資訊，將會有助於未來將該應用程式進行問題修正與效能改善之依據，面對該程式在 Production 環境下運行的時候，這些資訊將會由其為重要。

在這篇文章中，將會介紹如何將例外異常物件轉換成完整的字串訊息，這裡將會透過擴充方法 (Extension Method) 的方式，來將例外異常物件轉換成完整的字串訊息，這個擴充方法將會將例外異常物件的所有 Inner, Aggregation 例外異常，轉換成完整的字串訊息。

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
* 找到 [專案名稱] 欄位，輸入 `csExceptionToString` 作為專案名稱
* 在剛剛輸入的 [專案名稱] 欄位下方，確認沒有勾選 [將解決方案與專案至於相同目錄中] 這個檢查盒控制項
* 點選右下角的 [下一步] 按鈕
* 現在將會看到 [其他資訊] 對話窗
* 在 [架構] 欄位中，請選擇最新的開發框架，這裡選擇的 [架構] 是 : `.NET 8.0 (長期支援)`
* 在這個練習中，需要去勾選 [不要使用最上層陳述式(T)] 這個檢查盒控制項
  > 這裡的這個操作，可以由讀者自行決定是否要勾選這個檢查盒控制項
* 請點選右下角的 [建立] 按鈕

稍微等候一下，這個 背景工作服務 專案將會建立完成

## 修改 Program.cs 類別內容

* 在專案中找到並且打開 [Program.cs] 檔案
* 將底下的程式碼取代掉 `Program.cs` 檔案中內容

```csharp
using System.Text;

namespace csExceptionToString;

internal class Program
{
    static void Main(string[] args)
    {
        try
        {
                throw new InvalidOperationException("這是一個例外異常");
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
            Console.WriteLine();
            Console.WriteLine(new string('*', 40));
            Console.WriteLine();
            string fullExceptionMessage = ex.GetFullExceptionMessage();
            Console.WriteLine(fullExceptionMessage);
        }

        Console.WriteLine();
        Console.WriteLine(new string('-',40));
        Console.WriteLine();

        try
        {
            // 模擬內部例外異常
            try
            {
                throw new InvalidOperationException("這是內部例外異常");
            }
            catch (Exception innerEx)
            {
                throw new Exception("這是外部例外異常", innerEx);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
            Console.WriteLine();
            Console.WriteLine(new string('*', 40));
            Console.WriteLine();
            string fullExceptionMessage = ex.GetFullExceptionMessage();
            Console.WriteLine(fullExceptionMessage);
        }

        Console.WriteLine();
        Console.WriteLine(new string('-', 40));
        Console.WriteLine();

        try
        {
            // 模擬例外異常
            throw new AggregateException("外層例外",
                new InvalidOperationException("內層例外1"),
                new Exception("內層例外2", new ArgumentException("更深層例外")),
                new AggregateException("外層例外",
                new InvalidOperationException("內層例外1"),
                new Exception("內層例外2", new ArgumentException("更深層例外"))
            )
            );
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
            Console.WriteLine();
            Console.WriteLine(new string('*', 40));
            Console.WriteLine();
            string fullExceptionMessage = ex.GetFullExceptionMessage();
            Console.WriteLine(fullExceptionMessage);
        }
    }
}
public static class ExceptionExtensions
{
    public static string GetFullExceptionMessage(this Exception ex)
    {
        if (ex == null) throw new ArgumentNullException(nameof(ex));

        var sb = new StringBuilder();
        GetExceptionDetails(sb, ex);
        return sb.ToString();
    }

    private static void LogExceptionDetails(StringBuilder sb, Exception ex,
        int level = 0, string leadSpace = "")
    {
        if (ex is AggregateException aggregateException)
        {
            sb.AppendLine($"{leadSpace}[Aggregation Exceptions]");
        }
        sb.AppendLine($"{leadSpace}Exception Level {level}:");
        sb.AppendLine($"{leadSpace}Message: {ex.Message}");
        sb.AppendLine($"{leadSpace}Type: {ex.GetType()}");
        sb.AppendLine($"{leadSpace}Stack Trace: {ex.StackTrace}");
    }
    private static void GetExceptionDetails(StringBuilder sb, Exception ex, int level = 0)
    {
        if (ex == null) return;

        var levelSpace = "".PadRight(level * 3);

        LogExceptionDetails(sb, ex, level, levelSpace);

        if (ex is AggregateException aggregateException)
        {
            sb.AppendLine($"{levelSpace}Inner Exceptions:");
            foreach (var innerException in aggregateException.InnerExceptions)
            {
                sb.AppendLine();
                GetExceptionDetails(sb, innerException, level + 1);
            }
        }
        else if (ex.InnerException != null)
        {
            sb.AppendLine($"{levelSpace}Inner Exception:");
            sb.AppendLine();
            GetExceptionDetails(sb, ex.InnerException, level + 1);
        }
    }
}
```

這個程式碼主要是在 `Main` 方法內，透過 `try-catch` 區塊來模擬例外異常的發生，並且透過 `ex.ToString()` 方法，將例外異常物件轉換成字串，並且透過 `ex.GetFullExceptionMessage()` 方法，將例外異常物件轉換成完整的字串訊息。

這裡將會使用底下程式碼來模擬一般的例外異常

```csharp
throw new InvalidOperationException("這是一個例外異常");
```

當觸發了這個例外異常之後，將會在 [catch] 敘述內，使用 `ex.ToString()` 方法，將例外異常物件轉換成字串，底下將會是所顯示的例外異常文字敘述。

```plaintext
System.InvalidOperationException: 這是一個例外異常
   at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 11
```

接著透過例外異常擴充方法 [GetFullExceptionMessage()] 方法，將例外異常物件轉換成完整的字串訊息，底下將會是所顯示的例外異常文字敘述。

```plaintext
Exception Level 0:
Message: 這是一個例外異常
Type: System.InvalidOperationException
Stack Trace:    at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 11
```

這個延伸方法的函式簽章如下

```csharp
public static string GetFullExceptionMessage(this Exception ex)
```

這個延伸方法的主要目的是將例外異常物件轉換成完整的字串訊息，這個方法內部會呼叫 `GetExceptionDetails()` 方法，這個方法主要是用來取得例外異常物件的詳細資訊，這個方法內部會呼叫 `LogExceptionDetails()` 方法，這個方法主要是用來記錄例外異常物件的詳細資訊。

在這個 [GetExceptionDetails] 方法內，會先呼叫 `LogExceptionDetails()` 方法，該方法將會先檢查該例外異常是否為聚合例外異常，若是聚合例外異常，將會顯示出這是一個聚合例外異常的文字訊息，接著將該例外異常的 [Message] / [GetType()] / [StackTrack] 內容也顯示出來。

接著在 [GetExceptionDetails] 方法內，會檢查該例外異常是否為聚合例外異常，若是聚合例外異常，將會進入迴圈，並且將聚合例外異常的內部例外異常逐一取出，並且透過遞迴的方式，呼叫 `GetExceptionDetails()` 方法，來取得內部例外異常的詳細資訊。

若該例外異常不是聚合例外異常，則會檢查該例外異常是否有內部例外異常，若有內部例外異常，則會透過遞迴的方式，呼叫 `GetExceptionDetails()` 方法，來取得內部例外異常的詳細資訊。

這裡將會使用底下程式碼來模擬內部例外異常

```csharp
    try
    {
        throw new InvalidOperationException("這是內部例外異常");
    }
    catch (Exception innerEx)
    {
        throw new Exception("這是外部例外異常", innerEx);
    }
```

底下將會是使用 Exception.ToString() 所顯示的例外異常文字敘述。

```plaintext
System.Exception: 這是外部例外異常
 ---> System.InvalidOperationException: 這是內部例外異常
   at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 32
   --- End of inner exception stack trace ---
   at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 36
```

底下將會是使用 [GetFullExceptionMessage] 延伸方法，取得所有 Inner, Aggregation 例外異常所顯示的例外異常文字敘述。

```plaintext
Exception Level 0:
Message: 這是外部例外異常
Type: System.Exception
Stack Trace:    at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 36
Inner Exception:

   Exception Level 1:
   Message: 這是內部例外異常
   Type: System.InvalidOperationException
   Stack Trace:    at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 32
```

這裡將會使用底下程式碼來模擬聚合例外異常

```csharp
    throw new AggregateException("外層例外",
        new InvalidOperationException("內層例外1"),
        new Exception("內層例外2", new ArgumentException("更深層例外")),
        new AggregateException("外層例外",
        new InvalidOperationException("內層例外1"),
        new Exception("內層例外2", new ArgumentException("更深層例外"))
    )
    );
```

這裡將會有一個最外層的 [AggregateException] 例外異常，這個聚合例外異常將會包含了三個例外異常，包含了 [InvalidOperationException] 例外異常，一個 [Exception] 例外異常，以及一個 [AggregateException] 例外異常，而在這個聚合例外異常內，又包含了兩個 [InvalidOperationException] 例外，一個 [Exception] 例外，其中，在這個 [Exception] 物件中，又包含了一個 [ArgumentException] 例外異常。

底下將會是使用 Exception.ToString() 所顯示的例外異常文字敘述。

```plaintext
System.AggregateException: 外層例外 (內層例外1) (內層例外2) (外層例外 (內層例外1) (內層例外2))
 ---> System.InvalidOperationException: 內層例外1
   --- End of inner exception stack trace ---
   at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 56
 ---> (Inner Exception #1) System.Exception: 內層例外2
 ---> System.ArgumentException: 更深層例外
   --- End of inner exception stack trace ---<---

 ---> (Inner Exception #2) System.AggregateException: 外層例外 (內層例外1) (內層例外2)
 ---> System.InvalidOperationException: 內層例外1
   --- End of inner exception stack trace ---
 ---> (Inner Exception #1) System.Exception: 內層例外2
 ---> System.ArgumentException: 更深層例外
   --- End of inner exception stack trace ---<---
<---
```

底下將會是使用 [GetFullExceptionMessage] 延伸方法，取得所有 Inner, Aggregation 例外異常所顯示的例外異常文字敘述。

```plaintext
[Aggregation Exceptions]
Exception Level 0:
Message: 外層例外 (內層例外1) (內層例外2) (外層例外 (內層例外1) (內層例外2))
Type: System.AggregateException
Stack Trace:    at csExceptionToString.Program.Main(String[] args) in D:\Vulcan\GitHub\CSharp2024\csExceptionToString\csExceptionToString\Program.cs:line 56
Inner Exceptions:

   Exception Level 1:
   Message: 內層例外1
   Type: System.InvalidOperationException
   Stack Trace:

   Exception Level 1:
   Message: 內層例外2
   Type: System.Exception
   Stack Trace:
   Inner Exception:

      Exception Level 2:
      Message: 更深層例外
      Type: System.ArgumentException
      Stack Trace:

   [Aggregation Exceptions]
   Exception Level 1:
   Message: 外層例外 (內層例外1) (內層例外2)
   Type: System.AggregateException
   Stack Trace:
   Inner Exceptions:

      Exception Level 2:
      Message: 內層例外1
      Type: System.InvalidOperationException
      Stack Trace:

      Exception Level 2:
      Message: 內層例外2
      Type: System.Exception
      Stack Trace:
      Inner Exception:

         Exception Level 3:
         Message: 更深層例外
         Type: System.ArgumentException
         Stack Trace:
```

