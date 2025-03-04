# 合併兩個 JSON 物件

![](../Images/cs2024-9833.png)

在上一篇文章中，[使用 Dapper 對 JSON 欄位進行資料庫存取
](https://csharpkh.blogspot.com/2025/03/csharp-Dapper-JSON-JSONVALUE.html)，說明了如何將 JSON 物件儲存到資料庫內，並且如何下達可以查詢 JSON 內容值的 SQL 指令，這篇文章將會說明如何將兩個 JSON 物件合併成一個 JSON 物件。

會有這樣的動機在於，自身有個專案，可以讓使用者自行匯入多個 Excel 檔案內容，然而，這些 Excel 檔案內容的欄位數量是不固定的，而且欄位名稱也可能會有所變動，面對這樣的需求，將可以把每一個 ROW 紀錄是唯一筆 JSON 物件，然後將這些 JSON 物件合併成一個 JSON 物件，這樣的動作，可以讓使用者在匯入 Excel 檔案後，可以透過一個 JSON 物件，來查看所有的 ROW 紀錄。

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
* 找到 [專案名稱] 欄位，輸入 `csJsonMerge` 作為專案名稱
* 在剛剛輸入的 [專案名稱] 欄位下方，確認沒有勾選 [將解決方案與專案至於相同目錄中] 這個檢查盒控制項
* 點選右下角的 [下一步] 按鈕
* 現在將會看到 [其他資訊] 對話窗
* 在 [架構] 欄位中，請選擇最新的開發框架，這裡選擇的 [架構] 是 : `.NET 8.0 (長期支援)`
* 在這個練習中，需要去勾選 [不要使用最上層陳述式(T)] 這個檢查盒控制項
  > 這裡的這個操作，可以由讀者自行決定是否要勾選這個檢查盒控制項
* 請點選右下角的 [建立] 按鈕

稍微等候一下，這個 背景工作服務 專案將會建立完成

## 安裝要用到的 NuGet 開發套件

因為開發此專案時會用到這些 NuGet 套件，請依照底下說明，將需要用到的 NuGet 套件安裝起來。

### 安裝 Newtonsoft.Json 套件

請依照底下說明操作步驟，將這個套件安裝到專案內

* 滑鼠右擊 [方案總管] 視窗內的 [專案節點] 下方的 [相依性] 節點
* 從彈出功能表清單中，點選 [管理 NuGet 套件] 這個功能選項清單
* 此時，將會看到 [NuGet: csJsonMerge] 視窗
* 切換此視窗的標籤頁次到名稱為 [瀏覽] 這個標籤頁次
* 在左上方找到一個搜尋文字輸入盒，在此輸入 `Newtonsoft.Json`
* 在視窗右方，將會看到該套件詳細說明的內容，其中，右上方有的 [安裝] 按鈕
* 點選這個 [安裝] 按鈕，將這個套件安裝到專案內

## 修改 Program.cs 類別內容

在這篇文章中，將會把會用到的新類別與程式碼，都寫入到 [Program.cs] 這個檔案中，請依照底下的操作，修改 [Program.cs] 這個檔案的內容

* 在專案中找到並且打開 [Program.cs] 檔案
* 將底下的程式碼取代掉 `Program.cs` 檔案中內容

```csharp
using Newtonsoft.Json.Linq;
using System.Text.Json;
using System.Text.Json.Nodes;
using System.Xml;

namespace csJsonMerge;

internal class Program
{
    static void Main(string[] args)
    {
        // 舊 JSON
        string oldJson = """ 
        {
            "F1": 123,
            "F2": "abc"
        }
        """;

        // 新 JSON
        string newJson = """
        {
            "F1": "890",
            "F3": true
        }
        """;

        #region 使用 Json.NET
        // 將 JSON 轉換為 JObject
        JObject oldJsonObject = JObject.Parse(oldJson);
        JObject newJsonObject = JObject.Parse(newJson);

        // 合併兩個 JObject
        oldJsonObject.Merge(newJsonObject, new JsonMergeSettings
        {
            // 覆蓋相同的屬性
            MergeArrayHandling = MergeArrayHandling.Merge
        });

        // 轉換回 JSON 字串
        string mergedJson = oldJsonObject.ToString((Newtonsoft.Json.Formatting)Formatting.Indented);

        // 顯示結果
        Console.WriteLine(mergedJson);
        #endregion

        #region 使用 System.Text.Json
        JsonNode node1 = JsonNode.Parse(oldJson);
        JsonNode node2 = JsonNode.Parse(newJson);

        JsonNode MergeJsonNodes(JsonNode target, JsonNode source)
        {
            if (source is JsonObject sourceObject && target is JsonObject targetObject)
            {
                foreach (var property in sourceObject)
                {
                    if (targetObject.ContainsKey(property.Key) &&
                        targetObject[property.Key] is JsonObject &&
                        property.Value is JsonObject)
                    {
                        targetObject[property.Key] = MergeJsonNodes(targetObject[property.Key], property.Value);
                    }
                    else
                    {
                        targetObject[property.Key] = property.Value.DeepClone();
                    }
                }
                return targetObject;
            }
            return source.DeepClone();
        }

        JsonNode mergedNode = MergeJsonNodes(node1.DeepClone(), node2);

        string mergedJson2 = mergedNode.ToJsonString(new JsonSerializerOptions
        {
            WriteIndented = true
        });

        Console.WriteLine(mergedJson2);
        #endregion
    }
}
```

在 Main 的方法內，首先建立兩個 Json 字串，分別是 `oldJson` 與 `newJson`，這兩個 JSON 字串內容如下

```json
{
  "F1": "123",
  "F2": "abc",
}
```

```json
{
  "F1": "890",
  "F3": true
}
```

接下來將會使用 Json.NET 與 System.Text.Json 兩種方式，來合併這兩個 JSON 物件，並且將合併後的 JSON 物件輸出到主控台視窗內。

首先將會看到如何使用 Json.NET 的方法，這裡將會把 [oldJson] 與 [newJson] 解析成為 [JObject] 物件，然後使用 [JObject.Merge] 方法，將兩個 [JObject] 物件合併成一個 [JObject] 物件，在這裡透過 [JsonMergeSettings] 物件，來告知要合併的行為，這裡使用到了 [MergeArrayHandling.Merge] ，表示要進行覆蓋相同的屬性。

最後將這個合併後的 [JObject] 物件轉換成為 JSON 字串，並且輸出到主控台視窗內。

接著將會看到如何使用 System.Text.Json 的方法，這裡將會把 [oldJson] 與 [newJson] 解析成為 [JsonNode] 物件，然後使用自訂的方法 [MergeJsonNodes] 來合併這兩個 [JsonNode] 物件，這個方法會遞迴的合併兩個 [JsonNode] 物件，這裡的 foreach 迴圈內，會檢查是否有相同的屬性名稱，如果有相同的屬性名稱，則會進行合併，否則就是直接複製屬性值。

最後將合併後的 [JsonNode] 物件轉換成為 JSON 字串，並且輸出到主控台視窗內。

## 執行程式

* 按下 `F5` 鍵，開始執行這個程式
* 程式將會開始執行，並且在主控台視窗內，將會看到類似下圖的輸出結果

```json
{
  "F1": "890",
  "F2": "abc",
  "F3": true
}
{
  "F1": "890",
  "F2": "abc",
  "F3": true
}
```
