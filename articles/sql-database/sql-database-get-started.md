<properties
    pageTitle="SQL Database 教學課程：建立 SQL Database | Microsoft Azure"
    description="在 Azure 入口網站中使用 Microsoft 的關聯式資料庫管理系統 (RDBMS)，於幾分鐘內建立第一個 SQL Database。"
    keywords="sql database tutorial,create a sql database"  
    services="sql-database"
    documentationCenter=""
    authors="jeffgoll"
    manager="jeffreyg"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/01/2015"
    ms.author="jeffreyg"/>

# SQL Database 教學課程：使用範例資料和 Azure 入口網站在幾分鐘內建立 SQL database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

本 SQL Database 教學課程示範如何在 Azure 入口網站中使用範例資料，在短短幾分鐘內建立第一個 SQL 資料庫。 您將學習如何：

- 建立伺服器以裝載您建立的資料庫，然後為其設定防火牆規則。
- 從 AdventureWorks 範例中建立 SQL Database，其中包含您可以處理的資料

在開始之前，您需要有 Azure 帳戶和訂用帳戶。 如果您沒有帳戶，註冊 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

> [AZURE.NOTE] 此 SQL Database 教學課程涵蓋定域機組，Azure SQL Database 中使用 Microsoft 的關聯式資料庫管理服務的資料庫設定。 另一個選項是在 Azure 虛擬機器上執行 SQL Server。 請參閱 [了解 Azure SQL Database 和 Azure Vm 中的 SQL Server](data-management-azure-sql-database-and-sql-server-iaas.md) 如需快速比較，或您可以看到 [佈建 SQL server 虛擬機器](virtual-machines-provision-sql-server.md) 若要開始使用虛擬機器。

## 步驟 1：登入並開始設定 SQL Database
1. 登入 [Azure 入口網站](http://portal.azure.com/)。
2. 按一下 [ **新** > **資料 + 儲存體** > **SQL 資料庫**。

    ![SQL Database 教學課程：建立新的 SQL Database。](./media/sql-database-get-started/create-db.png)
    
     **SQL Database** 設定] 刀鋒視窗隨即出現，就是您將設定伺服器和資料庫的詳細資料。

    ![適用於 SQL Database 的資料庫與伺服器設定](./media/sql-database-get-started/get-started-dbandserversettings.png)

## 步驟 2：選擇伺服器設定
Azure 中的 SQL Database 會存留在資料庫伺服器中。 伺服器可以裝載多個資料庫。 設定資料庫時，您也可以建立並設定將裝載該資料庫的伺服器，或者可以使用先前建立的伺服器。 我們將設定新的伺服器。

1. 型別 a **名稱** 為您的資料庫 (我們使用 **AdventureWorks**)。 我們稍後再回頭討論其他資料庫設定。
2. 在 **伺服器** 按一下 **設定所需設定**, ，然後按一下 [ **建立新的伺服器**。

    ![為您的資料庫命名](./media/sql-database-get-started/name-and-newserver.png)

3. 在 **新伺服器** 刀鋒視窗中，輸入 **伺服器名稱** 是整個 Azure 獨一無二且容易記住。 稍後當您連接並使用資料庫時，將需要此名稱。
4. 型別 a **伺服器管理員登入** 是好記的字串 (我們使用 **AdventureAdmin**)。 然後輸入 [安全 **密碼** 並重新輸入一次在 **確認密碼**。

    ![新的資料庫伺服器設定](./media/sql-database-get-started/get-started-serversettings.png)

     保留 **建立 V12 伺服器 (最新更新)** 設 **是** 使用最新的功能。  **位置** 判斷您的伺服器建立所在的資料中心區域。

    >[AZURE.TIP] 接近會使用資料庫的應用程式的位置建立資料庫伺服器。 如果您想要變更位置，只要按一下 **位置**, ，挑選一個不同，然後按一下 **確定**。

5. 按一下 [ **確定** 回到 **SQL Database** 刀鋒視窗。 

資料庫與伺服器尚未建立。 此狀況將會在下一個步驟 (您會選擇從 AdventureWorks 範例中建立資料庫，然後確認設定) 完成後發生。

## 步驟 3：設定並建立 SQL Database
1. 在 **SQL Database** 刀鋒視窗中，按一下 [ **選取來源** 然後按一下 [ **範例**。 

    ![從範例建立 SQL Database](./media/sql-database-get-started/new-sample-db.png)

2. 您返回 **SQL Database** 刀鋒視窗中，其中 **選取範例** 現在會顯示 **AdventureWorks LT [V12]**。 按一下 [ **建立** 即可開始進行伺服器和資料庫的建立。

    ![建立範例 SQL Database](./media/sql-database-get-started/adworks_create.png)

    >[AZURE.NOTE] 這個快速的作法，我們沒有變更的設定 **定價層**, ，**定序**, ，和 **資源群組**。 您可以隨時變更資料庫定價層，並向上調升和向下減少，無須停機。 請參閱 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/) 和 [SQL database 定價層](sql-database-service-tiers.md) 若要深入了。 完成資料庫定序設定後，您將無法進行變更。 請參閱 [定序與 Unicode 支援](https://msdn.microsoft.com/library/ms143726.aspx) 定序的詳細資料。 請參閱 [Azure 資源管理員概觀](resource-group-overview.md) Azure 資源群組的詳細資料。

您會跳回「Azure 開始面板」，其中磚會顯示進度，直到資料庫建立完畢且已上線為止。 您也可以按一下 **全部瀏覽** 然後按一下 [ **SQL 資料庫** 確認資料庫是否在線上。
    
恭喜！ 您的雲端中目前已有執行的 SQL Database。 您即將完成設定。 最後還剩下一個重要步驟。 您必須在資料庫中設定規則，以便連接至資料庫。

## 步驟 4：設定防火牆

您必須在伺服器中設定防火牆規則，允許從用戶端電腦 IP 位置進行連接，以便使用資料庫。 如此不僅可確保能與資料庫連接，還是個可以查看區域的好方式，您可以在其中取得有關 Azure 中 SQL 伺服器的其他詳細資料。 

1. 按一下 [ **全部瀏覽**, ，向下捲動，然後按一下 **SQL server**, ，然後按一下您稍早建立的清單中的伺服器名稱 **SQL 伺服器**。

    ![選取您的資料庫伺服器](./media/sql-database-get-started/browse_dbservers.png)

    
3. 在右邊會出現 [資料庫屬性] 分頁，按一下 [ **設定** 然後按一下 [ **防火牆** 從清單中。

    ![開啟防火牆設定](./media/sql-database-get-started/db_settings.png)


    The **Firewall settings** show your current **Client IP address**. 

    ![Current IP address](./media/sql-database-get-started/firewall_config_client_ip.png)

4. 按一下 [ **新增用戶端 IP** ，讓 Azure 建立的規則，該 IP 位址，並按一下 [ **儲存**。

    ![新增 IP 位址](./media/sql-database-get-started/firewall_config_new_rule.png)

    >[AZURE.IMPORTANT] 您的用戶端 IP 位址可能會改變不時，且可能無法存取您的伺服器，您必須建立新的防火牆規則。 您可以檢查您的 [IP 位址使用 [Bing](http://www.bing.com/search?q=my%20ip%20address), ，然後新增 [單一 IP 位址或 IP 位址範圍。 請參閱 [如何設定防火牆設定](sql-database-configure-firewall-settings.md) 如需詳細資訊。

## 後續步驟
現在您已完成本 SQL Database 教學課程並建立有一些範例資料的資料庫，您可以準備開始使用慣用的工具進行探索。

- 如果您熟悉 TRANSACT-SQL 和 SQL Server Management Studio，了解如何 [連接並查詢 SQL 資料庫使用 SSMS](sql-database-connect-query-ssms.md)。

- 如果您知道 Excel，了解如何 [與 Excel 連接到 SQL 資料庫](sql-database-connect-excel.md)。

- 如果您準備好要開始撰寫程式碼，請參閱 [連接並查詢您的 SQL database 使用 C#](sql-database-connect-query.md) 和 [從.NET (C#) 使用 SQL 資料庫](sql-database-develop-dotnet-simple.md)。 請參閱 [快速入門程式碼範例 SQL 資料庫](sql-database-develop-quick-start-client-code-samples.md) Node.js、 Python、 Ruby、 Java、 PHP 和 c + + 範例及如何-的除了 C#。

- 如果您想要將內部部署 SQL Server 資料庫移至 Azure，請參閱 [資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md) 若要深入了。



