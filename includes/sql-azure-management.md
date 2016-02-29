
# 使用 SQL Server Management Studio 管理 Azure SQL Database 

您可以使用 SQL Server Management Studio (SSMS) 來管理 Azure SQL Database 邏輯伺服器與資料庫。 本主題會引導您使用 SSMS 的一般工作。 在您開始之前，您應該已經有 Azure SQL Database 中建立的邏輯伺服器與資料庫。 若要開始，讀取 [建立您的第一個 Azure SQL Database](sql-database-get-started.md) 然後再回來。

每當您使用 Azure SQL Database 時，建議您使用最新版本的 SSMS。 請瀏覽 [下載 SQL Server Management Studio](https://msdn.microsoft.com/en-us/library/mt238290.aspx) 來取得它。 


## 連線到 SQL Database 邏輯伺服器

若要連線到 SQL Database，您需要知道 Azure 上的伺服器名稱。 您可能需要登入入口網站才能取得此資訊。

1.  登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.  在左窗格中，按一下 [ **SQL 資料庫**。

3.  在 SQL Databases 首頁上，按一下 [ **伺服器** 來列出所有與您的訂閱相關聯的伺服器] 頁面的頂端。 尋找想要連線的伺服器名稱，並將它複製到剪貼簿上。

    接著，設定您的 SQL Database 防火牆
允許來自本機電腦的連線。 若要這麼做，請將本機電腦 IP 位址加入防火牆例外清單。

1.  在 SQL Databases 首頁上，按一下 [ **伺服器** ，然後按一下您要連接的伺服器。

2.  按一下 [ **設定** 頁面的頂端。

3.  複製 [目前用戶端 IP 位址] 中的 IP 位址。

4.  在 [設定] 頁面中， **允許的 IP 位址** 包括三個方塊，讓您可以指定規則名稱與 IP 位址範圍作為起始和結束值。 在規則名稱中，您可以輸入您的電腦名稱。 在起始範圍和結束範圍中，將您的電腦 IP 位址貼入這兩個方塊，然後按一下出現的核取方塊。

    規則名稱必須是唯一的。 如果這是您的開發電腦，您可以在 IP 範圍起始方塊和 IP 範圍結束方塊中輸入 IP 位址。 否則，您可能需要輸入較廣泛的 IP 位址範圍，以容納來自組織中其他電腦的連線。
 
5. 按一下 [ **儲存** 頁面的底部。

    **注意:** 最長可能延遲五分鐘的變更
    防火牆設定的變更才會生效。

    您現在可以使用 Management Studio 來連線到 SQL Database。

1.  在工作列上，按一下 [ **啟動**, ，指向 [ **所有程式**, ，指向
    **Microsoft SQL Server 2014**, ，然後按一下 [ **SQL Server
    Management Studio**。

2.  在 **連接到伺服器**, ，指定完整
    伺服器名稱做為 *serverName*。.database.windows.net。 在 Azure 上，伺服器名稱是由英數字元組成的自動產生字串。

3.  選取 **SQL Server 驗證**。

4.  在 **登入** 方塊中，輸入 SQL Server 系統管理員登入，您
    當您建立伺服器時，入口網站中指定。

5.  在 **密碼** 方塊中，輸入您在中指定的密碼
    入口網站建立伺服器時。

8.  按一下 [ **連接** 來建立連接。

具備最新更新的 SQL Server 2014 SSMS 提供下列工作的擴大支援：
建立和修改 Azure SQL Database。 此外，您也可以使用
Transact-SQL 陳述式來完成這些工作。 下列步驟
提供這些陳述式的範例。 如需有關使用
Transact-SQL 與 SQL Database 的詳細資訊 (包括所支援命令的詳細資料)，
支援，請參閱 [TRANSACT-SQL 參考 (SQL Database)](http://msdn.microsoft.com/library/bb510741.aspx)。

## 建立和管理 Azure SQL 資料庫

連線到時 **主要** 資料庫中，您可以建立新的
資料庫，並修改或卸除現有資料庫。 以下步驟
說明如何透過 Management Studio 完成幾個常見的資料庫管理
工作。 若要執行這些工作，請確定使用您在設定伺服器時所建立的伺服器層級主體登入，連線到
**主要** 您資料庫的伺服器層級主體登入
所建立。

若要在 Management Studio 中開啟查詢視窗，開啟 [資料庫] 資料夾中，展開 **系統資料庫** 資料夾中，以滑鼠右鍵按一下 **主要**, ，然後按一下 [ **新查詢**。

-   使用 **CREATE DATABASE** 陳述式來建立新的資料庫。 如需
    詳細資訊，請參閱 [CREATE DATABASE (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)。 以下陳述式會建立名為的新資料庫 **myTestDB** ，並指定它是預設大小上限為 250 GB 的標準 S0 Edition 資料庫。

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

按一下 [ **Execute** 來執行查詢。

-   使用 **ALTER DATABASE** 陳述式來修改現有的資料庫，
    例如，如果您要變更資料庫的名稱和版本
    。 如需詳細資訊，請參閱 [ALTER DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms174269.aspx)。 Auch die Eigenschaften
    以下陳述式修改您在先前步驟中建立的資料庫
    將版本變更為標準 S1。

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   使用 **卸除資料庫** 陳述式來刪除現有的資料庫。
    如需詳細資訊，請參閱 [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx)。 刪除以下陳述式 **myTestDB** 資料庫，但不會卸除它現在因為您將使用下一個步驟中建立登入。

        DROP DATABASE myTestBase;

-   Master 資料庫具有 **sys.databases** 可讓您檢視
    來檢視所有資料庫的詳細資料。 若要檢視所有現有資料庫，
    請執行下列陳述式：

        SELECT * FROM sys.databases;

-   在 SQL 資料庫中， **使用** 陳述式不支援的切換
    資料庫之間的切換。 相反地，您需要建立
    直接與目標資料庫的連線。

>[AZURE.NOTE] 許多可建立或修改資料庫的 TRANSACT-SQL 陳述式必須在自己的批次中執行，且無法與其他 TRANSACT-SQL 陳述式。 如需詳細資訊，請參閱陳述式特定資訊 (可透過上面列出的連結取得)。

## 建立和管理登入

 **主要** 登入，以及哪些登入具有追蹤的資料庫
建立資料庫或其他登入的權限。 管理登入的方式是
連接到 **主要** 資料庫與伺服器層級主體
而伺服器層級主體登入是您在設定伺服器時所建立。 您可以使用
**建立登入**, ，**ALTER LOGIN**, ，或 **卸除登入** 陳述式
針對 master 資料庫執行查詢，而 master 資料庫將管理
整個伺服器的登入。 如需詳細資訊，請參閱 [管理資料庫和 SQL 資料庫中的登入](http://msdn.microsoft.com/library/azure/ee336235.aspx)。 


-   使用 **建立登入** 陳述式來建立新的伺服器層級
    登入。 如需詳細資訊，請參閱 [CREATE LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189751.aspx)。 以下陳述式會建立
    呼叫 **login1**。 取代 **password1** 的密碼與您
    選擇的密碼。

        CREATE LOGIN login1 WITH password='password1';

-   使用 **CREATE USER** 陳述式來授與資料庫層級
    權限。 所有登入也必須在建立 **主要** 資料庫，
    但是針對連線到不同資料庫的登入，您
    必須授與它使用的資料庫層級權限 **建立使用者**
    陳述式。 如需詳細資訊，請參閱 [CREATE USER (SQL Database)](https://msdn.microsoft.com/library/ms173463.aspx)。 

-   若要將
    資料庫的權限呼叫 **myTestDB**, ，完成下列步驟
    步驟：

 1.  若要重新整理物件總管] 中，檢視 **myTestDB** 您剛剛建立的資料庫，以滑鼠右鍵按一下 [物件總管] 中的伺服器名稱，然後按一下 **重新整理**。  

     如果連線已關閉，您可以重新連線選取 **連接物件總管** 檔案] 功能表上。

 2. 以滑鼠右鍵按一下 **myTestDB** 資料庫，然後選取 **新查詢**。

    3.  針對 myTestDB 資料庫執行下列陳述式，以
        建立名為資料庫使用者 **login1User** 對應至
        伺服器層級登入 **login1**。

            CREATE USER login1User FROM LOGIN login1;

-   使用 **sp\_addrolemember** 預存程序來授與使用者
    將資料庫的適當權限等級授與使用者帳戶。 如需
    詳細資訊，請參閱 [sp_addrolemember (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms187750.aspx)。 以下陳述式 **login1User**
    加入資料庫的唯讀權限 **login1User** 至
     **db\_datareader** 角色。

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   使用 **ALTER LOGIN** 陳述式來修改現有的登入，
    例如，如果您要變更登入的密碼。 如需
    詳細資訊，請參閱 [ALTER LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189828.aspx)。  **ALTER LOGIN** 陳述式應該針對執行 **主要** 資料庫。 切換回連線到該資料庫的查詢視窗。 

    以下陳述式修改 **login1** 重設密碼的登入。
    取代 **newPassword** 以您選擇的密碼，
    **oldPassword** 使用目前登入密碼。

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   使用 **卸除登入** 陳述式來刪除現有的登入。
    刪除伺服器層級的登入也會同時刪除任何相關
    資料庫使用者帳戶。 如需詳細資訊，
    請參閱 [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx)。  **卸除登入**
    陳述式應該針對執行 **主要** 資料庫。 Auch die Eigenschaften
    刪除以下陳述式 **login1** 登入。

        DROP LOGIN login1;

-   Master 資料庫具有 **sql\_logins** ，您可以檢視
    來檢視登入。 若要檢視所有現有的登入，請執行
    下列陳述式：

        SELECT * FROM sys.sql_logins;

## 使用動態管理檢視監視 SQL Database</h2>

SQL Database 支援數個動態管理檢視，而您可以使用這些檢視
來監視個別資料庫。 以下幾個範例說明
您可以透過這些檢視擷取的監視器資料類型。 如需
完整的詳細資訊和詳細的使用方式範例，請參閱 [使用動態管理檢視監視 SQL Database](https://msdn.microsoft.com/library/azure/ff394114.aspx)。

-   查詢動態管理檢視需要 **檢視資料庫狀態**
    權限。 若要授與 **VIEW DATABASE STATE** 權限
    特定資料庫使用者，請連線到您想要使用伺服器層級主體登入所管理的資料庫，
    並針對資料庫執行下列
    陳述式：

        GRANT VIEW DATABASE STATE TO login1User;

-   計算資料庫大小使用 **sys.dm\_db\_partition\_stats**
    檢視呈現。  **Sys.dm\_db\_partition\_stats** 檢視傳回的頁面和
    資料列計數資訊：資料庫中的每個資料分割，而您
    可以使用這些資訊來計算資料庫大小。 下列查詢會傳回
    資料庫的大小 (MB)：

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   使用 **sys.dm\_exec\_connections** 和 **sys.dm\_exec\_sessions**
    檢視，以擷取下列項目的相關資訊：目前使用者連線以及
    與資料庫相關聯的內部工作。 下列查詢
    會傳回目前連線的相關資訊：

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   使用 **sys.dm\_exec\_query\_stats** 來擷取彙總檢視
    已快取查詢計畫的效能統計資料。 下列查詢
    會傳回依下列項目排名之前五項查詢的相關資訊：平均 CPU
    時間。

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

