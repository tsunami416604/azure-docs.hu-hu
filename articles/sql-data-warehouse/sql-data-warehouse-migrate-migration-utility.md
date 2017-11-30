---
title: "Áttelepítése: Az adatraktár áttelepítés segédprogram |} Microsoft Docs"
description: "Az SQL Data Warehouse telepíthet át."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>Data Warehouse Fájláttelepítő segédprogram (előzetes verzió)
> [!div class="op_single_selector"]
> * [Fájláttelepítő segédprogram letöltése][Download Migration Utility]
> 
> 

A Data Warehouse Fájláttelepítő segédprogram futtatása az eszköz a séma- és adatok áttelepítése az SQL Server és az Azure SQL Database az Azure SQL Data Warehouse. Séma az áttelepítés során az eszköz automatikusan hozzárendeli a megfelelő séma forrás célhelyre. A séma áttelepítése után az eszközök áthelyezni az adatokat az automatikusan létrehozott parancsfájlokkal lehetőséget biztosít.

Séma- és az áttelepítés mellett az eszköz felajánlja a forrás- és példányai, amely megakadályozná a zökkenőmentes áttelepítés között incompatibilities összefoglalója kompatibilitási-jelentések készítéséhez.

## <a name="get-started"></a>Bevezetés
A telepítéshez előfeltételként szüksége lesz a BCP parancssori segédprogram áttelepítési parancsfájlok és a kompatibilitási jelentés megtekintéséhez Office futtatásához. Miután elindította a letöltött végrehajtható fájl kérni fogja a szabványos végfelhasználói licencszerződés elfogadásához, mielőtt az eszköz települ.

Emellett futtassa az áttelepítési Utiliy, szüksége lesz az alábbi engedélyek használata az adatbázis, amely szeretné áttelepíteni:-adatbázis létrehozása, az ALTER ANY DATABASE vagy bármilyen NÉZETDEFINÍCIÓBAN.

### <a name="launching-the-tool-and-connecting"></a>Az eszköz megnyitása és csatlakozás
A telepítés után megjelenő asztali ikonra kattintva nyissa meg az eszköz. Az eszköz megnyitásakor kérni fogja a egy kezdeti kapcsolat lap, ahol kiválaszthatja a forrás- és az áttelepítési eszköz. Most támogatott SQL Server és az Azure SQL Database adatforrások és az SQL Data Warehouse célhelye. Ha bejelöli ezt követően kérni fog csatlakozni, hogy a forráskiszolgáló kitölti a kiszolgálónevet és hitelesítéséhez, majd kattintson az "Csatlakozás".

Hitelesítés után az eszköz jelennek meg, amelyek szerepelnek a kiszolgálót, amely kapcsolódik az adatbázisok listája. Az áttelepítés egy adatbázist, amely az áttelepíteni kívánt kiválasztásával, és kattintson a "Kijelölt áttelepítése" megkezdése.

## <a name="migration-report"></a>Áttelepítési jelentés
Jelölje ki az eszköz "Adatbázis kompatibilitásának ellenőrzése" hoz létre egy jelentést, az adatbázis összes objektum inkompatibilitása összefoglalójához áttelepítéséhez a kért. Egyes, amely nem jelenik meg az SQL Data Warehouse az SQL Server szolgáltatásai szélesebb körű listája megtalálható az [az áttelepítési dokumentáció][migration documentation]. A jelentés előállítása után lesz mentheti, és nyissa meg a jelentést az Excel programban.

Ne feledje, ha az áttelepítési séma, a legtöbb probléma megállapítja, hogy annak érdekében, hogy az adatok azonnali áttelepítési módosul "Object". Tekintse át a módosításokat, annak érdekében, amelyeket nem szeretne további módosításokat a séma alkalmazása előtt.

## <a name="migrate-schema"></a>Séma áttelepítése
A csatlakozás után át séma kiválasztásával hoz létre a kiválasztott táblák séma áttelepítési parancsfájl. A parancsfájl portok a tábla, nem kompatibilis a maps-adatok szerkezetének több kompatibilis űrlapok típust, és létrehozza a hitelesítő adatokat és a séma, ha ez jelzi a felhasználó az áttelepítési beállításokat. Ezt a kódot is futtathatók a célként kijelölt SQL Data Warehouse-példányhoz, egy fájlba menti, a vágólapra vagy beágyazott előtt további műveletek is szerkeszthető.  

Azt a fentiekben leírtuk, mint amikor áttelepítése séma tekintse át az áttelepítés, amely megváltoztatja az eszköz által végrehajtott annak érdekében, hogy, hogy megértette őket.  

## <a name="migrate-data"></a>Adatok áttelepítése
Az adatok áttelepítése lehetőségre kattintva létrehozhat BCP olyan parancsfájlok, amelyek helyezi át az adatokat először a kiszolgálón egybesimított fájlokba, majd közvetlenül az SQL Data Warehouse be. Javasoljuk, hogy ez a folyamat áthelyezésére kis mennyiségű adatokat, és az újrapróbálkozások nincsenek beépített, majd a hibák akkor fordulhat elő, ha a hálózati kapcsolat hiánya. Ez futtatásához meg kell rendelkeznie a telepített BCP parancssori segédprogram, és a sémát kell már létrejött.

Miután a fenti paraméterek kitöltött egyszerűen kattintson a Futtatás áttelepítési, és két csomagok készlete hoz létre a megadott helyen. Adatok exportálása az áttelepítési forrásról egybesimított fájlok az exportfájl futtatásával, és az importált fájl futtatásával importálja az adatokat az SQL Data Warehouse.

## <a name="next-steps"></a>Következő lépések
Most, hogy néhány adat áttelepítése, ellenőrizze, hogy miként [fejlesztése][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
