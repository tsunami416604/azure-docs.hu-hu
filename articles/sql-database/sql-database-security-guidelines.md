<properties
   pageTitle="Azure SQL Database 安全性方針和限制 | Microsoft Azure"
   description="了解與安全性相關的 Microsoft Azure SQL Database 方針和限制。"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/24/2015"
   ms.author="rickbyh"/>


# Azure SQL Database 安全性方針和限制

本主題描述與安全性相關的 Microsoft Azure SQL Database 方針和限制。 管理您的 Azure SQL Database 安全性時，請考慮下列幾點。

## 防火牆

Azure SQL Database 服務唯有透過 TCP 通訊埠 1433 才能使用。 若要從您的電腦存取 SQL Database，請務必確認您的防火牆允許 TCP 連接埠 1433 上的傳出 TCP 通訊。 連接程序當中，從 Azure 虛擬機器的連接會被重新導向到不同的 IP 位址和連接埠，針對每個背景工作角色都是唯一。 連接埠號碼的範圍從 11000 到 11999。

您可以在第一次連接到 Azure SQL Database 伺服器之前，您必須使用 [Azure 傳統入口網站](https://portal.azure.com) 或 [Azure 平台的傳統入口網站](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) 設定 Azure SQL Database 防火牆。 您必須建立伺服器層級的防火牆設定，讓您的電腦或 Azure 可以嘗試連接到 Azure SQL Database 伺服器。 此外，如果您想要控制 Azure SQL Database 伺服器中特定資料庫的存取，請針對個別資料庫建立資料庫層級的防火牆規則。 如需詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。

## 連接加密和憑證驗證

SQL 資料庫和您的應用程式之間的所有通訊隨時都需要加密 (SSL)。 如果用戶端應用程式沒有在連接時驗證憑證，則連接至 SQL Database 時容易受到「攔截式」攻擊。

若要使用應用程式程式碼或工具驗證憑證，請明確地要求加密的連接，並且不要信任伺服器憑證。 即使您的應用程式程式碼或工具未要求加密連接，它們仍然會接收加密的連接。 不過，它們可能不會驗證伺服器憑證，這樣一來很容易受到「攔截式」攻擊。

若要驗證憑證與 ADO.NET 應用程式程式碼，請設定 `Encrypt = True` 和 `TrustServerCertificate = False` 資料庫連接字串中。 如需詳細資訊，請參閱 [程式碼範例: 重試邏輯來連接到 Azure SQL 資料庫與 ADO.NET](https://msdn.microsoft.com/library/azure/ee336243.aspx)。

SQL Server Management Studio 也支援憑證驗證。 在 [連接到伺服器]**** 對話方塊中，按一下 [連接屬性]**** 索引標籤上的 [加密連接]****。
> [AZURE.NOTE] SQL Server Management Studio 不支援連接到 SQL Server 2008 R2 之前版本中的 SQL Database。

雖然 SQLCMD 支援從 SQL Server 2008 開始的 SQL Database，但是它不支援在 SQL Server 2008 R2 之前的版本中進行憑證驗證。 若要使用 SQLCMD 啟動 SQL Server 2008 R2 中驗證憑證，請使用 `-N` 命令列選項，而且不會使用 `-C` 選項。 若使用 -N 選項，SQLCMD 會要求加密的連接。 不使用 `-C` 選項，SQLCMD 並不會以隱含方式信任伺服器憑證，並強制驗證的憑證。

補充的技術資訊，請參閱 [Azure SQL 資料庫的連線安全性](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) 在 TechNet Wiki 網站的發行項。

## 驗證

Active Directory 驗證 (整合式安全性) 可在 SQL Database V12 中當成預覽使用。 如需設定 AD 驗證的詳細資訊，請參閱 [連接到 SQL 資料庫所使用 Azure Active Directory 驗證](sql-database-aad-authentication.md)。 若未使用預覽，使用者必須在每次連線到 SQL Database 時提供認證 (登入和密碼)。 如需有關 SQL Server 驗證的詳細資訊，請參閱 [選擇驗證模式](https://msdn.microsoft.com/library/ms144284.aspx) SQL Server 線上叢書 》 中。

[SQL Database V12](sql-database-v12-whats-new.md) 可讓使用者使用自主的資料庫使用者在資料庫進行驗證。 如需詳細資訊，請參閱 [所包含的資料庫使用者-讓您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx), ，[CREATE USER (TRANSACT-SQL)](https://technet.microsoft.com/library/ms173463.aspx), ，和 [自主資料庫](https://technet.microsoft.com/library/ff929071.aspx)。
> [AZURE.NOTE] Microsoft 建議使用自主資料庫使用者以加強延展性。

資料庫引擎會關閉保持閒置超過 30 分鐘的連接。 必須重新登入才能使用連接。

持續作用中的 SQL Database 連接，需要至少每 10 個小時重新授權一次 (由資料庫引擎執行)。 資料庫引擎嘗試重新授權時，會使用原本送出的密碼，但不需要使用者輸入。 基於效能考量，當在 SQL Database 中重設密碼時，此連接將不會重新驗證，即使連接因為連接共用而重設。 這點與內部部署 SQL Server 的行為不同。 如果連接經過初始授權後，密碼已經變更，則必須終止連接並使用新密碼建立新的連接。 使用 KILL DATABASE CONNECTION 權限的使用者可以明確地結束 SQL 資料庫的連接使用 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 命令。

## 登入和使用者

管理 SQL Database 的登入和使用者時，會有一些限制。

下列限制適用於伺服器層級主體登入：

- 對應至伺服器層級主體登入的 master 資料庫中的資料庫使用者，無法予以更改或卸除。
- 雖然伺服器層級的主體登入並不是 **master** 資料庫中這兩個資料庫角色 **dbmanager** 和 **loginmanager** 的成員，但是它擁有可授與這兩個角色的所有權限。

> [AZURE.NOTE] 此登入是在伺服器佈建期間所建立，而且類似於 SQL Server 執行個體中的 **sa** 登入。

下列限制適用於所有的登入：

- 英文 (美國) 是預設的語言。
- 若要存取 **master** 資料庫，每一個登入都必須對應到 **master** 資料庫中的使用者帳戶。 **master** 資料庫不支援自主資料庫使用者。
- 如果您未在連接字串中指定資料庫，根據預設，您將會連接到 **master** 資料庫。
- 您必須連接到 **主要** 時執行資料庫 `CREATE/ALTER/DROP LOGIN` 和 `CREATE/ALTER/DROP DATABASE` 陳述式。
- 執行時 `CREATE/ALTER/DROP LOGIN` 和 `CREATE/ALTER/DROP DATABASE` ADO.NET 應用程式中的陳述式，不允許使用參數化的命令。 如需詳細資訊，請參閱 [命令和參數](https://msdn.microsoft.com/library/ms254953.aspx)。
- 執行時 `CREATE/ALTER/DROP DATABASE` 和 `CREATE/ALTER/DROP LOGIN` 陳述式，每個陳述式必須是唯一的陳述式 TRANSACT-SQL 批次中。 否則便會發生錯誤。 例如，下列 Transact-SQL 會檢查資料庫是否存在。 如果存在的話， `卸除資料庫` 呼叫陳述式來移除資料庫。 因為 `卸除資料庫` 陳述式不是唯一的陳述式批次中，執行下列 TRANSACT-SQL 陳述式就會產生錯誤。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- 執行時 `CREATE USER` 陳述式搭配 `without LOGIN` 選項時，它必須是唯一的陳述式 TRANSACT-SQL 批次中。
- 執行時 `ALTER USER` 陳述式搭配 `WITH LOGIN` 選項時，它必須是唯一的陳述式 TRANSACT-SQL 批次中。
- 只有伺服器層級主體登入和成員 **dbmanager** 中的資料庫角色 **主要** 資料庫擁有執行權限 `CREATE DATABASE` 和 `卸除資料庫` 陳述式。
- 只有伺服器層級主體登入和成員 **loginmanager** 中的資料庫角色 **主要** 資料庫擁有執行權限 `建立登入`, ，`ALTER LOGIN`, ，和 `卸除登入` 陳述式。
- 若要 `CREATE/ALTER/DROP` 使用者必須擁有 `ALTER ANY USER` 資料庫的權限。
- 當資料庫角色的擁有者嘗試針對該資料庫角色加入或移除另一個資料庫使用者時，可能會發生下列錯誤：**此資料庫中沒有使用者或角色 'Name'**。因為擁有者看不到使用者，所以會發生此錯誤。 若要解決此問題，請授與角色的擁有者 `VIEW DEFINITION` 使用者的權限。

如需登入和使用者的詳細資訊，請參閱 [管理資料庫和登入 Azure SQL Database 中的](sql-database-manage-logins.md)。

## 安全性最佳作法

請考慮下列幾點，讓 Azure SQL Database 應用程式比較不容易受到安全性威脅：

- 一律使用最新的更新：連接到 SQL Database 時，一律使用最新版的工具和程式庫，以防止安全性弱點。
- 封鎖 TCP 通訊埠 1433 的傳入連接：應用程式與 SQL Database 進行通訊時，只需要 TCP 通訊埠 1433 的傳出連接。 如果該電腦上的任何其他應用程式不需要傳入的通訊，請確定您的防火牆會繼續封鎖 TCP 通訊埠 1433 的傳入連接。
- 防止插入式攻擊弱點：若要確定您的應用程式沒有 SQL 插入式攻擊弱點，盡可能使用參數化的查詢。 同時，請務必在部署應用程式之前，徹底檢閱程式碼並執行滲透測試。


## 另請參閱

[Azure SQL Database 防火牆](sql-database-firewall-configure.md)

[如何: 設定防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

[管理資料庫和 Azure SQL Database 中的登入](sql-database-manage-logins.md)

[SQL Server Database Engine 和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)





