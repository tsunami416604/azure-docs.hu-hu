<properties 
    pageTitle="如何將使用者加入彈性資料庫集區" 
    description="您必須將具有權限的使用者加入集區中的每個資料庫" 
    metaKeywords="azure sql database elastic databases credentials" 
    services="sql-database" documentationCenter=""  
    manager="jeffreyg" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="ddove; sidneyh" />

# 如何將使用者加入至彈性資料庫集區

 **彈性資料庫工作** 功能 （預覽） 可讓您執行 TRANSACT-SQL 指令碼一整組資料庫包括自訂集合的資料庫， **彈性資料庫集區** 或 **彈性資料庫分區集** Azure SQL 資料庫中。 若要執行指令碼，您必須將具有適當權限的使用者加入執行工作所在的每個資料庫。 如需詳細資訊，請參閱 [管理資料庫和 Azure SQL Database 中的登入](sql-database-manage-logins.md) 或 [將使用者加入您的 SQL Azure 資料庫](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## 必要條件
* 安裝 [彈性工作元件](sql-database-elastic-jobs-service-installation.md)。 

## 如何將使用者加入資料庫

1.  必須先連接到 **主要** 的 Azure SQL Database 伺服器所在的資料庫彈性資料庫集區中，然後建立新的登入使用安裝時所提供的相同認證 **彈性資料庫工作**。

        CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. 登入集區中的每個資料庫，並使用相同的名稱和密碼建立使用者。 使用者必須具有足夠的權限，才能執行這項工作。 這個程式碼必須在每個資料庫上執行。

        CREATE USER admin1 FROM LOGIN login1;
        
3. 使用者也必須具有足夠的權限，才能執行為工作所指定的指令碼。 使用 [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) 為使用者提供的指令碼，以順利執行的最小必要權限。 

## 後續步驟

若要建立及管理 Azure 入口網站中的工作，請參閱 [建立和管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md)。 若要使用 PowerShell 建立工作，請參閱 [建立和管理 SQL Database 彈性資料庫工作使用 PowerShell （預覽）](sql-database-elastic-jobs-powershell.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

 

