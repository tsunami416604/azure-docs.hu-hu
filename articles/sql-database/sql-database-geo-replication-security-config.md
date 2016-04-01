<properties
    pageTitle="標準或主動式異地複寫的安全性組態"
    description="本主題說明管理 SQL Database 之標準或主動式異地覆寫案例的安全性考量。"
    services="sql-database"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/22/2015"
    ms.author="jroth" />

# 標準或主動式異地複寫的安全性組態

## 概觀
本主題說明設定和控制的驗證需求 [標準和作用中地理複寫](sql-database-geo-replication-overview.md) 以及設定次要資料庫的使用者存取所需的步驟。 如需有關如何使用異地複寫的詳細資訊，請參閱 [從中斷復原 Azure SQL Database](sql-database-disaster-recovery.md)。

## 使用自主使用者
使用 [Azure SQL Database V12 版](sql-database-v12-whats-new.md), ，SQL Database 現在支援包含的使用者。 不同於傳統的使用者必須對應到 master 資料庫中的登入，自主使用者完全由資料庫本身管理。 這樣有兩個優點。 在異地覆寫案例中，使用者可以繼續連接到次要資料庫，而不需任何額外的組態，因為資料庫可管理使用者。 從登入的觀點來看，這個組態也有潛在的延展性和效能優勢。 如需詳細資訊，請參閱 [所包含的資料庫使用者-讓您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx)。 

關於自主使用者，如果您有多個資料庫使用相同的登入，您必須為每個資料庫 (例如密碼變更) 個別管理該使用者，而不是在伺服器層級管理登入。

>[AZURE.NOTE] 如果您想要個別變更主要和次要的讀取權限，則您必須使用傳統的登入和使用者。 獨立於主要次要複本上，將無法管理包含的使用者。

## 使用傳統的登入和使用者
如果您使用傳統的登入和使用者 (而非自主使用者)，您必須採取額外步驟以確保相同的登入存在於次要資料庫伺服器上。 下列各節概述涉及的步驟和其他考量。

### 設定線上次要資料庫的使用者存取
為了讓次要資料庫可供使用做為唯讀資料庫 (線上次要資料庫)，或是在容錯移轉的情況下可用的資料庫複本，次要資料庫必須具有適當的安全性組態。

只有伺服器管理員可以順利完成本主題稍後所述的所有步驟。 本主題稍後說明每個步驟的特定權限。

隨時都可以準備主動式異地複寫線上次要資料庫的使用者存取。 它包含如下所述的三個步驟：

1. 決定具備主要資料庫存取權的登入。
2. 在來源伺服器上尋找這些登入的 SID。
3. 利用來自來源伺服器的相符 SID，在目標伺服器上產生登入。

#### 1.決定具備主要資料庫存取權的登入：
程序的第一個步驟是判斷目標伺服器上必須複製哪些登入。 這是以一組 SELECT 陳述式完成，一個在來源伺服器上的邏輯 master 資料庫中，一個在主要資料庫本身。

只有伺服器管理員或成員的 **LoginManager** 伺服器角色可以決定下列 SELECT 陳述式的來源伺服器上的登入。 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

只有 db_owner 資料庫角色的成員、dbo 使用者或伺服器系統管理員可以判斷主要資料庫中的所有資料庫使用者主體。

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### 2.尋找在步驟 1 中識別的登入 SID：
藉由比較上一節查詢的輸出並比對 SID，您可以將伺服器登入對應到資料庫使用者。 具有資料庫使用者及相符 SID 的登入也會有該資料庫的使用者存取權做為資料庫使用者主體。 

下列查詢可用來查看資料庫中的所有使用者主體及其 SID。 只有 db_owner 資料庫角色的成員或伺服器系統管理員才能執行此查詢。

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE]  **INFORMATION_SCHEMA** 和 **sys** 使用者擁有 *NULL* Sid，而 **來賓** SID 是 **0x00**。  **Dbo** SID 可能會開始 *0x01060000000001648000000000048454 開頭*, ，如果資料庫建立者是伺服器管理員，而不是屬於 **DbManager**。

#### 3.在目標伺服器上產生登入：
最後一個步驟是移至目標伺服器或伺服器，並以適當的 SID 產生登入。 基本語法如下所示。

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

>[AZURE.NOTE] 如果您想要授與次要資料庫，但是不能在主要的使用者存取，您可以這麼做可以使用下列語法更改主要伺服器上的使用者登入。
>
>ALTER LOGIN <login name> 停用
>
>DISABLE 不會變更密碼，因此必要時可以永遠啟用。

## 在終止連續複製關聯性時設定使用者存取權
在容錯移轉發生時，主要和次要資料庫之間的連續複製關聯性必須停止。 如需此程序的資訊，請參閱 [從中斷復原 Azure SQL Database](sql-database-disaster-recovery.md)。

若為標準異地覆寫，使用者無法存取離線的次要資料庫，因此必須在終止連續複製關聯性時進行使用者帳戶的變更。

如果登入 SID 未在目標伺服器上重複，終止後僅限伺服器系統管理員有次要資料庫的存取權。 如果起始複寫的使用者是 DbManager，它們無法存取次要資料庫，除非它們的登入 SID 是從來源伺服器所複製。 只有對於複寫程序的存留期才會是這樣的情況。

當複寫終止時，在終止程序中，[dbo] 使用者主體將會變更為符合起始複寫之使用者的登入 SID，而該使用者將可以還原存取。 對於其他資料庫使用者就不是這樣的情況。

使用者帳戶和用來起始終止作業的相關聯登入應該出現在目標伺服器和資料庫，以確認該使用者帳戶能夠在終止完成後存取次要資料庫。

如需有關容錯移轉之後所需的步驟的詳細資訊，請參閱 [完成復原的 Azure SQL Database](sql-database-recovered-finalize.md)。

## 後續步驟
如需有關地理區域複寫和其他商務持續性功能的 SQL 資料庫的詳細資訊，請參閱 [業務持續性概觀](sql-database-business-continuity.md)。

