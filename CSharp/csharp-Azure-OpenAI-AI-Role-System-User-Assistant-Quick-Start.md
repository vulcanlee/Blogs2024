# Azure OpenAI AOAI 2.0 : 3 指定不同的角色 (Role) 來提升模型的行為控制和回應精確度

![](../Images/cs2024-9925.png)


這是繼上兩篇 [Azure OpenAI AOAI 2.0 : 1 第一次使用 Azure.AI.OpenAI 2.0.0 開發教學
](https://csharpkh.blogspot.com/2024/10/csharp-Azure-OpenAI-AI-ChatCompletion-Prompt-ASSISTANT-Quick-Start.html) / [Azure OpenAI AOAI 2.0 : 2 設計 支援類別，簡化設計過程](https://csharpkh.blogspot.com/2024/10/csharp-Azure-OpenAI-AI-Helper-Factory-Quick-Develop.html) 關於 Azure OpenAI for .NET 2.0 開發文章的接續內容，這也是一個系列文章，將會持續更新，希望能夠幫助大家更快速的上手 Azure OpenAI 2.0 的開發。

在上一篇文章中，從基本的建立起 AzureOpenAIClient 物件，以及創建該物件需要提供的參數該如何進行安全性的保護設計做出說明，接著需要創建 ChatClient 物件，以便可以進行對話 Chat Completion 的操作，而創建該物件需要提供的模型名稱，需要從哪裡取得做出說明；之後，設計一個 Prompt 提示詞，透過 ChatClient 來呼叫對應的 API，將提示詞送入到 Azure OpenAI LLM 大語言模型內，一旦大語言模型生成出內容之後，便會得到 Completion 完成內容之一系列設計過程。

這篇文章將會來探討與使用 Azure OpenAI 2.0 的角色 (Role) 機制，透過指定不同的角色 (Role) 來提升模型的行為控制和回應精確度。



