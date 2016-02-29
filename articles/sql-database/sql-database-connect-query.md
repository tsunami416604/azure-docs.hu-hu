<properties
    pageTitle="透過 C# 查詢連接到 SQL Database | Microsoft Azure"
    description="以 C# 撰寫程式以查詢和連接到 SQL Database。 有關 IP 位址、連接字串、安全登入和免費 Visual Studio 的資訊。"
    services="sql-database"
    keywords="c# database query, c# query, connect to database"
    documentationCenter=""
    authors="MightyPen"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/09/2015"
    ms.author="genemi"/>


# 以 & #x 23; 撰寫程式以查詢和連接到 SQL Database

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

了解如何以 C# 撰寫程式來查詢和連接到雲端的 Azure SQL Database。

本文會為 Azure SQL Database、C# 和 ADO.NET 的每位新手描述每一個步驟。 對 Microsoft SQL Server 和 C# 有豐富經驗的人可略過一些步驟並著重於專屬於 SQL Database 的步驟。


## 必要條件


若要執行 C# 查詢程式碼範例，您必須具備：


- Azure 帳戶和訂用帳戶。 您可以註冊 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。


-  **AdventureWorksLT** 示範 Azure SQL Database 服務上的資料庫。
 - [建立示範資料庫](sql-database-get-started.md) 以分鐘為單位。


- Visual Studio 2013 Update 4 (或更新版本)。 Microsoft 現在提供 Visual Studio 社群 *免費*。
 - [Visual Studio Community，下載](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [免費 Visual Studio 的更多選項](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - 或者，讓 [步驟](#InstallVSForFree) 稍後在本主題說明如何 [Azure 入口網站](http://portal.azure.com/) 引導您的 Visual Studio 安裝。


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 步驟 1：免費安裝 Visual Studio Community


如果您需要安裝 Visual Studio，您可以：

- 將瀏覽器瀏覽至提供免費下載及其他選項的 Visual Studio 網頁以免費安裝 Visual Studio Community。或
- 讓 [Azure 入口網站](http://portal.azure.com/) 一節所說明的下載網頁來引導您。


### 透過 Azure 入口網站的 Visual Studio


1. 透過登入 [Azure 入口網站](http://portal.azure.com/), ，http://portal.azure.com/。

2. 按一下 [ **瀏覽* 所有** > **SQL 資料庫**。 刀鋒視窗會開啟該資料庫的搜尋。

3. 在頂端附近的篩選器] 文字方塊中，啟動 [鍵入的名稱，以您 **AdventureWorksLT** 資料庫。

4. 當您看到伺服器上資料庫的資料列時，按一下資料列。 刀鋒視窗會為您的資料庫開啟。

5. 為了方便起見，請按一下前述每個刀鋒視窗上的最小化控制項。

6. 按一下 [ **Visual Studio 中開啟** 在資料庫刀鋒視窗頂端附近的按鈕。 關於 Visual Studio 的新刀鋒視窗會利用連結開啟以安裝 Visual Studio 的位置。

    ![[在 Visual Studio 中開啟] 按鈕][20-OpenInVisualStudioButton]

7. 按一下 [ **社群 (免費)** 連結或類似的連結。 新的網頁會隨即新增。

8. 使用新網頁上的連結安裝 Visual Studio。

9. 安裝的 Visual Studio，在之後 **開啟在 Visual Studio 中** 刀鋒視窗中按一下 **在 Visual Studio 中開啟** ] 按鈕。 Visual Studio 隨即開啟。

10. 權益其 **SQL Server 物件總管** ] 窗格中，Visual Studio 要求您填寫對話方塊中的連接字串欄位。
 - 選擇 **SQL Server 驗證**, ，而非 **Windows 驗證**。
 - 記得要指定您 **AdventureWorksLT** 資料庫 (**選項** > **連接屬性** 在對話方塊中)。

11. 在 **SQL Server 物件總管**, ，展開 [資料庫] 節點。


## 步驟 2：在 Visual Studio 中建立新專案


在 Visual Studio 中建立新的專案為基礎的 C# 的入門範本 > Windows > **主控台應用程式**。


1. 按一下 [ **檔案** > **新** > **專案**。 **** 對話方塊隨即顯示。

2. 在 **已安裝**, ，C# 和 Windows 中，展開以便 **主控台應用程式** 會出現在中間窗格中的選項。

    ![[新增專案] 對話方塊][30-VSNewProject]

2. 如 **名稱** 輸入 **ConnectAndQuery_Example**。 按一下 [ **確定**。


## 步驟 3：為 config 處理新增組件參考


我們的 C# 範例會使用.NET Framework 組件 **System.Configuration.dll**, ，因此讓我們加入的參考。


1. 在 **方案總管] 中** ] 窗格中，以滑鼠右鍵按一下 **參考** > **加入參考**。  **參考管理員** ] 視窗隨即顯示。

2. 展開 **組件** > **Framework**。

3. 捲動，然後按一下以反白顯示 **System.Configuration**。 確定是否選取其核取方塊。

4. 按一下 [ **確定**。

5. 編譯您的程式功能表 **建置** > **建置方案**。


## 步驟 4：取得連接字串


使用 [Azure 入口網站](http://portal.azure.com/) 複製連接到 SQL 資料庫所需的連接字串。

是您第一次使用 Visual Studio 連接至您的 Azure SQL Database **AdventureWorksLT** 資料庫。


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## 步驟 5：將連接字串新增至 App.config 檔案


1. 在 Visual Studio 中，從 [方案總管] 窗格開啟 App.config 檔案。

2. 新增 **& #x3c; 組態 #x3e; & #x3c; / 組態與 #x3e;** 項目，如下列範例 App.config 程式碼範例所示。
 - 取代 *{your_placeholders}* 以您的實際值:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup>
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
        </startup>

        <connectionStrings>
            <clear />
            <add name="ConnectionString4NoUserIDNoPassword"
            connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
            />
        </connectionStrings>
    </configuration>
```

3. 儲存 App.config 變更。

4. 在 [方案總管] 窗格中，以滑鼠右鍵按一下 **App.config** 節點，然後再按一下 **屬性**。

5. 設定 **複製到輸出目錄** 至 **永遠複製**。
 - 這會使 App.config 檔案的內容取代 &#x2a;.exe.config 檔案的內容，其位於 &#x2a;.exe 檔案建置所在的目錄中。 每次您重新編譯 &#x2a;.exe 時都會出現取代情形。
 - &#x2a;.exe.config 檔案會在我們的範例 C# 程式執行時讀取。

    ![複製到輸出目錄 = 永遠複製][50-VSCopyToOutputDirectoryProperty]


## 步驟 6：在範例 C# 程式碼中貼上


1. 在 Visual Studio 中，使用 **方案總管] 中** 窗格，即可開啟您 **Program.cs** 檔案。

    ![在範例 C# 查詢程式碼中貼上。][40-VSProgramCsOverlay]

2. 在下列範例 C# 程式碼中貼上以覆寫 Program.cs 中的所有起始程式碼。
 - 如果您想要較短的程式碼範例，您可以指派將整個連接字串為常值給變數 **SQLConnectionString**。 然後您可以清除這兩個方法 **GetConnectionStringFromExeConfig** 和 **GatherPasswordFromConsole**。


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
    class Program
    {
        static void Main()
        {
            string connectionString4NoUserIDNoPassword,
                password, userName, SQLConnectionString;

            // Get most of the connection string from ConnectAndQuery_Example.exe.config
            // file, in the same directory where ConnectAndQuery_Example.exe resides.
            connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
                ("ConnectionString4NoUserIDNoPassword");
            // Get the user name from keyboard input.
            Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
            userName = Console.ReadLine();
            // Get the password from keyboard input.
            password = Program.GatherPasswordFromConsole();

            SQLConnectionString = "Password=" + password + ';' +
                "User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

            // Create an SqlConnection from the provided connection string.
            using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
            {
                // Formulate the command.
                C.SqlCommand command = new C.SqlCommand();
                command.Connection = connection;

                // Specify the query to be executed.
                command.CommandType = D.CommandType.Text;
                command.CommandText = @"
                    SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
                    FROM SalesLT.Customer;  -- In AdventureWorksLT database.
                    ";
                // Open a connection to database.
                connection.Open();

                // Read data returned for the query.
                C.SqlDataReader reader = command.ExecuteReader();
                while (reader.Read())
                {
                    Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
                        reader[0], reader[1], reader[2], reader[3], reader[4]);
                }
            }
            Console.WriteLine("View the results here, then press any key to finish...");
            Console.ReadKey(true);
        }
        //----------------------------------------------------------------------------------

        static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
        {
            G.ConnectionStringSettings connectionStringSettings =
                G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

            if (connectionStringSettings == null)
            {
                throw new ApplicationException(String.Format
                    ("Error. Connection string not found for name '{0}'.",
                    connectionStringNameInConfig));
            }
                return connectionStringSettings.ConnectionString;
        }

        static string GatherPasswordFromConsole()
        {
            T.StringBuilder passwordBuilder = new T.StringBuilder(32);
            ConsoleKeyInfo key;
            Console.WriteLine("Enter your password: ");
            do
            {
                key = Console.ReadKey(true);
                if (key.Key != ConsoleKey.Backspace)
                {
                    passwordBuilder.Append(key.KeyChar);
                    Console.Write("*");
                }
                else  // Backspace char was entered.
                {
                    // Retreat the cursor, overlay '*' with ' ', retreat again.
                    Console.Write("\b \b");
                    passwordBuilder.Length = passwordBuilder.Length - 1;
                }
            }
            while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
            Console.WriteLine(Environment.NewLine);
            return passwordBuilder.ToString();
        }
    }
}
```


### 編譯您的程式


1. 在 Visual Studio 中，請依序按一下功能表編譯您的程式 **建置** > **建置方案**。


### 範例程式中的動作摘要


1. 從組態檔讀取大多數的 SQL 連接字串。

2. 從鍵盤收集使用者名稱和密碼，並將其新增以完成連接字串。

3. 用來連線到連接字串和 ADO.NET 類別 **AdventureWorksLT** 示範 SQL 資料庫上的資料庫。

4. 發出 SQL **選取** 讀取 **SalesLT** 資料表。

5. 將傳回的資料列列印到主控台。


我們試著讓 C# 範例保持簡短。 但是我們加入可讀取組態檔的程式碼以滿足像您這樣的客戶所提出的數個要求。 我們同意生產品質程式應該使用組態檔而不是 .exe 中的常值硬式編碼。


> [AZURE.WARNING] 適用在程式碼保持簡潔，我們選擇不包含例外狀況處理程式碼，然後重試邏輯，在此範例中教育。 不過，與雲端資料庫互動的生產程式應該包含兩者。
>
> [這裡](sql-database-develop-csharp-retry-windows.md) 是具有重試邏輯的程式碼範例的連結。


## 步驟 7：加入伺服器防火牆中允許的 IP 位址範圍


在用戶端電腦的 IP 位址加入 SQL Database 防火牆之前，您的用戶端 C# 程式無法連接到 SQL Database。 您的程式將會失敗並出現一個好用的錯誤訊息，陳述必要的 IP 位址。


您可以使用 [Azure 入口網站](http://portal.azure.com/) 新增 IP 位址。



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



如需詳細資訊，請參閱:<br/>
[如何: 在 SQL 資料庫上設定防火牆設定](sql-database-configure-firewall-settings.md)



## 步驟 8：執行程式


1. 在 Visual Studio 中，執行您的 C# 查詢程式功能表 **偵錯** > **開始偵錯**。 主控台視窗隨即顯示。

2. 依照指示輸入您的使用者名稱和密碼。
 - 幾個連接工具需要您的使用者名稱附加 "@{your_serverName_here}"，但這個後置詞對於 ADO.NET 而言是選擇性的。 請務必輸入此後置詞。

3. 資料列會隨即顯示。


## 相關連結


- [SQL Database 的用戶端快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)

- 如果用戶端程式在 Azure VM 上執行，了解在 1433年以外的 TCP 連接埠:<br/>[針對 ADO.NET 4.5 及 SQL Database V12 的 1433年以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

