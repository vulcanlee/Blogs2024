# 使用 Syncfusion 套件讀取 Excel 檔案內容

![](../Images/cs2024-9832.png)

有了可以將 JSON 物件儲存到資料庫，並且可以進行搜尋的技術，以及具有了將 JSON 物件合併的方法，接下來，我們將會進一步的學習如何使用 Syncfusion 套件，來讀取 Excel 檔案內容，如此，便可以完成工作上的專案需求，這個專案需求是讀取 Excel 檔案內容，並且將 Excel 檔案內容的資料，轉換成 JSON 物件，這樣，便可以將 Excel 檔案內容，轉換成 JSON 物件，進而進行資料庫的儲存。

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

## 安裝要用到的 NuGet 開發套件

因為開發此專案時會用到這些 NuGet 套件，請依照底下說明，將需要用到的 NuGet 套件安裝起來。

### 安裝 Syncfusion.XlsIO.Net.Core 套件

請依照底下說明操作步驟，將這個套件安裝到專案內

* 滑鼠右擊 [方案總管] 視窗內的 [專案節點] 下方的 [相依性] 節點
* 從彈出功能表清單中，點選 [管理 NuGet 套件] 這個功能選項清單
* 此時，將會看到 [NuGet: csReadExcelSyncfusion] 視窗
* 切換此視窗的標籤頁次到名稱為 [瀏覽] 這個標籤頁次
* 在左上方找到一個搜尋文字輸入盒，在此輸入 `Syncfusion.XlsIO.Net.Core`
* 在視窗右方，將會看到該套件詳細說明的內容，其中，右上方有的 [安裝] 按鈕
* 點選這個 [安裝] 按鈕，將這個套件安裝到專案內

## 建立 HomePageModel.cs 類別內容

* 滑鼠右擊專案節點
* 從功能表清單內，點選 [加入] > [類別] 
* 在 [新增項目 - csReadExcelSyncfusion] 視窗中
  * 在左方的範本清單中，找到並且點選 [類別] 範本
  * 在右方的 [名稱] 文字方塊中，輸入 `HomePageModel` 作為類別名稱
  * 點選右下角的 [新增] 按鈕
* 將底下的程式碼取代掉這個類別內容

```csharp
namespace DataModel.Models;

public class HomePageModel
{
    public string 性別 { get; set; }
    public string 年齡 { get; set; }
    public string 身高 { get; set; }
    public string 體重 { get; set; }
    public string BMI { get; set; }
}
```

在這個類別內，定義了一個病患的基本資料，包含了性別、年齡、身高、體重、BMI 這幾個欄位，這個類別將會用來儲存從 Excel 檔案內讀取到的資料內容。

## 建立 ExcleService.cs 類別內容

這個 [ExcleService] 類別將會是整體核心，這個類別將會透過 Syncfusion 套件提供的功能，負責讀取 Excel 檔案內容。

* 滑鼠右擊專案節點
* 從功能表清單內，點選 [加入] > [類別] 
* 在 [新增項目 - csReadExcelSyncfusion] 視窗中
  * 在左方的範本清單中，找到並且點選 [類別] 範本
  * 在右方的 [名稱] 文字方塊中，輸入 `ExcleService` 作為類別名稱
  * 點選右下角的 [新增] 按鈕
* 將底下的程式碼取代掉這個類別內容

```csharp
public class ExcleService
{
    public void ReadExcel()
    {
        NextGenerationSportsHealthManagementModel healthManagementModel = new();
        using (ExcelEngine excelEngine = new ExcelEngine())
        {
            IApplication application = excelEngine.Excel;

            application.DefaultVersion = ExcelVersion.Xlsx;

            using (FileStream sampleFile = new FileStream("sample.xlsx", FileMode.Open))
            {
                IWorkbook workbook = application.Workbooks.Open(sampleFile);

                //Access first worksheet from the workbook.
                IWorksheet worksheetCT身體組成 = workbook.Worksheets["CT身體組成"];
                IWorksheet worksheetBIA身體組成 = workbook.Worksheets["BIA身體組成(初版)"];

                //Access a cell value from Excel
                healthManagementModel.HomePageModel.性別 = worksheetCT身體組成.Range["E2"].Value;
                healthManagementModel.HomePageModel.年齡 = worksheetCT身體組成.Range["D2"].Value;
                healthManagementModel.HomePageModel.身高 = worksheetBIA身體組成.Range["C2"].Value;
                healthManagementModel.HomePageModel.體重 = worksheetBIA身體組成.Range["C3"].Value;
                healthManagementModel.HomePageModel.BMI = worksheetBIA身體組成.Range["C5"].Value;

                ShowInformation(healthManagementModel);
            }
        }

    }
    public void ShowInformation(NextGenerationSportsHealthManagementModel healthManagementModel)
    {
        Console.WriteLine($"性別 {healthManagementModel.HomePageModel.性別}");
        Console.WriteLine($"年齡 {healthManagementModel.HomePageModel.年齡}");
        Console.WriteLine($"身高 {healthManagementModel.HomePageModel.身高}");
        Console.WriteLine($"體重 {healthManagementModel.HomePageModel.體重}");
        Console.WriteLine($"BMI {healthManagementModel.HomePageModel.BMI}");
    }
}
```

在這個 [ExcleService] 類別內，有個 [ReadExcel] 方法，首先會先建立一個 [ExcelEngine] 物件，這是讀取 Excel 資料的核心物件，接著取得 [IApplication] 物件，這是 Excel 應用程式的物件，接著使用 [FileStream] 來開啟 Excel 檔案，透過 `application.Workbooks.Open(sampleFile)` 方法，取得 Excel 檔案內的工作表，該物件將會一個型別為 [IWorksheet] 的物件，最後，透過工作表的 [Range] 屬性，取得 Excel 檔案內的資料內容，在 [Range] 屬性內，使用 Excel 的 Cell 定位方式，指定要讀取哪個 Row & Column 內的資料，如此，便可以取得該 Cell 內的值。

## 修改 Program.cs 類別內容

在這篇文章中，將會把會用到的新類別與程式碼，都寫入到 [Program.cs] 這個檔案中，請依照底下的操作，修改 [Program.cs] 這個檔案的內容

* 在專案中找到並且打開 [Program.cs] 檔案
* 將底下的程式碼取代掉 `Program.cs` 檔案中內容

```csharp
using SyncExcel.Services;

namespace csReadExcelSyncfusion
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");
            ExcleService excleService = new();
            excleService.ReadExcel();
        }
    }
}
```

在 [Main] 方法內，建立一個 [ExcleService] 物件，並且呼叫 [ReadExcel] 方法，這樣，便可以讀取 Excel 檔案內容。

## 執行程式

* 按下 `F5` 鍵，開始執行這個程式
* 程式將會開始執行，並且在主控台視窗內，將會看到類似下圖的輸出結果

```plaintext
Hello, World!
性別 M
年齡 23Y
身高 177.6
體重 63.1
BMI 20
```