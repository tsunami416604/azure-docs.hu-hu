<properties
    pageTitle="修正暫時性連接中斷的動作 |Microsoft Azure"
    description="與 Azure SQL Database 互動時排解、診斷和防止連接錯誤和其他暫時性錯誤的動作。"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="msmets"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/10/2015"
    ms.author="daleche"/>


# 排解 SQL database 的暫時性錯誤和連接錯誤


本主題描述如何防止、排解、診斷和減少您的用戶端程式在與 Azure SQL Database 互動時發生的連接錯誤和暫時性錯誤。


<a id="i-transient-faults" name="i-transient-faults"></a>

## 暫時性錯誤


暫時性錯誤是根本原因很快就自行解決的錯誤。 當 Azure 系統快速地將硬體資源轉移到負載平衡更好的各種工作負載時，偶爾會發生暫時性錯誤。 在此重新設定時間範圍期間，與 Azure SQL Database的連接可能會失去。


如果用戶端程式使用 ADO.NET，您的程式接獲指示有關暫時性錯誤所擲回 **SqlException**。  **數目** 屬性可以針對主題頂端附近的暫時性錯誤的清單進行比較 ︰
[SQL 資料庫用戶端程式的錯誤訊息](sql-database-develop-error-messages.md)。


### 連接與命令


嘗試連接期間發生暫時性錯誤時，應該延遲數秒重試連接。


執行 SQL 查詢命令期間發生暫時性錯誤時，不應該立即重試命令。 而是在延遲之後，應該建立全新的連接。 然後，可以重試命令。


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## 暫時性錯誤的重試邏輯


偶爾遇到暫時性錯誤的用戶端程式，當它們包含重試邏輯時更穩健。


當您的程式透過協力廠商中介軟體與 Azure SQL Database 進行通訊時，請洽詢廠商中介軟體是否包含暫時性錯誤的重試邏輯。


### 重試原則


- 如果錯誤是暫時性錯誤，則應該重新嘗試開啟連接。


- 不應直接重試由於暫時性錯誤而失敗的 SQL SELECT 陳述式。
 - 而是建立全新的連線，然後重試 SELECT。


- SQL UPDATE 陳述式由於暫時性錯誤而失敗時，應該先建立全新的連接，再重試 UPDATE。
 - 重試邏輯必須確保整個資料庫交易完成，或整個交易已復原。


#### 其他重試考量


- 下班後自動啟動的批次程式，以及將在早上前完成的批次程式，可以進行長時間間隔的重試。


- 使用者介面程式應該說明了人類將在長時間等候後放棄的傾向。
 - 不過，方案不得每隔幾秒鐘重試，因為該原則可能讓系統充斥要求。


### 增加重試之間的間隔



我們建議您在您第一次重試前延遲 5 秒鐘。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 對於後續每次重試，延遲應以指數方式成長，最大值為 60 秒。

討論 *封鎖期間* 使用 ADO.NET 的用戶端會提供 [SQL Server 連接共用 (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)。

您也可能想要設定程式在自行終止之前的重試次數上限。


### 具有重試邏輯的程式碼範例


各種程式設計語言中具有重試邏輯的程式碼範例位於：

- [快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

## 測試您的重試邏輯


若要測試您的重試邏輯，您必須在程式仍在執行時模擬或產生可以更正的錯誤。


### 中斷與網路連接以進行測試


您可以測試重試邏輯的方法，就是在程式執行時中斷用戶端電腦與網路的連接。 錯誤為：
- **SqlException.Number** = 11001
- 訊息：「未知的主機」


第一次重試時，您的程式可以更正拼字錯誤，然後嘗試連接。


若要使這個動作可行，請從網路拔除電腦，再啟動您的程式。 然後，您的程式會辨識執行階段參數讓程式：
1. 暫時將 11001 加入至其錯誤清單，視為暫時性。
2. 如往常般嘗試其第一個連接。
3. 在攔截到錯誤之後，請從清單中移除 11001。
4. 顯示訊息告訴使用者將電腦連線到網路。
 - 暫停使用進一步執行 **Console.ReadLine** 方法或具有 [確定] 按鈕的對話方塊。 將電腦插入網路中之後。使用者按下 Enter 鍵。
5. 重新嘗試連接，預期成功。


### 連接時拼錯資料庫名稱以進行測試


在第一次連接嘗試之前，您的程式可以故意拼錯使用者名稱。 錯誤為：
- **SqlException.Number** = 18456
- 錯誤將為：「使用者 'WRONG_MyUserName' 登入失敗。」


第一次重試時，您的程式可以更正拼字錯誤，然後嘗試連接。


若要使這個動作可行，您的程式可以辨識執行階段參數讓程式：
1. 暫時將 18456 加入至其錯誤清單，視為暫時性。
2. 故意將 'WRONG_' 加入至使用者名稱。
3. 在攔截到錯誤之後，請從清單中移除 18456。
4. 從使用者名稱中移除 'WRONG_'。
5. 重新嘗試連接，預期成功。


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## 連接：連接字串


連接到 Azure SQL Database 所需的連接字串和連接到 Microsoft SQL Server 的字串稍有不同。 您可以將連接字串複製之資料庫的 [Azure 入口網站](http://portal.azure.com/)。


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]



### 進行連線重試的.NET SqlConnection 參數


如果您的用戶端程式連接到 Azure SQL database 使用.NET Framework 類別 **System.Data.SqlClient.SqlConnection**, ，您應該使用.NET 4.5.1 或更新版本，因此您可以利用其連接重試功能。 此功能的詳細資料 [這裡](http://go.microsoft.com/fwlink/?linkid=393996)。


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


當您建立 [連接字串](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) 的您 **SqlConnection** 物件時，您應該調整下列參數的值 ︰

- ConnectRetryCount &nbsp;&nbsp;*(預設值為 0。範圍是 0 到 255)。*
- ConnectRetryInterval &nbsp;&nbsp;*(預設值為 1 秒。範圍是 1 到 60)。*
- Connection Timeout &nbsp;&nbsp;*(預設值為 15 秒。範圍是 0 到 2147483647)*


具體來說，您選擇的值應該會讓下列等式成立：

- Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

例如，如果計數 = 3，且間隔 = 10 秒，則連線時，僅 29 秒的逾時將無法給予系統充足的時間以進行第三次及最後一次的重試：29 < 3 * 10。


#### 連接與命令


 **ConnectRetryCount** 和 **ConnectRetryInterval** 參數可讓您 **SqlConnection** 物件重試連線作業，而不會告訴或困擾您的程式，例如將控制權傳回給您的程式。 可能會在以下情況中發生重試：

- mySqlConnection.Open 方法呼叫
- mySqlConnection.Execute 方法呼叫

有一些微妙的差異。 如果發生暫時性錯誤而您 *查詢* 正在執行，您 **SqlConnection** 物件不會重試連線作業，以及它絕對不會重新查詢。 不過， **SqlConnection** 很快會檢查傳送您的查詢執行之前的連線。 如果快速檢查偵測到連線問題， **SqlConnection** 重試連線作業。 如果重試成功，就會傳送您的查詢以供執行。


#### 是否應該將 ConnectRetryCount 與應用程式重試邏輯結合？

假設您的應用程式有健全的自訂重試邏輯。 它可能會重試連接作業 4 次。 如果您將加入 **ConnectRetryInterval** 和 **ConnectRetryCount** = 3 加入連接字串，您會增加為 4 * 3 = 12 的重試次數的重試次數。 您可能不會想要這麼多的重試次數。



<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

## 連接：IP 位址


您必須設定 SQL Database 伺服器，以接受來自裝載您的用戶端程式之電腦的通訊。 您可以編輯防火牆設定，透過 [Azure 入口網站](http://portal.azure.com/)。


如果您忘了設定 IP 位址，您的程式將會失敗並出現一個好用的錯誤訊息，陳述必要的 IP 位址。


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


如需詳細資訊，請參閱：
[如何：在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

## 連接：連接埠


通常，您只需要在裝載用戶端程式的電腦上確定已開啟連接埠 1433 進行輸出通訊。


例如，當用戶端程式裝載在 Windows 電腦上時，主機上的 Windows 防火牆可讓您開啟通訊埠 1433：


1. 開啟 [控制台]
2. &gt; 所有控制台項目
3. &gt; Windows 防火牆
4. &gt; 進階設定
5. &gt; 輸出規則
6. &gt; 動作
7. &gt; 新增規則


如果用戶端程式裝載於 Azure 的虛擬機器 (VM)，您應該閱讀 ︰<br/>[針對 ADO.NET 4.5 及 SQL 1433 以外的連接埠資料庫 V12](sql-database-develop-direct-route-ports-adonet-v12.md)。


如需 cofiguration 背景資訊的連接埠和 IP 位址，請參閱 ︰
[Azure SQL Database 防火牆](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

## 連接：ADO.NET 4.5


如果您的程式使用 ADO.NET 類別，例如 **System.Data.SqlClient.SqlConnection** 若要連接到 Azure SQL Database，我們建議您使用.NET Framework 4.5 或更新版本。


ADO.NET 4.5:
- 新增支援 TDS 7.4 通訊協定。 這包括以外 4.0 中的連線增強功能。
- 支援連接共用。 這包括提供給您的程式的連接物件是否運作的有效驗證。


當您從連接集區使用連接物件時，建議您的程式若未立即使用連接，則暫時關閉它。 重新開啟連接比建立新的連接更便宜。


如果您使用 ADO.NET 4.0 或更舊版本，建議您升級到最新的 ADO.NET。
- 截至 2015 年，您可以 [下載 ADO.NET 4.6](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)。


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## 診斷：測試公用程式是否可以連接


如果您的程式無法連接到 Azure SQL Database，有一個診斷選項將嘗試與公用程式連接。 理想的情況下，此公用程式會使用您的程式使用的同一程式庫來連接。


您可以在任何 Windows 電腦上，嘗試這些公用程式：
- SQL Server Management Studio (SSMS.exe)，連接時使用 ADO.NET。
- sqlcmd.exe，會使用連接 [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)。


一旦完成連接，就會測試簡短的 SQL SELECT 查詢是否可以運作。


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

## 診斷：檢查開啟的連接埠


假設您懷疑連接嘗試由於連接埠問題而失敗。 在您的電腦上，您可以執行報告連接埠組態的公用程式。


下列公用程式在 Linux 上可能很有用：
- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (將範例值變更為您的 IP 位址)。


在 Windows 上 [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) 公用程式可能會很有幫助。 以下是在 Azure SQL Database 伺服器上查詢連接埠情況，以及在膝上型電腦上執行的的範例執行：


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

## 診斷：記錄您的錯誤


有時診斷間歇問題的最好方式，就是數天或數週偵測一般模式。


您的用戶端可以記錄其遇到的所有錯誤來協助診斷。 您可以使記錄項目與 Azure SQL Database 本身內部記錄的錯誤資料相互關聯。


Enterprise Library 6 (EntLib60) 提供 .NET Managed 類別來協助記錄：
- [5 - 輕而易舉：使用記錄應用程式區塊](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

## 診斷：檢查系統記錄找出錯誤


以下是一些查詢錯誤記錄和其他資訊的 Transact-SQL SELECT 陳述式。


| 記錄查詢 | 說明 |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>（& s) nbsp; & nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>（& s) nbsp; & nbsp;`e.event_type, e.end_time;` |  [Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) 檢視提供個別的事件，包括某些可能會導致暫時性錯誤或連線失敗的相關資訊。<br/><br/>在理想情況下您可以相互關聯 **start_time** 或 **end_time** 值，當用戶端程式發生問題的相關資訊。<br/><br/>**提示 ︰** 您必須連接到 **主要** 資料庫來執行此程序。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>（& s) nbsp; & nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |  [Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) 檢視提供的其他診斷的事件類型的彙總的計算。<br/><br/>**提示 ︰** 您必須連接到 **主要** 資料庫來執行此程序。 |


### 診斷：在 SQL Database 記錄中搜尋問題事件


您可以在 Azure SQL Database 的記錄中搜尋有關問題事件的項目。 請嘗試下列的 TRANSACT-SQL SELECT 陳述式中 **主要** 資料庫 ︰


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### 數個從 sys.fn_xe_telemetry_blob_target_read_file 傳回的資料列


接下來是傳回的資料列可能的樣子。 顯示的 null 值通常在其他資料列不是 null。


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## Enterprise Library 6


Enterprise Library 6 (EntLib60) 是 .NET 類別的架構，可協助您實作雲端服務的健全用戶端，其中之一就是 Azure SQL Database 服務。 您可以找出 EntLib60 可以協助的每個領域的專用主題，請先造訪：
- [Enterprise Library 6 – 2013 年 4 月](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


處理暫時性失敗重試邏輯是一個區域 EntLib60 可以幫助 ︰
- [4 - 堅持是所有成功的秘方：使用暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


一個在重試邏輯中使用 EntLib60 的簡短 C# 程式碼範例位於：
- [程式碼範例：C# 中用於連接到 SQL Database 的 Enterprise Library 6 重試邏輯](sql-database-develop-entlib-csharp-retry-windows.md)


> [AZURE.NOTE] EntLib60 的原始程式碼可供大眾 [下載](http://go.microsoft.com/fwlink/p/?LinkID=290898)。 Microsoft 不打算對 EntLib 做進一步的功能或維護更新。


### 用於暫時性錯誤和重試的 EntLib60 類別


下列 EntLib60 類別特別有助於重試邏輯。 所有這些，或在命名空間之下更進一步 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*命名空間中 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

- **RetryPolicy** 類別
 - **ExecuteAction** 方法


- **ExponentialBackoff** 類別


- **SqlDatabaseTransientErrorDetectionStrategy** 類別


- **ReliableSqlConnection** 類別
 - **ExecuteCommand** 方法


命名空間中 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** 類別

- **NeverTransientErrorDetectionStrategy** 類別


以下是 EntLib60 相關資訊的連結：

- 免費 [通訊錄的下載 ︰ Microsoft 企業程式庫，第 2 版的開發人員指南](http://www.microsoft.com/download/details.aspx?id=41145)

- 最佳作法 ︰ [重試一般指引](best-practices-retry-general.md) 已重試邏輯的深入討論。

- NuGet 下載 [Enterprise Library-暫時性錯誤處理應用程式區塊 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)


### EntLib60：記錄區塊


- 記錄區塊是高度彈性且可設定的解決方案，可讓您：
 - 建立記錄訊息，並儲存在各種不同的位置中。
 - 分類與篩選訊息。
 - 收集有助於偵錯和追蹤的內容資訊，以及用於稽核和一般記錄需求的內容資訊。


- 記錄區塊可彙總來自記錄目的地的記錄功能，使應用程式程式碼能夠一致，而不必理會目標記錄存放區的的位置和類型。


如需詳細資料，請參閱：
[5 - 輕而易舉：使用記錄應用程式區塊](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)


### EntLib60 IsTransient 方法的原始程式碼


接下來，從 **SqlDatabaseTransientErrorDetectionStrategy** 類別中，是 C# 原始程式碼 **IsTransient** 方法。 原始程式碼將釐清哪些錯誤會被視為暫時性並值得重試 (從 2013 年 4 月起)。

許多 **//comment** 行已從這個複本以強調可讀性。


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## 詳細資訊


- [SQL Server 連接集區 (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*重試* 是一般用途的程式庫，以重試一次的 Apache 2.0 授權 **Python**, ，以簡化工作將重試行為加入至幾乎任何項目。](https://pypi.python.org/pypi/retrying)


