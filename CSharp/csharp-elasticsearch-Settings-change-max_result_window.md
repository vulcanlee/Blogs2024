# Elasticsearch 系列 : 如何使用 C# / NEST 來變更 index.max_result_window 大小

![](../Images/cs2024-9964.png)

在 Elasticsearch 中，有一個重要的設定值，就是 `index.max_result_window`，這個設定值是用來設定在進行搜尋的時候，最大可以取得的搜尋結果數量。在 Elasticsearch 中，這個設定值的預設值是 `10000`，也就是說，當進行搜尋的時候，最多只能取得 `10000` 筆的搜尋結果。

對於一些應用場景，可能會需要取得更多的搜尋結果，否則就需要透過不同搜尋條件，讓每次搜尋出來的結果可以小於 10000 以下，可是，有些時候還是無法做到單一一次的搜尋，出來的數量小於10000這樣的結果。

這時候，就需要進行 `index.max_result_window` 的設定值變更。在這篇文章中，將會介紹如何透過 C# 與 NEST 這個套件，來進行 `index.max_result_window` 的設定值變更。

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

namespace csElasticsearchNestChangeSetting;

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
    static string IndexName = $"blog_setting";
    static async Task Main(string[] args)
    {
        var settings = new ConnectionSettings(new Uri("http://localhost:9200"))
            .DisableDirectStreaming()
            .BasicAuthentication("elastic", "elastic");

        var client = new ElasticClient(settings);

        await Console.Out.WriteLineAsync($"生成索引");
        await MakeRandomIndex(client);
        await Console.Out.WriteLineAsync($"取得索引設定");
        await GetIndexAllSetting(client);
        await Console.Out.WriteLineAsync($"變大 Result Windows Size");
        await EnlargeResultSize(client);
        await Console.Out.WriteLineAsync($"取得索引設定");
        await GetIndexAllSetting(client);
        await Console.Out.WriteLineAsync($"移除 Result Windows Size");
        await RemoveResultSize(client);
        await Console.Out.WriteLineAsync($"取得索引設定");
        await GetIndexAllSetting(client);
    }

    static async Task GetIndexAllSetting(IElasticClient client)
    {
        var response = client.Indices.GetSettings(IndexName);

        if (response.IsValid)
        {
            var indexSettings = response.Indices[IndexName].Settings;
            foreach (var setting in indexSettings)
            {
                Console.WriteLine($"{setting.Key}: {setting.Value}");
            }
        }
        else
        {
            Console.WriteLine($"Failed to retrieve index settings: {response.DebugInformation}");
        }
    }

    static async Task RemoveResultSize(IElasticClient client)
    {
        await Console.Out.WriteLineAsync();
        // 更新索引的配置
        var response = await client.Indices.UpdateSettingsAsync(IndexName, uis => uis
            .IndexSettings(s => s

                .Setting("index.max_result_window", 10_000)
            )
        );
        if (response.IsValid)
        {
            Console.WriteLine("Index settings updated successfully.");
        }
        else
        {
            Console.WriteLine($"Failed to update index settings: {response.DebugInformation}");
        }
        await Console.Out.WriteLineAsync();
    }

    static async Task EnlargeResultSize(IElasticClient client)
    {
        await Console.Out.WriteLineAsync();
        // 更新索引的配置
        var response = await client.Indices.UpdateSettingsAsync(IndexName, uis => uis
            .IndexSettings(s => s

                .Setting("index.max_result_window", 200_000)
            )
        );
        if (response.IsValid)
        {
            Console.WriteLine("Index settings updated successfully.");
        }
        else
        {
            Console.WriteLine($"Failed to update index settings: {response.DebugInformation}");
        }
        await Console.Out.WriteLineAsync();
    }

    static async Task MakeRandomIndex(IElasticClient client)
    {
        // 刪除指定的 Index
        await client.Indices.DeleteAsync(IndexName);

        Random random = new Random();

        var response = await client.IndexAsync<Blog>(new Blog()
        {
            BlogId = random.Next(),
            Title = $"Nice to meet your 999",
            Content = $"Hello Elasticsearch 999",
            CreateAt = DateTime.Now.AddDays(999),
            UpdateAt = DateTime.Now.AddDays(999),
        }, idx => idx.Index(IndexName));
        if (response.IsValid)
        {
            //Console.WriteLine($"Index document with ID {response.Id} succeeded.");
        }
        else
        {
            Console.WriteLine($"Error Message : {response.DebugInformation}");
        }
    }
}
```

在這段範例程式碼中，將會嘗試建立一個 [blog_setting] 索引，並且在這個索引中，新增一筆 Blog 資料。接著，將會透過 NEST 套件，來進行 `index.max_result_window` 的設定值變更。

因此，將會宣告一個 [Blog] 這個類別，並且在這個類別中，定義了一些屬性，用來代表 Blog 的資料結構。其中，對於 [ElasticsearchType] 屬性中的 [IdProperty] 屬性，這個屬性是用來指定這個類別中，哪一個屬性是用來代表這個類別的 ID 值。

這個程式碼中將會新設計四個方法，分別是 GetIndexAllSetting、RemoveResultSize、EnlargeResultSize 和 MakeRandomIndex。將會分別用來取得 [blog_setting] 索引的設定、移除 `index.max_result_window` 設定值、變更 `index.max_result_window` 設定值和建立一個隨機的索引文件。

在 [Main] 方法內，首先會先建立起 [ElasticClient] 物件，並且透過 [ConnectionSettings] 物件來設定需要相關連線參數與運作方式。

為了要能夠取得索引內的設定，這裡需要先來建立這個索引，因此，將會呼叫 [MakeRandomIndex] 方法；在這個方法內，會先使用 `await client.Indices.DeleteAsync(IndexName);` 將 [blog_setting] 索引刪除，接著，使用 `await client.IndexAsync` 方法來新增一筆文件到 [blog_setting] 索引內，如此，便可以建立一個全新的索引與具有預設值的索引設定內容。

回到 [Main] 方法內，接下來將會執行 [GetIndexAllSetting] 方法，使用 `client.Indices.GetSettings` 方法來取得 [blog_setting] 索引的設定值，並且將這些設定值輸出到螢幕上。這裡使用底下的方法

```csharp
var indexSettings = response.Indices[IndexName].Settings;
foreach (var setting in indexSettings)
{
    Console.WriteLine($"{setting.Key}: {setting.Value}");
}
```

了解到現在該特定索引內的設定內容之後，就可以將 [index.max_result_window] 的設定值進行變更。這裡將會呼叫 [EnlargeResultSize] 方法來完成這樣的工作，在該方法內，將會透過 `client.Indices.UpdateSettingsAsync` 方法來變更 [index.max_result_window] 的設定值。這裡使用 `Setting("index.max_result_window", 200_000)` 來設定 [index.max_result_window] 的值為 200000。

為了要能夠驗證這個設定值是否有被正確的變更，接著將會呼叫 [GetIndexAllSetting] 方法，來取得 [blog_setting] 索引的設定值，並且將這些設定值輸出到螢幕上。

最後，透過 [RemoveResultSize] 方法，將會移除 [index.max_result_window] 的設定值，並且再次透過 [GetIndexAllSetting] 方法，來取得 [blog_setting] 索引的設定值，並且將這些設定值輸出到螢幕上。

## 執行程式碼
* 在 Visual Studio 2022 IDE 內，按下 `F5` 鍵，或是在功能表列上，點選 [除錯] -> [開始偵錯]，來執行這個程式碼
* 程式碼執行完畢之後，將會在輸出視窗內，看到類似底下的訊息

```plaintext
生成索引

取得索引設定

index.number_of_replicas: 1
index.number_of_shards: 3
index.routing.allocation.include._tier_preference: data_content
index.provided_name: blog_setting
index.creation_date: 1721373669290
index.uuid: zP7yuvpXS2Gmpez3dv9EWw
index.version.created: 8100399

變大 Result Windows Size

Index settings updated successfully.

取得索引設定

index.number_of_replicas: 1
index.number_of_shards: 3
index.routing.allocation.include._tier_preference: data_content
index.provided_name: blog_setting
index.max_result_window: 200000
index.creation_date: 1721373669290
index.uuid: zP7yuvpXS2Gmpez3dv9EWw
index.version.created: 8100399

移除 Result Windows Size

Index settings updated successfully.

取得索引設定

index.number_of_replicas: 1
index.number_of_shards: 3
index.routing.allocation.include._tier_preference: data_content
index.provided_name: blog_setting
index.max_result_window: 10000
index.creation_date: 1721373669290
index.uuid: zP7yuvpXS2Gmpez3dv9EWw
index.version.created: 8100399
```
