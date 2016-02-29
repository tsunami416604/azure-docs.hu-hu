<properties 
    pageTitle="在 SQL Database Update V12 中建立資料庫" 
    description="示範如何在 Azure SQL Database Update V12 中建立資料庫" 
    services="sql-database" 
    documentationCenter="" 
    authors="sonalmm" 
    manager="jeffreyg" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-management" 
    ms.date="04/28/2015" 
    ms.author="sonalm"/>


# 在 SQL Database V12 中建立資料庫


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[註冊](https://portal.azure.com) SQL Database v12 [(某些區域中的預覽)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable) Microsoft Azure 上利用新一代的 SQL 資料庫。 首先，您需要 Microsoft Azure 訂用帳戶。 申請 [免費 Azure 試用版](http://azure.microsoft.com/pricing/free-trial) 並檢閱 [定價](http://azure.microsoft.com/pricing/details/sql-database) 資訊。 


| 建立資料庫 | 螢幕擷取畫面 |
| :--- | :--- |
| 1.登入 [http://portal.azure.com/](http://portal.azure.com/)。 | ![新 Azure 傳統入口網站][] 1 |
| 2.在頁面上，在左側的底端按一下 [ **新增**。 | ![起始新的服務][] 2|
| 3.按一下 [ **SQL 資料庫**。| ![從選取不同的服務][] 3 |
| 4.A **SQL 資料庫** 分頁隨即開啟。 在 **名稱** 欄位中，指定資料庫名稱。 | ![資料庫名稱][] 4 |
| 5.在 **SQL Database] 分頁中**, ，按一下 [ **伺服器**。 A **伺服器** 刀鋒視窗隨即開啟，其中兩個選擇: 您可以建立新的伺服器，或使用現有的伺服器。| ![選取伺服器類型][] 4 |
|5a. 如果您選取 **使用現有的伺服器** 選項，請選取您所選的伺服器，並按一下 [ **選取**。 然後，完成步驟 6 以後的所有動作。| ![從清單中選取伺服器][] 5| 
|5b.   如果您選取 **建立新的伺服器**, 、 **新伺服器** 分頁隨即開啟。 指定伺服器名稱、伺服器系統管理員登入和密碼。 按一下 [ **位置** 選取伺服器位置。 | ![完成建立新的伺服器選項][] 9| 
|5c。 **新伺服器** 刀鋒視窗可讓您選擇使用 V12 更新建立新的伺服器。 若要深入了解 V12 伺服器中的功能，檢閱 [的 SQL Database V12 新功能](sql-database-v12-whats-new.md)。| ![選取 V12 伺服器][] 6|
|5d. 上進行選取 **新伺服器** 分頁，然後按一下 **確定**。 您將會回到 **SQL Database** 分頁，以完成動作，來建立資料庫的其餘部分。 | ![完成新增伺服器] 刀鋒視窗動作][] 8|
|6.按一下 [ **選取來源**。 建立資料庫時，您可以選取的各種來源類型： 空白資料庫、範例資料庫或從資料庫的備份。| ![選取來源資料庫][] 10|
|7.接下來，在 **SQL 資料庫** 刀鋒視窗中，按一下 [ **定價層**。 您可以選取其中一個建議的定價層或 **檢視所有** 可用的定價層。 進行選擇之後，請按一下 **選取**。 <p> 如需定價層的詳細資訊，請參閱 [SQL Database Web/Business 資料庫升級至新的服務層](./sql-database-upgrade-new-service-tiers/) 和 [Azure SQL Database 服務層和效能層級](sql-database-service-tiers.md)。 |![選取的定價層][] 7
| 8.接下來，在 **SQL 資料庫** 刀鋒視窗中，按一下 [ **選擇性組態**, ，進行選擇，然後按一下 **確定**。 
| 9.當您選取現有的伺服器， **資源群組** 和 **訂閱** 系統已為您選擇。 在 **SQL 資料庫** 刀鋒視窗中，您會看到顯示鎖定的圖示旁邊 **資源群組** 和 **訂閱**。 如果您建立新的伺服器，則可以選取或建立資源群組。 如需詳細資訊，請檢閱 [使用資源群組來管理您的 Azure 資源。](resource-group-overview.md)|![指定資源群組][] 11
| 10.按一下 [ **建立**。 即會建立含有 SQL Database V12 功能的新資料庫。 |![建立新的資料庫][] 12

## 相關連結

- [SQL Database V12 新功能](sql-database-v12-whats-new.md)
- [規劃和準備升級至 Azure SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 
