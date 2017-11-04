---
title: "A Power BI használja az SQL Data Warehouse szolgáltatással |} Microsoft Docs"
description: "Tippek a Power BI használatával az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4b7609fc5d6ce7bf0e3bd3ebf6d8f52e93a40a75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>A Power BI használja az SQL Data Warehouse szolgáltatással
Mint az Azure SQL Database, SQL Data Warehouse közvetlen kapcsolódás felhasználó hatékony logikai leküldéses közzététele a Power bi analitikai funkciók együtt használni.  Közvetlen csatlakozás esetén a lekérdezések rendszer küldi vissza az Azure SQL Data Warehouse valós idejű, az adatokba.  Ez, az SQL Data Warehouse lehetővé teszi a számára elleni terabájtos adatkészleteket percben dinamikus jelentéseket készít a skála együtt.  A Megnyitás Power BI gomb bevezetése emellett lehetővé teszi a felhasználók kapcsolódhatnak közvetlenül a Power BI az SQL Data Warehouse Azure egyéb részeitől információk gyűjtése nélkül.

Ha közvetlen kapcsolódás használatával adjon Megjegyzés:

* Adja meg a teljes kiszolgálónév (lásd alább olvashat) csatlakozáskor
* Ellenőrizze az adatbázisra vonatkozó tűzfalszabályok az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése".
* Minden művelet, például jelöljön ki egy oszlopot, vagy hozzáad egy szűrőt közvetlenül az adatraktár lekérdezésére
* Csempék frissítése körülbelül 15 percenként (a frissítés nem szükséges ütemezése)
* A Q & A nincs közvetlen kapcsolódás adatkészletek
* Sémaváltozások nem átveszik automatikusan
* Minden közvetlen kapcsolódás lekérdezések időtúllépést okoz 2 perc múlva

E korlátozások és a megjegyzések módosíthatja, továbbra is a élmény javítása érdekében. Való csatlakozás lépéseit lásd lent.  

## <a name="using-the-open-in-power-bi-button"></a>A "Megnyitás Power BI-ban" gombra kattintva
Helyezze át a SQL Data Warehouse között a Power bi-ban a legegyszerűbb módja, nyissa meg a Power BI gomb. Ez a gomb lehetővé teszi zökkenőmentesen a Power BI-ban új irányítópultok létrehozásának megkezdéséhez.  

1. Első lépésként nyissa meg az SQL Data Warehouse-példányhoz a klasszikus Azure-portálon.
2. Kattintson a „Megnyitás Power BI-ban” gombra.
3. Ha jelenleg nem tudjuk szolgáltatásba közvetlenül, vagy ha nem rendelkezik Power BI-fiókja, szüksége lesz jelentkezik be.  
4. Hogy a rendszer kéri az SQL Data Warehouse csatlakozási oldalán, az SQL Data Warehouse előre feltöltve származó információkkal.
5. A hitelesítő adatok bevitele után meg teljesen csatlakoznak-e az SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>A Power BI-portálon keresztül csatlakozó
A Megnyitás Power BI gomb mellett, a felhasználók is kapcsolódhatnak az SQL Data Warehouse a Power BI-portálon keresztül.

1. Kattintson az "Adatok beolvasása" a navigációs ablaktábla alján.
2. Válassza ki a "Adatbázisokat".
3. Egyszer az adatbázisok lapon válassza az "Azure SQL Data Warehouse", és kattintson a "Csatlakozás".
4. Adja meg a szükséges csatlakozási adatait.  A kiszolgáló és adatbázis nevét az Azure portálon található.
5. Vissza a Power BI fő lapján a rendszer kéri, és ha a kapcsolat létrejött egy új "Adatkészletek" bejegyzést jelenik meg a példány nevét.  
6. Kattintson az új adatkészlet alapján megismerheti a táblák és nézetek az adatbázis összes. Jelöljön ki egy oszlopot visszaküldi a lekérdezés a forráskiszolgálón, dinamikusan létrehozása a visual. Ezek a látványelemek egy új jelentés mentése, és vissza rögzítve az irányítópulton való rögzítéséhez.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
