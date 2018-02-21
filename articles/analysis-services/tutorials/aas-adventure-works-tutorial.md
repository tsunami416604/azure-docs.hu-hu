---
title: "Azure Analysis Services – Az Adventure Works oktatóanyaga | Microsoft Docs"
description: "Az Azure Analysis Serviceshez készült Adventure Works-oktatóanyagot ismerteti"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/10/2018
ms.author: owend
ms.openlocfilehash: ecc95181f61f556b18fa820617ea5ad0636c7a4b
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services – Adventure Works-oktatóanyag

Ez az oktatóanyag azzal kapcsolatos leckéket tartalmaz, hogyan hozhat létre és helyezhet üzembe táblázatos modelleket az 1400-as kompatibilitási szinten a Visual Studio és az [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) együttes használatával.  

Ha most ismerkedik az Analysis Services szolgáltatással és a táblázatos modellezéssel, akkor ebből az oktatóanyagból sajátíthatja el leggyorsabban az alapszintű táblázatos modellek Visual Studio használatával történő létrehozását és üzembe helyezését. Ha az előfeltételek teljesültek, két-három óra szükséges a befejezésig.  
  
## <a name="what-you-learn"></a>Ismertetett témák   
  
-   Új táblázatosmodell-projekt létrehozása az **1400-as kompatibilitási szinten** a Visual Studióban az SSDT használatával.
  
-   Adatok importálása relációs adatbázisból egy táblázatosmodell-projekt munkaterület-adatbázisába.  
  
-   A modellben található táblák közötti kapcsolatok létrehozása és kezelése.  
  
-   Számított oszlopok, mértékek és fő teljesítménymutatók létrehozása, amelyek segítenek a felhasználóknak a kritikus fontosságú üzleti metrikák elemzésében.  
  
-   Perspektívák és hierarchiák létrehozása és kezelése, amelyekkel vállalkozás- és alkalmazásspecifikus szempontokat adhat meg, így egyszerűbben tallózhat a modelladatok között.  
  
-   Partíciók létrehozása, amelyek kisebb, a többi partíciótól függetlenül is feldolgozható logikai részekre osztják a táblaadatokat.  
  
-   Modellobjektumok és -adatok védelme szerepkörökkel rendelkező felhasználói tagok létrehozásával.  
  
-   Táblázatos modell üzembe helyezése egy **Azure Analysis Services**-kiszolgálón vagy egy helyszíni SQL Server 2017 Analysis Services-kiszolgálón.  
  
## <a name="prerequisites"></a>Előfeltételek  
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:  
  
-   Egy Azure Analysis Services-kiszolgáló. Regisztráljon az [Azure Analysis Services ingyenes próbaverziójára](https://azure.microsoft.com/services/analysis-services/), és [hozzon létre egy kiszolgálót](../analysis-services-create-server.md). 

-   Egy [Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) adattárház, amely rendelkezik az **AdventureWorksDW mintaadatbázissal** vagy egy SQL Server Data Warehouse, [Adventure Works mintaadatbázissal](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks).

    **Fontos:** Ha egy helyszíni SQL Server Data Warehouse adattárházba telepíti a mintaadatbázist, és egy Azure Analysis Services-kiszolgálón helyezi üzembe a modellt, akkor szükség van egy [helyszíni adatátjáróra](../analysis-services-gateway.md).

-   Az [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) for Visual Studio legújabb verziója.

-   Az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) legújabb verziója.    

-   Egy ügyfélalkalmazás (pl. [Power BI Desktop](https://powerbi.microsoft.com/desktop/) vagy Excel). 

## <a name="scenario"></a>Forgatókönyv  
Ez az oktatóanyag az Adventures Works Cycles nevű kitalált vállalatra épül. Az Adventure Works egy nagy, multinacionális gyártócég, amely kerékpárokat, alkatrészeket és kiegészítőket gyárt Észak-Amerika, Európa és Ázsia kereskedelmi piacaira. A vállalat 500 alkalmazottat foglalkoztat. Az Adventure Works továbbá számos regionális értékesítési csapatot alkalmaz a különböző piacokon. A projekt célja egy táblázatos modell létrehozása az értékesítési és marketing felhasználók számára, amely elemzi az AdventureWorksDW adatbázis internetes értékesítési adatait.  
  
Az oktatóanyag befejezéséhez különféle leckéket kell majd elvégeznie. Minden leckén belül feladatok várnak Önre. A leckék teljesítéséhez sorban kell végrehajtania a feladatokat. Előfordulhat, hogy egy bizonyos leckén belül több feladat is hasonló eredményt hoz, de a végrehajtásuk módja kismértékben különbözik. Ez a módszer bemutatja, hogy egy feladat gyakran többféleképpen teljesíthető, valamint a korábbi leckékben és feladatokban elsajátított készségeit is próbára teszi.  
  
A leckék célja, hogy végigvezessék Önt egy alapszintű táblázatos modell létrehozásán, amelyhez az SSDT-ben elérhető funkciókat használhatja. Mivel minden lecke az előző leckére épül, érdemes a megadott sorrendben elvégezni őket.
  
Ez az oktatóanyag nem tartalmaz leckéket a következőkről: kiszolgáló kezelése az Azure Portalon, kiszolgáló vagy adatbázis kezelése az SSMS használatával vagy ügyfélalkalmazás használata a modelladatok közötti navigáláshoz. 


## <a name="lessons"></a>Leckék  
Az oktatóanyag a következő leckéket tartalmazza:  
  
|Lecke|Az oktatóanyag elvégzésének várható időtartama|  
|----------|------------------------------|  
|[1 – Új táblázatos modellprojekt létrehozása](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 perc|  
|[2 – Az adatok beszerzése](../tutorials/aas-lesson-2-get-data.md)|10 perc|  
|[3 – Megjelölés dátumtáblaként](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 perc|  
|[4 – Kapcsolatok létrehozása](../tutorials/aas-lesson-4-create-relationships.md)|10 perc|  
|[5 – Számított oszlopok létrehozása](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 perc|
|[6 – Mértékek létrehozása](../tutorials/aas-lesson-6-create-measures.md)|30 perc|  
|[7 – Fő teljesítménymutatók (KPI) létrehozása](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 perc|  
|[8 – Perspektívák létrehozása](../tutorials/aas-lesson-8-create-perspectives.md)|5 perc|  
|[9 – Hierarchiák létrehozása](../tutorials/aas-lesson-9-create-hierarchies.md)|20 perc|  
|[10 – Partíciók létrehozása](../tutorials/aas-lesson-10-create-partitions.md)|15 perc|  
|[11 – Szerepkörök létrehozása](../tutorials/aas-lesson-11-create-roles.md)|15 perc|  
|[12 – Elemzés az Excelben](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 perc| 
|[13 – Üzembe helyezés](../tutorials/aas-lesson-13-deploy.md)|5 perc|  
  
## <a name="supplemental-lessons"></a>Kiegészítő leckék  
Ezek a leckék nem kötelezők az oktatóanyag elvégzéséhez, de hasznosak lehetnek az összetettebb táblázatos modellek létrehozásával kapcsolatos funkciók megismerésében.  
  
|Lecke|Az oktatóanyag elvégzésének várható időtartama|  
|----------|------------------------------|  
|[Részletsorok](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 perc|
|[Dinamikus biztonság](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 perc|
|[Hézagos hierarchiák](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 perc| 

  
## <a name="next-steps"></a>További lépések  
Az első lépéseket lásd: [1. lecke: Új táblázatosmodell-projekt létrehozása](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

