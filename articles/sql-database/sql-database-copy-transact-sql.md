<properties 
    pageTitle="使用 Transact-SQL 建立 Azure SQL Database 的複本" 
    description="使用 Transact-SQL 建立 Azure SQL Database 的複本" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="12/01/2015"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# 使用 Transact-SQL 建立 Azure SQL Database 的複本

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



下列步驟說明如何利用 Transact-SQL 複製 SQL Database。 資料庫複製作業會將 SQL 資料庫複製到新的資料庫使用 [CREATE DATABASE]() 陳述式。 副本是您在同一部伺服器或不同伺服器上建立的資料庫快照備份。


> [AZURE.NOTE] Azure SQL Database 會自動建立，並維護每個使用者資料庫，您可以還原的備份。 如需詳細資訊，請參閱 [業務持續性概觀](sql-database-business-continuity.md)。


複製程序完成時，新的資料庫是功能完整的資料庫，獨立於來源資料庫。 複製完成時，新資料庫與來源資料庫在交易上一致。 資料庫副本與來源資料庫的服務層和效能層級 (定價層) 相同。 複製完成之後，副本會變成功能完整的獨立資料庫。 可以個別管理登入、使用者和權限。


當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。


若要完成這篇文章中的步驟，您需要下列項目︰

- Azure 訂閱。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- Azure SQL Database。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟 ︰ [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx)。 如果您沒有 SSMS，或如果無法使用，這篇文章中所述的功能 [下載最新版本](https://msdn.microsoft.com/library/mt238290.aspx)。




## 複製您的 SQL Database

使用伺服器層級主體登入或建立您要複製之資料庫的登入來登入 master 資料庫。 不是伺服器層級主體的登入必須是 dbmanager 角色的成員才能複製資料庫。 如需有關登入和連接到伺服器的詳細資訊，請分別參閱〈在 Azure SQL Database 中管理資料庫、登入和使用者〉和〈Azure SQL Database 開發：作法主題〉。

使用 CREATE DATABASE 陳述式，開始複製來源資料庫。 執行此陳述式會起始資料庫複製程序。 因為複製資料庫是非同步程序，所以 CREATE DATABASE 陳述式會在資料庫完成複製之前傳回。


### 將 SQL Database 複製到相同伺服器

使用伺服器層級主體登入或建立您要複製之資料庫的登入來登入 master 資料庫。 不是伺服器層級主體的登入必須是 dbmanager 角色的成員才能複製資料庫。

此命令會將 Database1 複製到相同伺服器上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### 將 SQL Database 複製到不同伺服器

登入目的地伺服器的 master 資料庫，也就是即將建立新資料庫的 Azure SQL Database 伺服器。 使用具有與來源 Azure SQL Database 伺服器上來源資料庫的資料庫擁有者 (DBO) 相同之名稱和密碼的登入。 目的地伺服器上的登入必須也是 dbmanager 角色的成員或是伺服器層級主體登入。

此命令會將 server1 上的 Database1 複製到 server2 上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## 監視複製作業的進度

藉由查詢 sys.databases 和 sys.dm_database_copies 檢視來監視複製程序。 當複製正在進行時，新資料庫的 sys.databases 檢視的 state_desc 資料行會設定為 COPYING。


- 如果複製失敗，新資料庫的 sys.databases 檢視的 state_desc 資料行會設定為 SUSPECT。 在此情況下，在新的資料庫上執行 DROP 陳述式，稍後再試一次。
- 如果複製成功，新資料庫的 sys.databases 檢視的 state_desc 資料行會設定為 ONLINE。 在此情況下，複製已完成且新資料庫是一般資料庫，能夠與來源資料庫分開進行變更。



## 後續步驟


- 如果您決定取消複製正在進行時，執行 [卸除資料庫](https://msdn.microsoft.com/library/ms178613.aspx) 新的資料庫上的陳述式。 或者，在來源資料庫上執行 DROP DATABASE 陳述式也會取消複製程序。
- 線上目的地伺服器上新的資料庫之後，請使用 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 陳述式，以從新的資料庫登入目的地伺服器上的使用者重新對應。 新資料庫中的所有使用者都保有其在來源資料庫中原有的權限。 起始資料庫複製的使用者會變成新資料庫的資料庫擁有者，並且被指派新的安全性識別碼 (SID)。 在複製成功之後，重新對應其他使用者之前，只有起始複製的登入 (也就是資料庫擁有者 (DBO)) 可以登入新的資料庫。




## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


