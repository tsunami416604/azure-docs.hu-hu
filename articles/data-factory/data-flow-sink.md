---
title: Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése
description: Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 795b8072bbd9b248f982d061d699f490b1b63b17
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271867"
---
# <a name="azure-data-factory-mapping-data-flow-sink-transformation"></a>Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Fogadó-beállítások](media/data-flow/windows1.png "fogadó 1")

Az Adatátalakítási folyamat befejezése után az átalakított adatok is fogadó célkiszolgáló adatkészletekbe. A fogadó átalakítási választhatja ki, amely a cél kimeneti adatok használni kívánt adatkészlet-definícióban.

Egy fiókra a bejövő adatok módosítása és a számla séma eltéréseket az általános gyakorlat, hogy a kimeneti adatokat fogadó egy mappába a kimeneti adatkészletben definiált séma nélkül. Meg is emellett a fiók az összes oszlop változáshoz a forrásokban "Engedélyezése séma eltéréseket" a forrásnál, és a automatikus hozzárendelés kijelölésével a fogadó található összes mezőhöz.

Ha szeretné, felülírja, hozzáfűzése, vagy az adatok az adatfolyam sikertelen, ha az Elhelyezés adatkészletre.

Választhatja "automatikus hozzárendelés" fogadó minden bejövő mezőt is. Ha szeretné, hogy a cél fogadó kívánt mezők kiválasztása, vagy ha szeretné módosítani a célhely a mezők nevében, válassza a "automatikus"hozzárendelés "Off", és a kimeneti mezők leképezése a leképezési lapon kattintson:

![Fogadó-beállítások](media/data-flow/sink2.png "fogadó 2")

## <a name="output-to-one-file"></a>Egy fájl kimeneti
Az Azure Storage Blob vagy a Data Lake fogadó típusait az átalakított adatokat fog kimeneti mappába. A Spark használt particionálási séma alapján particionált kimeneti adatfájlokat hoz létre a fogadó átalakítása. Beállíthatja a particionálási sémát az "Optimalizálás" fülre kattintva. Ha szeretné, hogy a kimeneti egyesíthet egyetlen fájl ADF, kattintson a "Egypartíciós" gombra.

![Fogadó-beállítások](media/data-flow/opt001.png "fogadó-beállítások")

## <a name="blob-storage-folder"></a>Blob Storage Folder
Ha a adatátalakítások Elhelyezés a Blob Store, válassza a blob *mappa* , a célmappa elérési útja, nem fájl. ADF-adatfolyam létrehozza a kimeneti fájlok az Ön számára abban a mappában.

![Mappa elérési útja](media/data-flow/folderpath.png "mappa elérési útja")

## <a name="optional-azure-sql-data-warehouse-sink"></a>Nem kötelező az Azure SQL Data Warehouse fogadó

Szeptemberben egy korai béta ADW Sink DataSet adatfolyam. Ez lehetővé teszi, hogy megnyitja az átalakított adatok közvetlenül az Azure SQL DW belül adatfolyam nincs szükség a másolási tevékenység hozzáadása a folyamatban.

Először hozzon létre egy ADW adatkészlethez, ugyanúgy, mint bármely más ADF folyamat, és egy társított szolgáltatást, amely tartalmazza a ADW hitelesítő adatait, és válassza ki az adatbázist, amelyet csatlakozni kíván. A táblázat neve vagy válasszon egy meglévő táblát, vagy írja be a táblázat nevét, hogy milyen adatfolyam a bejövő mezőket, az automatikus létrehozásához.

![Fogadó-beállítások](media/data-flow/adw3.png "fogadó 3")

Jelentkezzen be a fogadó tranformation (ADW jelenleg csak a támogatott fogadóként) fog választani, az Ön által létrehozott és a Storage-fiókot szeretné használni az átmeneti a polybase az adatok betöltése az ADW ADW adatkészlet. Az elérési út mezőben a következő formátumban van: "containername/Mappanév".

![Fogadó-beállítások](media/data-flow/adw1.png "fogadó 4")

### <a name="save-policy"></a>Házirend mentése

Felülírással csonkolja a táblából, ha létezik, hozza létre újra, és betöltheti az adatokat. Fűzze hozzá a rendszer az új sor beszúrásához. Ha az adatkészlet-tábla neve a tábla nem létezik egyáltalán a célzott ADW, annak adatfolyam a tábla létrehozásához, majd az adatok betöltéséhez.

Ha törli az "Automatikus térkép", leképezheti a mezőket a céltábla manuálisan.

![Fogadó ADW beállítások](media/data-flow/adw2.png "adw fogadó")

### <a name="max-concurrent-connections"></a>Max. egyidejű kapcsolatok

A fogadó átalakításában szerepel az egyidejű kapcsolatok maximális száma állíthatja be az adatokat egy Azure adatbázis-kapcsolat írásakor.

![Csatlakozási lehetőségek](media/data-flow/maxcon.png "kapcsolatok")
