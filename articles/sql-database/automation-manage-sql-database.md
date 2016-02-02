<properties
    pageTitle="使用 Azure 自動化管理 Azure SQL 資料庫"
    description="了解如何使用 Azure 自動化服務大規模地管理 Azure SQL 資料庫。"
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jeffreyg"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="jolevy"/>




# 使用 Azure 自動化管理 Azure SQL 資料庫

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化您的 Azure SQL 資料庫管理。


## 什麼是 Azure 自動化？

[Azure 自動化](http://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可簡化雲端管理透過程序自動化。 透過 Azure 自動化，長時間執行、手動、容易發生錯誤和經常重複的工作都可以自動化，以提高可靠性、效率，並為您的組織縮短創造價值時程。

Azure 自動化提供非常可靠且高度可用的工作流程執行引擎，可隨著組織的成長根據您的需求進行調整。 在 Azure 自動化中，程序可透過手動方式、經由協力廠商系統，或依照排程的間隔啟動，讓工作精準地在需要時執行。

將您的雲端管理工作交由「Azure 自動化」自動執行，以降低營運負擔並釋出 IT/開發維運人力，使其專注於能夠為企業創造價值的工作上。


## Azure 自動化為何有助於管理 Azure SQL 資料庫？

可透過在 Azure 自動化管理 azure SQL Database [Azure SQL 資料庫 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/dn546726.aspx) 可用於 [Azure PowerShell 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)。 Azure 自動化的這些 Azure SQL 資料庫 PowerShell Cmdlet 都是內建的，以便您在服務內執行所有的 SQL 資料庫管理工作。 您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

Azure 自動化也可直接與 SQL 伺服器通訊，只要使用 PowerShell 發出 SQL 命令即可。

[Azure 自動化 runbook 資源庫](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) 含有多種產品團隊和社群 runbook，以便開始使用自動化管理 Azure SQL 資料庫、 其他 Azure 服務，以及第 3 個廠商系統。 資源庫 Runbook 包括：

 * [針對 SQL Server 資料庫執行 SQL 查詢](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [垂直調整 (向上或向下) Azure SQL Database 的排程](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [如果資料庫到達其大小上限，截斷的 SQL 資料表](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [如果高度片段化 Azure SQL 資料庫中的資料表建立索引](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## 後續步驟

了解 Azure 自動化的基本概念以及如何用它來管理 Azure SQL 資料庫之後，請參考下列連結，以深入了解 Azure 自動化。

 * 請參閱 Azure 自動化 [快速入門教學課程](../automation-create-runbook-from-samples.md)
 * 讀取 [Azure 自動化: 您的 SQL 代理程式定域機組中](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 部落格文章






