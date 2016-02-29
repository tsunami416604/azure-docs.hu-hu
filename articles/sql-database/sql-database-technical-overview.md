<properties
    pageTitle="什麼是 SQL Database？ SQL Database 簡介 | Microsoft Azure"
    description="取得 SQL Database 簡介：Microsoft 的關聯式資料庫管理系統 (RDBMS) 在雲端中的技術詳細資料和功能。"
    keywords="introduction to sql,intro to sql,what is sql database,DTU"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jeffreyg"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# 什麼是 SQL Database？ SQL Database、技術詳細資料和 DTU 說明的簡介

SQL Database 是以領先市場的 Microsoft SQL Server 引擎為基礎，位於雲端並擁有許多關鍵任務功能的關聯式資料庫服務。 SQL Database 提供可預測的效能、無停機時間的延展性、商務持續性和資料保護等功能，且全都幾乎免管理。 如此一來，您便可以專注於快速開發應用程式及加快上市時間，而不是耗費在管理虛擬機器與基礎結構上。 因為它根據 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 引擎，SQL Database 支援現有的 SQL Server 工具、 程式庫和 Api，可讓您輕鬆地移動，並延伸到雲端。

本文介紹與效能、延展性和管理能力相關的 SQL Database 核心概念和功能，並提供連結讓您進一步了解詳細資料。 如果您準備好要跳中，您可以 [建立您的第一個 SQL database](sql-database-get-started.md) 或 [建立彈性資料庫集區](sql-database-elastic-pool-portal.md) 以分鐘為單位。 如果您想要進行更深入的探討，請觀賞這段 30 分鐘的影片。


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## 無須停機即可調整效能和規模
SQL 資料庫可用於基本、 標準和高階 *服務層*。 每個服務層提供 [不同層級的效能和功能](sql-database-service-tiers.md) 支援輕量型重量級的資料庫工作負載。 您可以在小型資料庫上建立第一個應用程式的少數 bucks 一個月，然後 [變更服務層](sql-database-scale-up.md) 手動或以程式設計方式在任何時候您的應用程式不用爆紅全球，為您的應用程式或您客戶的停機時間。

對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低單一資料庫的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 

[彈性資料庫集區](sql-database-elastic-pool.md) SQL 資料庫中解決此問題。 概念很簡單。 您可以將效能配置給集區，並針對集區的整體效能 (而非單一資料庫的效能) 付款。 您不需要調高或調低資料庫的效能。 集區的資料庫稱為 *彈性資料庫*, 、 自動向上和向下以滿足需求。 彈性資料庫會取用集區的資源，但不會超過其限制，因此您的成本可在資料庫使用情形無法預測的狀況下維持可預測性。 什麼是多個項目，您可以 [新增和移除資料庫至集區](sql-database-elastic-pool-portal.md), ，調整您的應用程式從少數幾個資料庫全部都在您所控制的預算內千分位。

因此，不論您要使用單一或彈性資料庫，都將不再受到限制。 您可以混合使用彈性資料庫集區與單一資料庫，並變更單一資料庫和集區的服務層，以製作創新的設計。 此外，透過 Azure 功能強大而無遠弗屆的特性，您可以使用 SQL Database 混合和搭配 Azure 服務，滿足您獨特新型應用程式的設計需求、有效運用成本和資源，並且產生新的商機。

但是，您要如何比較資料庫和資料庫集區的相對效能？ 當您調高和調低效能時，要如何知道該在何處停止？ 答案就是使用單一資料庫的資料庫交易單位 (DTU)，以及彈性資料庫和資料庫集區的彈性 DTU (eDTU)。

## 了解 DTU

[AZURE.INCLUDE [SQL DB DTU description](../../includes/sql-database-understanding-dtus.md)]

## 讓您的應用程式和業務持續運作

Azure 的業界領先的 99.99%可用性服務等級協定 [(SLA)](http://azure.microsoft.com/support/legal/sla/), ，由 Microsoft 管理的資料中心的全球網路，可協助確保執行 24/7 的應用程式。 使用每個 SQL Database 時，您可以利用內建的資料保護、容錯功能，以及可能會需要另外設計、購買、建置和管理的資料保護功能。 即便如此，根據您的業務需求，您也可能會要求額外層級的保護，以確保在發生嚴重損壞、錯誤或其他事件時，應用程式和業務可以快速復原。 使用 SQL Database 時，每個服務層會提供不同的功能選單，讓您用來立即上線運作。 您可以使用時間點還原將資料庫回復成先前的狀態，最久可至 35 天前。 此外，如果裝載資料庫的資料中心發生中斷情形，您可以容錯移轉至其他區域中的資料庫複本。 或者您可以使用複本進行升級，或將其重新放置到不同區域。

![SQL Database 異地複寫](./media/sql-database-technical-overview/azure_sqldb_map.png)


請參閱 [業務持續性](sql-database-business-continuity.md) 如需詳細資訊的不同服務層提供不同的業務持續性功能。

## 保護您的資料
SQL Server 有傳統的 SQL 資料庫做表示支持的功能，限制存取、 保護資料，並協助您監視活動的穩固的資料安全性。 請參閱 [保護您的 SQL database](sql-database-security.md) 的快速摘要的 SQL 資料庫中擁有的安全性選項。 請參閱 [SQL Server Database Engine 和 SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589) 安全性功能更完整檢視。 造訪 [Azure 信任中心](http://azure.microsoft.com/support/trust-center/security/) 如需 Azure 的平台安全性資訊。

## 後續步驟
您現已閱讀 SQL Database 簡介並回答了「什麼是 SQL Database？」，您就可以執行下列作業：

- 請參閱 [定價頁面](http://azure.microsoft.com/pricing/details/sql-database/) ，單一資料庫和彈性資料庫定價計算機。

- 要開始 [建立第一個資料庫](sql-database-get-started.md)。 然後建立第一個應用程式 [C#](sql-database-connect-query.md), ，[Java](sql-database-develop-java-simple-windows.md), ，[Node.js](sql-database-develop-nodejs-simple-windows.md), ，[PHP](sql-database-develop-php-retry-windows.md), ，[Python](sql-database-develop-python-simple-windows.md), ，或 [Ruby](sql-database-develop-ruby-simple-linux)。 


