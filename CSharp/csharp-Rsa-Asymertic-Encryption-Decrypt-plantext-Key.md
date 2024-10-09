# .NET 8 / C# 非對稱式 RSA 加解密使用教學

在上一篇文章中，我們介紹了如何使用 C# 來進行對稱式加解密的操作，透過對稱式加解密的操作，可以讓我們將資料進行加密處理，進而保護資料的安全性，而在這篇文章中，將會介紹如何使用 C# 來進行非對稱式加解密的操作，透過非對稱式加解密的操作，可以讓我們透過不同的金鑰來進行加解密的操作，這樣的加解密方式，可以讓我們更加的安全。

所謂的非對稱式加解密，是指加密與解密使用不同的金鑰，這樣的加解密方式，可以讓我們透過公開金鑰來進行加密，而私密金鑰來進行解密，這樣的加解密方式，可以讓我們更加的安全，因為即使有人取得了公開金鑰，也無法透過公開金鑰來進行解密的操作，這樣的加解密方式，可以讓我們更加的安全。而傳統上，當一名使用者創建了一組公開金鑰與私密金鑰之後，會將公開金鑰公開給其他人，而私密金鑰則是保留給自己，這樣的加解密方式，可以讓我們更加的安全。

在這篇文章中，將會 RSA 這個演算法作為公鑰與私鑰的計算依據，所謂的 RSA 演算法，是一種非對稱式加解密的演算法，透過 RSA 演算法，可以讓我們進行非對稱式加解密的操作，而在這篇文章中，將會介紹如何使用 C# 來進行 RSA 加解密的操作，透過這篇文章的學習，讀者將可以學會如何使用 C# 來進行 RSA 加解密的操作。

## 建立測試專案

請依照底下的操作，建立起這篇文章需要用到的練習專案

* 打開 Visual Studio 2022 IDE 應用程式
* 從 [Visual Studio 2022] 對話窗中，點選右下方的 [建立新的專案] 按鈕
* 在 [建立新專案] 對話窗右半部
  * 切換 [所有語言 (L)] 下拉選單控制項為 [C#]
  * 切換 [所有專案類型 (T)] 下拉選單控制項為 [服務]
* 在中間的專案範本清單中，找到並且點選 [背景工作服務] 專案範本選項
  > 用於建立 Worker Service 的空白專案範本
* 點選右下角的 [下一步] 按鈕
* 在 [設定新的專案] 對話窗
* 找到 [專案名稱] 欄位，輸入 `csSymmetricEncryption` 作為專案名稱
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
using System.Security.Cryptography;
using System.Text;

namespace csAsymmetricEncryption;

internal class Program
{
    static void Main(string[] args)
    {
        RsaTool rsaTool = new();
        Console.WriteLine("Generating keys for Jim...");
        RSA jimRsa = rsaTool.GenerateKeys();
        string jimPublicKey = rsaTool.GetPublicKey(jimRsa);
        string jimPrivateKey = rsaTool.GetPrivateKey(jimRsa);
        Console.WriteLine($"Jim Public Key : {jimPublicKey}");
        Console.WriteLine($"");
        Console.WriteLine($"Jim Private Key : {jimPrivateKey}");
        Console.WriteLine($""); Console.WriteLine($"");

        Console.WriteLine("Generating keys for Bob...");
        RSA BobRsa = rsaTool.GenerateKeys();
        string BobPublicKey = rsaTool.GetPublicKey(BobRsa);
        string BobPrivateKey = rsaTool.GetPrivateKey(BobRsa);
        Console.WriteLine($"Bob Public Key : {BobPublicKey}");
        Console.WriteLine($"");
        Console.WriteLine($"Bob Private Key : {BobPrivateKey}");
        Console.WriteLine($""); Console.WriteLine($"");



        string plainText = "Hello, World!";
        Console.WriteLine($"Jim 準備要送出的未加密明碼文字 : {plainText}");
        string encryptedForBob = rsaTool.Encrypt(BobPublicKey, plainText);
        Console.WriteLine($"Jim 使用 Bob 公開金鑰 加密後的密文文字 : {encryptedForBob}");

        string decryptedByBob = rsaTool.Decrypt(BobPrivateKey, encryptedForBob);
        Console.WriteLine($"Bob 使用自己私鑰 進行解密後的明碼文字 : {decryptedByBob}");
        Console.WriteLine($""); Console.WriteLine($"");



        plainText = "Hello, World!";
        Console.WriteLine($"Bob 準備要送出的未加密明碼文字 : {plainText}");
        string encryptedForJim = rsaTool.Encrypt(jimPublicKey, plainText);
        Console.WriteLine($"Bob 使用 Jim 公開金鑰 加密後的密文文字 : {encryptedForJim}");

        string decryptedByJim = rsaTool.Decrypt(jimPrivateKey, encryptedForJim);
        Console.WriteLine($"Jim 使用自己私鑰 進行解密後的明碼文字 : {decryptedByJim}");
        Console.WriteLine($""); Console.WriteLine($"");



        plainText = "What happened to you?  你怎麼了? 123!";
        Console.WriteLine($"Bob 準備要送出的未加密明碼文字 : {plainText}");
        encryptedForJim = rsaTool.Encrypt(jimPublicKey, plainText);
        Console.WriteLine($"Bob 使用 Jim 公開金鑰 加密後的密文文字 : {encryptedForJim}");

        decryptedByJim = rsaTool.Decrypt(jimPrivateKey, encryptedForJim);
        Console.WriteLine($"Jim 使用自己私鑰 進行解密後的明碼文字 : {decryptedByJim}");
        Console.WriteLine($""); Console.WriteLine($"");
    }
}

public class RsaTool
{
    public RSA GenerateKeys()
    {
        RSA rsa = RSA.Create(2048); // 2048 位元的密鑰長度
        return rsa;
    }

    public string GetPublicKey(RSA rsa)
    {
        return Convert.ToBase64String(rsa.ExportRSAPublicKey());
    }

    public string GetPrivateKey(RSA rsa)
    {
        return Convert.ToBase64String(rsa.ExportRSAPrivateKey());
    }

    public string Encrypt(string publicKey, string plainText)
    {
        using (RSA rsa = RSA.Create())
        {
            rsa.ImportRSAPublicKey(Convert.FromBase64String(publicKey), out _);
            byte[] plainBytes = Encoding.UTF8.GetBytes(plainText);
            byte[] encryptedBytes = rsa.Encrypt(plainBytes, RSAEncryptionPadding.OaepSHA256);
            return Convert.ToBase64String(encryptedBytes);
        }
    }

    public string Decrypt(string privateKey, string cipherText)
    {
        using (RSA rsa = RSA.Create())
        {
            rsa.ImportRSAPrivateKey(Convert.FromBase64String(privateKey), out _);
            byte[] encryptedBytes = Convert.FromBase64String(cipherText);
            byte[] decryptedBytes = rsa.Decrypt(encryptedBytes, RSAEncryptionPadding.OaepSHA256);
            return Encoding.UTF8.GetString(decryptedBytes);
        }
    }
}
```

在上面的程式碼中，將會建立一個 [RsaTool] 這個類別，這個類別中，將會包含了一些 RSA 加解密的操作，這裡將會設計了這些方法：[GenerateKeys]、[GetPublicKey]、[GetPrivateKey]、[Encrypt]、[Decrypt]，透過這些方法，可以讓我們進行 RSA 加解密的操作。

對於 [GenerateKeys] 方法，這個方法將會用來產生 RSA 的公開金鑰與私密金鑰，這裡設定了金鑰的長度為 2048 位元，這樣的金鑰長度，可以讓我們進行安全的加解密操作。




