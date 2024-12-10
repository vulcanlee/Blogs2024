# 了解與比較各種經典的 Web 開發框架

![](../Images/cs2024-9921.png)

電腦上的應用程式，從傳統的命令提示字元介面到現代的圖形用戶界面，再到網頁應用程式，一直在不斷演進。每一次的演進都是為了更好地滿足用戶的需求，提高開發效率，降低維護成本。當到了以網頁為主的應用程式時候，各種應用技術與開發框架也應運而生。隨著時間推移與技術的不斷翻新，開發者們需要不斷學習新的技術，以應對不斷變化的需求。

當開發者要進行網頁應用程式開發的時候，將會面臨到選擇適合的框架的問題。ASP.NET 是微軟推出的一個 Web 開發框架，提供了多種不同的開發模型，包括 Web Forms、MVC、Razor Pages 等。此外，前端技術也在不斷發展，例如 React、Angular、Vue 等。開發者需要根據項目的需求、自身的技術背景和喜好，來選擇適合的框架。

以下是 ASP.NET 和其他前端技術推出時間的先後順序、開發背景、特色、優點、缺點以及需要搭配的開發技術，以便對這些框架進行分析：

以下是針對 ASP Classic（也稱為 ASP 或 Active Server Pages）的技能樹，並與其他開發框架進行比較。

## **ASP Classic**
ASP Classic 是微軟於 1990 年代末推出的伺服器端腳本技術，用於生成動態網頁。以下是需要掌握的技能樹以及其背景、優缺點等：

**推出時間：** 1996年  
**背景：在此之前，相關的網頁都是採用靜態內容的方式來製作，** ASP Classic 是在靜態網頁逐漸不能滿足動態交互需求的背景下推出的，例如，可以依據使用者的操作，查詢企業內部資料庫內的紀錄，並且重新產生網頁，並且顯示到網頁上。它使開發者可以在伺服器端生成 HTML 內容，增加網頁動態性，適應當時互聯網快速增長的需求。  
**特色：**  
- 主要基於伺服器端腳本，使用 VBScript 或 JScript（JavaScript 的微軟版本）。
- 提供動態網頁生成的基本功能，允許使用簡單的代碼與伺服器進行交互。

**優點：**
- **簡單易上手**：對於有 VBScript 或簡單腳本基礎的開發者來說很容易入門。
- **快速生成動態內容**：比靜態 HTML 更靈活，能夠動態生成內容。
- **適合小型應用**：簡單而輕量，適合用於一些基礎的表單處理和小型應用。

**缺點：**
- **結構化不足**：缺乏明確的分層結構，不適合複雜和大型應用的開發。
- **可擴展性差**：隨著應用規模的增大，代碼管理和維護變得困難。
- **與現代技術的兼容性差**：許多現代的工具、框架、和技術無法與 ASP Classic 良好集成。
- **語法限制**：主要使用 VBScript，相對於現代的 JavaScript 或 C#，表達能力有限且功能不足。

### **技能樹：**
- **VBScript 或 JScript**：ASP Classic 使用 VBScript 作為主要腳本語言，少數情況下也支持 JScript。
- **HTML/CSS**：需要理解基礎的 HTML 和 CSS，用於網頁內容展示和樣式控制。
- **SQL/T-SQL**：用於資料查詢和存取。常與 **SQL Server** 一起使用以管理資料庫。
- **ADO（ActiveX Data Objects）**：用於連接資料庫和執行 SQL 查詢。
- **IIS（Internet Information Services）**：需要掌握如何在 IIS 上設置和部署 ASP 應用，因為 IIS 是 ASP Classic 的主要運行環境。
- **JavaScript**（客戶端）：用於增強頁面的前端交互和驗證（如表單驗證）。
  
### **搭配技術：**
- **SQL Server 或 Access**：ASP Classic 主要使用這些資料庫來存儲和檢索數據。
- **COM+ 組件**：在某些情況下，可能需要使用 COM 來擴展功能，例如處理複雜邏輯。
- **FTP/SFTP**：了解如何將靜態和動態資源上傳至伺服器進行應用部署。

## ASP.NET (ASP.NET Web Forms)
**推出時間：** 2002年  
**背景：** 隨著時間推移，ASP Classic技術已經無法滿足開發上的應用，因此，微軟在1990年代推出了 ASP.NET，這是一種伺服器端技術，用來生成動態網頁。隨著需求增長，ASP.NET 在 .NET 平台上作為更現代化的替代方案，具備更好的結構化和開發者友好特性。  
**特色：**  
- 基於事件驅動的模型，類似於桌面應用程序的開發方式。
- 提供許多可重用的伺服器端控件，可以通過簡單拖放進行設計。  

**優點：**  
- 提供許多封裝好的伺服器控件，加速開發速度。
- 狀態管理（ViewState）方便了開發者保存表單資料。
- 類似桌面應用程式的開發方式使 VB6 開發者更容易適應。  

**缺點：**  
- ViewState 大量使用導致網頁負擔過重，效能低下。
- HTML 代碼生成受限於控件，對前端頁面精細控制困難。
- 網頁開發與前端 HTML 的分離程度低，不易適應日益成熟的前端技術。

**搭配技術：**  
- JavaScript：用於部分客製化的前端交互。
- SQL Server 或其他數據庫：作為後端的資料存儲。

### **ASP.NET Web Forms 技能樹**
ASP.NET Web Forms 為一種較為傳統的開發方式，主要是基於拖放的控件開發，因此需要以下技能：
- **C# 或 VB.NET**：基礎後端語言，用於撰寫伺服器端代碼。
- **ASP.NET Web Forms**：理解 Web Forms 的事件驅動模型和生命週期。
- **HTML/CSS**：用於調整頁面的佈局和樣式。
- **JavaScript**：雖然 Web Forms 本質上不強調 JavaScript，但為了更好地完成前端交互，掌握 JavaScript 是必要的。
- **SQL/T-SQL**：用於編寫查詢以與資料庫交互，常與 **SQL Server** 一起使用。
- **ADO.NET**：負責數據訪問操作。
- **IIS（Internet Information Services）**：了解如何部署 ASP.NET Web Forms 應用至 IIS。

## ASP.NET MVC
**推出時間：** 2009年  
**背景：** 因為科技始終來自於人性，ASP.NET Web Forms的問題隨之浮現，伴隨著網頁應用系統的複雜度增長，MVC (Model-View-Controller) 設計模式成為一個標準，提供更好的分離關注點，便於維護。微軟推出 ASP.NET MVC 為應對複雜應用需求，特別是更細緻的 HTML 控制和更靈活的設計。  
**特色：**  
- 使用 MVC 設計模式，將應用分為模型（Model）、視圖（View）和控制器（Controller），提高代碼結構化。
- 更加貼近 HTML，開發者可以完全控制生成的 HTML。

**優點：**  
- 清晰的架構分離使應用更易於維護和測試。
- 完全控制生成的 HTML，有助於實現更佳的 SEO 和響應式設計。
- 更適合單元測試。  

**缺點：**  
- 初學者可能需要更多的學習時間，尤其是對 MVC 設計模式不熟悉的開發者。
- 相對於 Web Forms 的拖放控件式開發，MVC 需要更多手寫代碼。

**搭配技術：**  
- JavaScript（例如 jQuery）：用於豐富的前端交互。
- AJAX：用於局部頁面更新，提高用戶體驗。
- SQL Server 或其他數據庫：作為後端的資料存儲。

### **ASP.NET MVC 技能樹**
ASP.NET MVC 使用 MVC 設計模式，更注重架構分離，所需技能包含：
- **C#**：用於後端控制器和邏輯開發。
- **ASP.NET MVC**：理解 MVC 模式（模型、視圖、控制器），熟悉路由系統。
- **Razor 語法**：用於生成動態視圖，編寫伺服器端代碼嵌入 HTML。
- **HTML/CSS**：控制頁面佈局，實現良好的網頁設計。
- **JavaScript 和 jQuery**：實現頁面互動效果，特別是局部刷新（AJAX）。
- **Entity Framework（EF）**：用於與資料庫進行交互的 ORM 工具。
- **REST API**：理解如何開發 API，支持前後端分離的需求。
- **SQL Server**：資料存取的基礎技能。
- **IIS**：掌握如何配置和部署 ASP.NET MVC 應用。

## React
**推出時間：** 2013年，由 Facebook 推出  
**背景：** 網頁應用越來越複雜，需求增加，因為之前的技術都是在後端生成要顯示 HTML & CSS & JavaScript，這樣的架構大幅增加了伺服器端的負載，而瀏覽器端的電腦也越來越強大與便宜，大家就在想說，是否可以將許多工作移交到前端來處理，而面對到需要更靈活與好的使用者操作體驗，也需要加大這方面的設計與挑戰，因此，前端需要更高效地管理狀態和更新界面。React 引入了組件化開發理念和虛擬 DOM，解決了傳統前端框架效率低下的問題。  
**特色：**  
- 基於組件的開發方式，可以將應用拆分為可重用的部分。
- 虛擬 DOM 提供了高效的界面渲染。
- 提供單向數據流，便於預測界面狀態的變化。  

**優點：**  
- 提高了前端開發的靈活性和可重用性。
- 更高效的渲染性能和快速的狀態更新。
- 豐富的生態系統和社區支援。  

**缺點：**  
- 開發的學習曲線較高，JSX 語法對部分開發者不友好。
- 組件的管理隨著應用規模增大可能變得困難。

**搭配技術：**  
- Redux 或其他狀態管理工具：用於大型應用的狀態管理。
- REST API 或 GraphQL：用於和後端交互。
- ASP.NET Core：作為後端服務提供 API。

### **React 技能樹**
React 是當前流行的前端框架，具備強大的組件化能力，所需技能如下：
- **JavaScript/ES6+**：需要深入了解 JavaScript，特別是 ES6+ 特性（如箭頭函數、解構賦值、模組化等）。
- **JSX**：React 使用的擴展語法，結合 JavaScript 和 HTML。
- **React**：掌握組件、props、state、生命週期方法、Hooks（如 useState, useEffect）等核心概念。
- **CSS/SCSS**：控制網頁樣式，掌握 CSS 預處理器（如 SCSS）有助於應對複雜樣式。
- **Redux 或其他狀態管理庫**：用於管理應用的全局狀態，對於大型應用尤為重要。
- **REST API/GraphQL**：理解如何從後端拉取數據以更新 UI。
- **Webpack 或 Vite**：理解如何使用打包工具來優化應用。
- **Node.js 和 npm**：用於項目依賴管理和簡單的後端開發。
- **TypeScript**（可選）：提升代碼的健壯性和可維護性。

## ASP.NET Razor Page
**推出時間：** 2017年，隨 ASP.NET Core 2.0 一同推出  
**背景：** Razor Page 是對 Web Forms 和 MVC 的一個簡化和改進，它更加關注單個頁面的場景，減少 MVC 的複雜性，適合中小型應用。  
**特色：**  
- 每個頁面都有自己對應的頁面模型，更直觀地實現頁面和後端的綁定。
- 更簡潔的頁面代碼，減少樣板代碼。  

**優點：**  
- 比 MVC 更簡單，適合快速開發 CRUD 應用。
- 對初學者更加友好，因為頁面和代碼更集中。  

**缺點：**  
- 不適合大型和高度複雜的應用。
- 在分離關注點上不如 MVC，那麼清晰。  

**搭配技術：**  
- JavaScript 和 AJAX：用於豐富的前端交互。
- SQL Server 或其他數據庫：作為後端資料存儲。

### **ASP.NET Razor Pages 技能樹**
Razor Pages 是 ASP.NET Core 中一種簡化的開發模式，適合開發簡單的 Web 應用：
- **C#**：用於後端開發。
- **ASP.NET Core Razor Pages**：理解 Razor Pages 的結構和頁面模型的作用。
- **Razor 語法**：用於在頁面中嵌入伺服器端代碼。
- **HTML/CSS**：進行頁面佈局和樣式控制。
- **JavaScript/jQuery**：用於簡單的頁面交互。
- **Entity Framework Core**：用於數據訪問和持久化。
- **SQL Server**：作為資料存取的基礎技術。
- **ASP.NET Core 中間件**：了解中間件管道及如何自定義中間件。
- **IIS 或 Azure**：了解如何部署應用到 IIS 或 Azure。

## Blazor
**推出時間：** 2018年 (ASP.NET Core 3.0 之後)  
**背景：** Blazor 為滿足開發者使用 C# 寫前端應用的需求，並且得益於 WebAssembly 技術的成熟，微軟推出 Blazor，允許開發者使用 C# 開發客戶端應用，統一技術棧。  
**特色：**  
- 可以使用 C# 而不是 JavaScript 來開發互動豐富的前端應用。
- 支持兩種模式：Blazor Server 和 Blazor WebAssembly，分別基於伺服器端和客戶端運行。  

**優點：**  
- 開發者可以只用 C# 技術棧進行前後端開發，減少學習不同技術的成本。
- 利用 WebAssembly 技術，提供接近原生性能的客戶端執行環境。  

**缺點：**  
- WebAssembly 的性能受限於當前瀏覽器技術。
- 相較於傳統前端技術，Blazor 生態系統尚未成熟，第三方工具和庫較少。  

**搭配技術：**  
- .NET Core API：用於後端服務。
- SignalR：在 Blazor Server 模式下用於維持伺服器與客戶端的即時通信。
- SQL Server 或其他數據庫：作為資料存儲。

### **Blazor 技能樹**
Blazor 允許使用 C# 代替 JavaScript 來開發互動式前端應用：
- **C#**：Blazor 開發完全基於 C#，需要熟悉其語法和概念。
- **ASP.NET Core Blazor**：了解 Blazor 的兩種模式（Server 和 WebAssembly）及其差異。
- **Razor 語法**：Blazor 使用 Razor 語法來構建 UI 組件。
- **HTML/CSS**：控制應用的前端佈局和樣式。
- **JavaScript（可選）**：Blazor 可以通過 JSInterop 來調用 JavaScript，因此 JavaScript 知識仍有幫助。
- **Entity Framework Core**：用於數據訪問。
- **SignalR**：在 Blazor Server 模式下，了解如何使用 SignalR 進行即時通信。
- **WebAssembly**：理解 WebAssembly 基本概念，有助於使用 Blazor WebAssembly。

# 總結
- **ASP.NET Web Forms** 適合不熟悉前端的開發者，拖放控件方便，但頁面控制能力差。
- **ASP.NET MVC** 提供更好的 HTML 控制和清晰的架構分離，適合中大型應用。
- **React** 及其他前端框架提供組件化和高效渲染，適合需要高交互性的應用。
- **ASP.NET Razor Page** 適合簡單的 CRUD 應用，結構比 MVC 簡單。
- **Blazor** 則試圖統一 C# 技術棧，讓前端開發者不再依賴 JavaScript，適合喜愛 .NET 的開發者。

## 額外技術樹 - 通用技術
底下所列出技術對於所有框架來說都是需要能夠孰悉的，：
- **Git**：版本控制系統，適用於所有類型的開發。
- **HTTP 基本概念**：理解 HTTP 狀態碼、請求和回應頭，有助於 API 開發。
- **RESTful API 開發**：對於前後端分離項目至關重要。
- **OAuth / JWT 認證**：實現安全登錄和驗證，保護 API。
- **DevOps**：了解基本的 DevOps 流程、CI/CD（如 GitHub Actions、Azure DevOps），有助於項目自動化和部署。
- **雲服務**（如 **Azure**、**AWS**）：了解如何將應用部署到雲端，擴展應用的規模和可用性。

每個框架的推出和演進都是針對當時的技術需求和開發者的痛點而來的。選擇合適的框架取決於應用的規模、開發者的背景，以及項目的特定需求。









當選擇不同的開發框架時，通常需要掌握各自特定的技能組合。以下是各種開發框架或方式所需要的技能樹，將幫助您了解這些框架的全面技術需求。





這些技能組合可以幫助您根據選擇的開發框架制定學習和掌握的方向，選擇最適合您的應用開發需求。每個框架都有其特定的優勢和適用範圍，因此技能的選擇和應用也會有所不同。






