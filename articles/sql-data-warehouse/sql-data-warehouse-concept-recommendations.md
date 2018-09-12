---
title: SQL Data Warehouse-ajánlatok – fogalmak |} A Microsoft Docs
description: Ismerje meg az SQL Data Warehouse javaslatok, és hogyan generáljon
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 57bce631a570f549d46a9b0beefcb5adce4decfc
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380114"
---
# <a name="sql-data-warehouse-recommendations"></a>Az SQL Data Warehouse javaslatok

Ez a cikk ismerteti az Azure advisorral az SQL Data Warehouse által kiszolgált javaslatokat.  

Az SQL Data Warehouse biztosít annak biztosítása érdekében az adattárház javaslatok következetesen teljesítmény van optimalizálva. Data warehouse javaslatok szorosan integrálva [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) belül ajánlott eljárások közvetlenül tudja biztosítani a [az Azure portal](https://aka.ms/Azureadvisor). Az SQL Data Warehouse elemzi az adattárház jelenlegi állapotát, a napi szintű telemetriai és a Surface-eszközök javaslatok a aktív számítási feladatok számára gyűjti. Az alábbiakban javasolt műveletek alkalmazása mellett vázolt a támogatott data warehouse javaslat forgatókönyveket.

Ha bármilyen visszajelzése van az SQL Data Warehouse Advisor a vagy problémákat tapasztal, forduljon [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Kattintson a [Itt](https://aka.ms/Azureadvisor) a javaslatok ellenőrzése még ma! Ez a funkció jelenleg csak a Gen2 adattárházak alkalmazható. 

## <a name="data-skew"></a>Adateltérés

Adatok torzulása további adatok mozgását vagy az erőforrás szűk keresztmetszeteket okozhat, a számítási feladatok futtatásakor. Az alábbi dokumentáció azt ismerteti, adatok torzulása azonosításához, és a egy optimális terjesztési kulcs kiválasztásával el kerülni megjelenítése.

- [Azonosításához és eltávolításához a döntés](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nem vagy elavult statisztika

Optimálisnál rosszabb statisztikáit súlyosan hatással lehet a lekérdezési teljesítmény, az optimálisnál rosszabb lekérdezési tervek készítése az SQL Data Warehouse lekérdezésoptimalizáló okozhat. Az alábbi dokumentáció azt ismerteti, hogy körül statisztikák létrehozása és frissítése az ajánlott eljárásokat:

- [Tábla statisztikák létrehozása és frissítése](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Ezekkel az ajánlásokkal által érintett táblák listájának megtekintéséhez futtassa a következő [T-SQL parancsfájl](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Az advisor folyamatosan futó ugyanezt a T-SQL szkriptet létrehozni ezeket a javaslatokat.
