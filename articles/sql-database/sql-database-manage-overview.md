---
title: "Áttekintés: SQL Database-felügyeleti eszközök | Microsoft Docs"
description: "Az Azure SQL Database felügyeletére szolgáló eszközöket és lehetőségeket hasonlítja össze"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 73cc9d1d02ed915466f0eac2d9a42fe8cf9e2fb9
ms.openlocfilehash: 32d0a1fd8671bbd9d450711dd686ca49c2178c16


---
# <a name="overview-management-tools-for-sql-database"></a>Áttekintés: SQL Database felügyeleti eszközök
Ez a témakör az Azure SQL Database-adatbázisok felügyeletére szolgáló eszközöket és lehetőségeket hasonlítja össze, hogy kiválaszthassa a feladatnak, az üzletmenetének és az igényeinek legjobban megfelelő eszközt. A megfelelő eszköz kiválasztása attól függ, hány adatbázist felügyel, pontosan mi a feladat, és milyen gyakran végzi el a feladatot.

## <a name="azure-portal"></a>Azure Portal
Az [Azure Portal](https://portal.azure.com) egy webalapú alkalmazás, ahol adatbázisokat és logikai kiszolgálókat hozhat létre, frissíthet és törölhet, valamint figyelemmel követheti az adatbázis-tevékenységeket. Ez az eszköz nagyszerű választás, ha most kezdte az Azure használatát, kevés adatbázist felügyel. vagy gyorsan kell elvégeznie valamit.

A portál használatáról további információért lásd [az SQL Database-adatbázisok az Azure Portal használatával történő felügyeletét](sql-database-manage-portal.md) ismertető cikket.

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio és SQL Server Data Tools a Visual Studióban
Az SQL Server Management Studio (SSMS) és az SQL Server Data Tools (SSDT) számítógépen futó ügyféleszközök a felhőbeli adatbázis felügyeletéhez és alakításához. Ha Ön a Visual Studiót vagy más integrált fejlesztőkörnyezeteket (IDE-ket) ismerő alkalmazásfejlesztő, [próbálja ki az SSDT használatát a Visual Studióban](https://msdn.microsoft.com/library/mt204009.aspx). Számos adatbázis-rendszergazda veszi igénybe az SSMS funkcióit, amely az Azure SQL-adatbázisokkal használható. [Töltse le az SSMS legújabb verzióját](https://msdn.microsoft.com/library/mt238290), és mindig a legújabb kiadást használja az Azure SQL Database használatakor. Az Azure SQL-adatbázisok SSMS-sel való felügyeletéről további információt az [SQL-adatbázisok az SSMS-sel történő felügyeletét](sql-database-manage-azure-ssms.md) ismertető cikkben talál.

> [!IMPORTANT]
> Mindig az [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) és az [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket.
>  

## <a name="powershell"></a>PowerShell
A PowerShell segítségével adatbázisokat és rugalmas készleteket felügyelhet, valamint automatizálhatja az Azure-erőforrások üzembe helyezését. A Microsoft ezt az eszközt javasolja nagy számú adatbázis felügyeletéhez, és az üzembe helyezés, valamint az erőforrás-módosítások automatizálásához éles környezetben.

További információért lásd [az SQL Database PowerShell használatával történő kezelését](sql-database-manage-powershell.md) ismertető cikket.

## <a name="elastic-database-tools"></a>Rugalmasadatbázis-eszközök
A rugalmasadatbázis-eszközökkel például a következőket végezheti el: 

* T-SQL-szkript futtatása adatbázis-készleteken egy [rugalmas feladattal](sql-database-elastic-jobs-overview.md)
* Több-bérlős modelladatbázisok áthelyezése egybérlős modellbe a [felosztási-egyesítési eszközzel](sql-database-elastic-scale-overview-split-and-merge.md)
* Adatbázisok felügyelete egybérlős vagy több-bérlős modellben a [rugalmas méretezésű ügyfélkódtárral](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>További források
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Jan17_HO1-->


