# Elasticsearch 系列 : 如何使用 C# / NEST 找到是否有匹配符合的 index 名稱

![](../Images/cs2024-9967.png)

在 Elasticsearch 中，index 是一個非常重要的概念，它是用來存儲相關數據的地方，而在 Elasticsearch 中，index 的名稱是唯一的，這意味著在 Elasticsearch 中，每一個 index 都有一個唯一的名稱。

然而，有時候我們需要在 Elasticsearch 中找到是否有匹配符合的 index 名稱，這時候，我們可以使用 C# / NEST 套件來達到這個目的。例如，若有一群 Index 名稱為 : myIndex2020, myIndex2021, myIndex2022, myIndex2023, myIndex2024 等等，這些 Index 將會儲存當年度的資料，此時，若想要針對所有 Index 名稱前面有 myIndex 的 Index 進行使用 DSL 查詢，就需要先找到這些 Index 名稱。

有了這些 Index 名稱之後，我們就可以使用 C# / NEST 套件來進行查詢。在這篇文章中，將會介紹如何使用 C# / NEST 套件，來找到是否有匹配符合的 index 名稱。

這裡的範例程式碼將會隨機產生10個索引檔案，然後再使用 C# / NEST 套件來找到是否有匹配符合的 index 名稱。

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
* 找到 [專案名稱] 欄位，輸入 `csElasticsearchNestFindIndex` 作為專案名稱
* 在剛剛輸入的 [專案名稱] 欄位下方，確認沒有勾選 [將解決方案與專案至於相同目錄中] 這個檢查盒控制項
* 點選右下角的 [下一步] 按鈕
* 現在將會看到 [其他資訊] 對話窗
* 在 [架構] 欄位中，請選擇最新的開發框架，這裡選擇的 [架構] 是 : `.NET 8.0 (長期支援)`
* 在這個練習中，需要去勾選 [不要使用最上層陳述式(T)] 這個檢查盒控制項
  > 這裡的這個操作，可以由讀者自行決定是否要勾選這個檢查盒控制項
* 請點選右下角的 [建立] 按鈕

稍微等候一下，這個主控台專案將會建立完成

## 安裝要用到的 NuGet 開發套件

因為開發此專案時會用到這些 NuGet 套件，請依照底下說明，將需要用到的 NuGet 套件安裝起來。

### 安裝 NEST 套件

對於這個 NuGet 套件 [NEST] 其可以提供一個 .NET 平台的 Elasticsearch 客戶端庫，它允許您從 .NET 應用程序與 Elasticsearch 集群進行通信和互動。該庫為 Elasticsearch 提供了一個強類型的、易於使用的 API，可以用於執行各種操作，如索引管理、文檔檢索、搜索、數據分析等。

請依照底下說明操作步驟，將這個套件安裝到專案內

* 滑鼠右擊 [方案總管] 視窗內的 [專案節點] 下方的 [相依性] 節點
* 從彈出功能表清單中，點選 [管理 NuGet 套件] 這個功能選項清單
* 此時，將會看到 [NuGet: csElasticsearchCreate] 視窗
* 切換此視窗的標籤頁次到名稱為 [瀏覽] 這個標籤頁次
* 在左上方找到一個搜尋文字輸入盒，在此輸入 `NEST`
* 在視窗右方，將會看到該套件詳細說明的內容，其中，右上方有的 [安裝] 按鈕
* 點選這個 [安裝] 按鈕，將這個套件安裝到專案內

## 建立要使用的程式碼

* 在 [方案總管] 內找到並且開啟 [Program.cs] 檔案這個節點
* 使用底下 C# 程式碼，將原本的程式碼取代掉

```csharp
using Nest;

namespace csElasticsearchNestFindIndex;

[ElasticsearchType(IdProperty = nameof(BlogId))]
public class Blog
{
    public int BlogId { get; set; }
    public string Title { get; set; } = string.Empty;
    public string Content { get; set; } = string.Empty;
    public DateTime CreateAt { get; set; } = DateTime.Now;
    public DateTime UpdateAt { get; set; } = DateTime.Now;
}

internal class Program
{
    static string IndexPrefix = "random-index";
    static async Task Main(string[] args)
    {
        var settings = new ConnectionSettings(new Uri("http://10.1.1.231:9200/"))
            .DisableDirectStreaming()
            //.OnRequestCompleted(response =>
            //{
            //    Console.WriteLine($"Request: {response.RequestBodyInBytes?.Length} bytes");
            //    Console.WriteLine($"Response: {response.ResponseBodyInBytes?.Length} bytes");
            //})
            .BasicAuthentication("elastic", "elastic");

        var client = new ElasticClient(settings);

        await MakeRandomIndex(client);
        await FindMatchIndex(client);
    }

    static async Task FindMatchIndex(IElasticClient client)
    {
        var indexNamesResponse = await client.Indices.GetAliasAsync();
        var indexNames = indexNamesResponse.Indices.Keys.ToList();
        var findIndex = indexNames
            .Where(x=>x.Name.Contains(IndexPrefix+"-"));
        foreach (var index in findIndex)
        {
            Console.WriteLine($"Found index : {index}");
        }
        await Console.Out.WriteLineAsync($"按下任一按鍵，將會開始刪除 {IndexPrefix}- 開頭的索引名稱");
        Console.ReadKey();
        foreach (var index in findIndex)
        {
            await client.Indices.DeleteAsync(index);
            Console.WriteLine($"Found index : {index}");
        }
    }

    static async Task MakeRandomIndex(IElasticClient client)
    {
        int totalIndex = 10;
        Random random = new Random();
        for (var i = 0; i < totalIndex; i++)
        {
            var indexName = $"{IndexPrefix}-{i}";

            var response = await client.IndexAsync<Blog>(new Blog()
            {
                BlogId = random.Next(),
                Title = $"Nice to meet your 999",
                Content = $"Hello Elasticsearch 999",
                CreateAt = DateTime.Now.AddDays(999),
                UpdateAt = DateTime.Now.AddDays(999),
            }, idx => idx.Index(indexName));
            if (response.IsValid)
            {
                //Console.WriteLine($"Index document with ID {response.Id} succeeded.");
            }
            else
            {
                Console.WriteLine($"Error Message : {response.DebugInformation}");
            }
        }

        Console.WriteLine($"Total indices ({totalIndex}) has beed created");
    }
}
```

首先，設計一個 Blog 類別，這個類別將會用來當作索引的資料模型

接下來就是要開始建立10個隨機索引名稱，這裡的索引名稱將會採用 "random-index" 作為前綴，然後再加上一個數字，這個數字將會是從 0 到 9 的隨機數字。這樣的需求將會設計到 [MakeRandomIndex] 方法內。而在 Program.cs 內，將會使用 `await MakeRandomIndex(client);` 方式來呼叫這個方法。

在這個 [MakeRandomIndex] 方法內，會建立一個可以循環執行10次的迴圈，每次會在具有該迴圈索引值的索引檔案名稱內，新增一筆文件，直到10個索引檔案都完全建立出來。

接下來就會使用 `await FindMatchIndex(client);` 方式來呼叫 [FindMatchIndex] 方法，這個方法將會用來找到是否有匹配符合的 index 名稱，並且將這些 index 名稱列出來。

在這個方法內會先使用 `await client.Indices.GetAliasAsync();` 來取得所有的索引名稱，然後再使用 LINQ 查詢語法，來找到是否有匹配符合的 index 名稱，最後再將這些 index 名稱列出來。

## 執行程式

* 按下 `F5` 鍵，開始執行這個程式
* 程式將會開始執行，並且在主控台視窗內，將會看到類似下圖的輸出結果

```
Total indices (10) has beed created
Found index : random-index-0
Found index : random-index-3
Found index : random-index-2
Found index : random-index-7
Found index : random-index-1
Found index : random-index-9
Found index : random-index-6
Found index : random-index-8
Found index : random-index-5
Found index : random-index-4
按下任一按鍵，將會開始刪除 random-index- 開頭的索引名稱
Found index : random-index-0
Found index : random-index-3
Found index : random-index-2
Found index : random-index-7
Found index : random-index-1
Found index : random-index-9
Found index : random-index-6
Found index : random-index-8
Found index : random-index-5
Found index : random-index-4
```

