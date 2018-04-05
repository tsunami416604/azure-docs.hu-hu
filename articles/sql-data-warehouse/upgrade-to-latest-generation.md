---
title: Frissítés az Azure SQL Data Warehouse legújabb generációját |} Microsoft Docs
description: Azure SQL Data Warehouse frissítése a legújabb generációját Azure hardver- és tároló-architektúra lépéseket.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Az Azure-portálon az Azure SQL Data Warehouse legújabb generációját frissítése

Az Azure portál segítségével frissítse az Azure SQL Data Warehouse használatához Azure hardver- és tárolási architektúra legújabb generációját. A frissítés által kihasználhatja gyorsabb, nagyobb méretezhetőséggel és az oszlopcentrikus indexek korlátlan tárterület.  

## <a name="applies-to"></a>A következőkre vonatkozik
Ez a frissítés a rugalmasság teljesítményszinttel optimalizálása az adatraktárak vonatkozik.  Az utasításokat a rugalmasság teljesítményszinttel optimalizálása adatraktár számítási teljesítmény szinten optimalizált frissítésével. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

1. Ha a frissítendő rugalmasság adatraktár optimalizálása fel van függesztve, [folytathatja az adatraktár](pause-and-resume-compute-portal.md).
2. Néhány perc leállás mellett készíteni. 
3. A frissítési folyamat használhatatlanná teszi az összes munkamenet, és az összes kapcsolat esik. A frissítés előtt győződjön meg arról, a lekérdezések végrehajtotta. Ha egy frissítés a folyamatban lévő tranzakciók indítása, a visszaállítás ideje széles körű lehet. 

## <a name="start-the-upgrade"></a>A frissítés megkezdése

1. Nyissa meg az adatraktár az Azure-portálon, és kattintson **számítás optimalizált frissítés**.
2. Figyelje meg a számítási teljesítmény réteg döntések optimalizálása. Az alapértelmezett érték az aktuális szinten, a frissítés előtt összehasonlítható.
3. Válasszon egy teljesítményszinttel. A számítási teljesítmény rétegben optimalizálása árát a rendszer nem jelenleg félig-a próbaidőszak alatt.
4. Kattintson a **frissítése**.
5. Ellenőrizze az Azure portálon.
6. Várjon, amíg az adatraktár online módosítani.

## <a name="rebuild-columnstore-indexes"></a>Oszlopcentrikus indexek újraépítése

Amennyiben az adatraktár online állapotban, az adatok betöltése, és lekérdezéseket futtathat. Teljesítmény azonban nem először lassú, mert egy háttérfolyamat az adatok áttelepítése új hardverre. 

A lehető leggyorsabban áttelepítéséhez adat titkosításának kikényszerítése, ajánlott az oszlopcentrikus indexek újraépítése. Ehhez lásd az útmutató a [szegmens minőségének javítására oszlopcentrikus indexek újraépítése](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>További lépések
Az adatraktár online állapotban. Az új teljesítmény-szolgáltatások használatához, tekintse meg a [erőforrás-osztályok a munkaterhelés felügyeleti](resource-classes-for-workload-management.md).
 