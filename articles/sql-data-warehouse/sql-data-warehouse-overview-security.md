<properties
   pageTitle="保護 SQL 資料倉儲中的資料庫 | Microsoft Azure"
   description="保護 Azure SQL 資料倉儲中的資料庫以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/15/2015"
   ms.author="sahajs"/>


# 保護 SQL 資料倉儲中的資料庫

本文逐步解說保護 Azure SQL 資料倉儲資料庫的基本概念。 本文將著重於協助您開始利用資源，在資料庫上限制存取、保護資料，以及監視活動。

## 連接安全性

「連線安全性」是指如何使用防火牆規則和連線加密，限制和保護資料庫的連線。

伺服器和資料庫兩者都可使用防火牆規則，拒絕來自未明確設為允許清單的 IP 位址的連線嘗試。 若要允許應用程式或用戶端機器的公用 IP 位址，嘗試連線至新的資料庫，您必須先使用 Azure 傳統入口網站、REST API 或 PowerShell 建立伺服器層級的防火牆規則。 最好的作法是，您應該盡可能限制允許穿透您伺服器防火牆的 IP 位址範圍。 如需詳細資訊，請參閱 [Azure SQL Database 防火牆 []][]。


## 驗證

「驗證」是指連線到資料庫時如何證明身分識別。 SQL 資料倉儲目前支援使用使用者名稱和密碼的 SQL 驗證。

當您為資料庫建立邏輯伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。

不過，最佳作法是，貴組織的使用者應該使用不同的帳戶來驗證。 因為萬一應用程式的程式碼容易受到 SQL 插入式攻擊，您就可以限制授與應用程式的權限，並降低惡意活動的風險。 若要依據伺服器登入建立資料庫使用者：

首先，使用伺服器管理員登入連接到您伺服器上的主資料庫，並建立新的伺服器登入。

```
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';
```

然後使用您的伺服器管理員登入，連接到 SQL 資料倉儲資料庫，並根據您剛建立的伺服器登入建立資料庫使用者。

```

-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

如需有關驗證 SQL Database 的詳細資訊，請參閱 [管理資料庫和 Azure SQL Database [] 中的登入][]。


## Authorization

「授權」是指在 Azure SQL 資料倉儲內可以執行的動作，這是由使用者帳戶的角色成員資格和權限所控制。 最好的作法是，您應該授與使用者所需的最低權限。 Azure SQL 資料倉儲可讓您輕鬆地透過 T-SQL 中的角色進行上述管理：

```
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。

有許多方式可以進一步限制使用者透過 Azure SQL Database 可以執行的動作：

- [資料庫角色 []][] 除了 db_datareader 和 db_datawriter 可以用來建立功能更強大的應用程式的使用者帳戶或權力較小的管理帳戶。
- 細微 [權限 []][] 可讓您控制哪些作業，您可以執行個別的資料行、 資料表、 檢視、 程序和其他資料庫中的物件。
- [預存程序 []][] 可以用來限制可對資料庫採取的動作。

要從 Azure 傳統入口網站或使用 Azure 資源管理員 API 管理資料庫和邏輯伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需有關本主題的詳細資訊，請參閱 [Azure 入口網站 [] 中的角色型存取控制][]。



## 加密

Azure SQL 資料倉儲可以協助保護您的資料時在 「 靜止 」 時加密資料或儲存在資料庫檔案和備份，使用 [透明資料加密]][]。 若要加密您的資料庫，請連接到您伺服器上的主要資料庫並執行：


```

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

您也可以從 [設定資料庫啟用透明資料加密 [Azure 傳統入口網站 []][]。



## 稽核

稽核和追蹤資料庫事件可協助您遵循法規，並找出可疑的活動。 SQL 資料倉儲稽核可讓您將資料庫中的事件記錄到 Azure 儲存體帳戶中的稽核記錄。 SQL 資料倉儲稽核也整合了 Microsoft Power BI，具備向下鑽研報表和分析的功能。 如需詳細資訊，請參閱 [開始使用 SQL 資料庫稽核 []][]。



## 後續步驟

如需更多開發秘訣，請參閱 [開發概觀 []][]。







[development overview]: sql-data-warehouse-overview-develop.md 
[azure sql database firewall]: https://msdn.microsoft.com/library/ee621782.aspx 
[database roles]: https://msdn.microsoft.com/library/ms189121.aspx 
[managing databases and logins in azure sql database]: https://msdn.microsoft.com/library/ee336235.aspx 
[permissions]: https://msdn.microsoft.com/library/ms191291.aspx 
[stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx 
[transparent data encryption]: http://go.microsoft.com/fwlink/?LinkId=526242 
[get started with sql database auditing]: sql-database-auditing-get-started.md 
[azure classic portal]: https://portal.azure.com/ 
[role-based access control in azure portal]: http://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-configure.aspx 

