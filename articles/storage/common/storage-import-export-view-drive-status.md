---
title: Az Azure Import/Export-feladatok állapotának megtekintése |} A Microsoft Docs
description: Ismerje meg az Import/Export-feladatok és a használt meghajtókat állapotának megtekintése.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467747"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Azure Import/Export-feladatok állapotának megtekintése

Ez a cikk információt nyújt az Azure Import/Export-feladatok a meghajtót, és a feladat állapotának megtekintéséhez. Az Azure Import/Export szolgáltatás segítségével biztonságosan továbbíthat nagy mennyiségű adat Azure-Blobok és az Azure Files. A szolgáltatás az adatok exportálása az Azure Blob storage-ból is szolgál.  

## <a name="view-job-and-drive-status"></a>Feladat és a meghajtó állapotának megtekintése
Az importálás állapotának nyomon követése, vagy a feladatok exportálása az Azure Portalról. Kattintson a **Import/Export** fülre. A feladatok listáját az oldalon jelenik meg.

![Feladat állapotának megtekintése](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Feladat állapotának megtekintése

Attól függően, a meghajtó az a folyamat a következő feladat állapotok valamelyikét látja.

| Feladat állapota | Leírás |
|:--- |:--- |
| Létrehozás | Egy feladat létrehozása után az állapot értéke **létrehozása**. A feladat, amíg a **létrehozása** állapotba, az Import/Export szolgáltatás feltételezi, hogy rendelkezik a meghajtók nem elküldtük Önnek az adatközpontban. Egy feladat legfeljebb két hét, amely után automatikusan törli a szolgáltatás ebben az állapotban maradhatnak. |
| Szállítás | Miután a csomag szállít, frissítenie kell a követési információkat az Azure Portalon.  Ez a feladat be bekapcsolja **szállítási** állapota. A feladat marad a **szállítási** két héttel állapot. 
| Megérkezett | Miután az összes meghajtó érkezik, az adatközpontban, a feladat állapotának értéke **fogadott**. |
| Átvitel | Miután legalább egy meghajtó már megkezdődött a feldolgozás, a feladat állapotának beállítása **átadása**. További információért ugorjon [meghajtó állapota](#view-drive-status). |
| Csomagolás | Miután az összes meghajtó feldolgozása befejeződött, a feladat kerül **csomagolási** állapot, amíg vissza a meghajtók tartalmazza a szükséges. |
| Befejezve | Miután az összes meghajtó szállítjuk vissza, ha a feladat hiba nélkül befejeződött, akkor a feladat értéke **befejezve**. A feladatot a rendszer automatikusan törli a 90 nap után a **befejezve** állapota. |
| Lezárva | Miután az összes meghajtó szállítjuk vissza, ha a feladat feldolgozása során hibákat, a feladat értékre van állítva **lezárva**. A feladatot a rendszer automatikusan törli a 90 nap után a **lezárva** állapota. |

## <a name="view-drive-status"></a>A meghajtók állapotának megtekintése

Az alábbi táblázat ismerteti a meghajtók egyéni életciklusának, azt egy importálási vagy exportálási feladatot keresztül értékre vált. Minden olyan meghajtó, a feladat jelenlegi állapota látható az Azure Portalon.

A következő táblázat ismerteti az egyes állapot esetében egy feladat minden olyan meghajtó mehet keresztül.

| Meghajtó állapota | Leírás |
|:--- |:--- |
| A megadott | Importálási feladatokhoz, ha a feladat jön létre az Azure Portalról, a kezdeti meghajtó állapota **megadott**. Exportálási feladatokhoz, mivel nincs meghajtó van megadva, a feladat létrehozásakor a kezdeti meghajtó állapota **fogadott**. |
| Megérkezett | A meghajtó átkerül a **fogadott** állapot, amikor az Import/Export szolgáltatás feldolgozta a szállító cég importálási feladatokhoz érkezett meghajtókat. Exportálási feladatokhoz, a kezdeti meghajtó állapota a **fogadott** állapota. |
| NeverReceived | A meghajtó áthelyezi a **NeverReceived** állapot, amikor egy feladat a csomag érkezik, de a csomag nem tartalmazza a meghajtó. A meghajtó is helyezi ebbe az állapotba, ha két hét volt, mert a szolgáltatás a kézbesítési kapott, de a csomag még nem érkezett meg a datacenter. |
| Átvitel | A meghajtó áthelyezése a **átadása** állapotba, ha a szolgáltatás elkezdi a meghajtóról adatok átviteléhez az Azure Storage. |
| Befejezve | A meghajtó áthelyezése a **befejezve** állapotba, ha a szolgáltatás sikeresen át lett adva hiba nélkül az adatok.
| CompletedMoreInfo | A meghajtó áthelyezése a **CompletedMoreInfo** állapotba, ha a szolgáltatás észlelt bizonyos problémák a meghajtóra vagy az adatok másolása közben. Az adatok között szerepelhetnek, hibák, figyelmeztetések és információs üzenetek blobok felülírására.
| ShippedBack | A meghajtó áthelyezése a **ShippedBack** állapota akkor azt elküldtük Önnek az adatközpontból a visszatérési címre. |

Az Azure Portalon a lemezkép egy példa feladat meghajtó állapotát jeleníti meg:

![Meghajtó állapotának megtekintése](./media/storage-import-export-service/drivestate.png)

A következő táblázat ismerteti a meghajtó hiba állapotait és az egyes állapotokhoz szinteknél végrehajtott műveleteket.

| Meghajtó állapota | Esemény | Megoldás / a következő lépés |
|:--- |:--- |:--- |
| NeverReceived | Olyan meghajtón, amely van megjelölve **NeverReceived** (mert nem érkezett a feladat szállításáról részeként) egy másik szállítmány érkezik. | Az üzemeltetési csapat áthelyezi a meghajtót **fogadott**. |
| – | Olyan meghajtón, amely nem tartozik minden olyan feladat megérkezik az adatközpontban egy másik feladat részeként. | A meghajtó egy extra meghajtón van megjelölve, és Önnek a az eredeti csomaggal kapcsolatos feladat elvégzésekor adja vissza. |

## <a name="time-to-process-job"></a>Feldolgozási feladat időtartama
Importálási/exportálási feladatok feldolgozásához szükséges idő például tényező alapján változik:

-  Szállítási idő
-  Az Adatközpont betöltése
-  Feladat típusa és az adatok másolását mérete
-  Egy feladat a lemezek számát. 

Importálási/exportálási szolgáltatás nem rendelkezik SLA-t, de a szolgáltatás nagy hangsúlyt fektet a 7-10 napos lemezek vannak fogadását követően a másolás végrehajtásához. Az Azure Portal webhelyen közzétett állapota REST API-k segítségével nyomon követheti a feladat előrehaladását. Az Állapot paraméter az [listázhatók a feladatok](/previous-versions/azure/dn529083(v=azure.100)) API művelet meghívásának biztosít a százalékos másolása folyamatban van.


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatok áthelyezése az AzCopy parancssori segédprogram](storage-use-azcopy.md)
* [Az Azure importálási exportálása REST API-minta](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
