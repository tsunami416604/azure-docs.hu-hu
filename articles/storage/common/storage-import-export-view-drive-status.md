---
title: Az Azure importálási/exportálási feladatainak állapotának megtekintése | Microsoft dokumentumok
description: További információ az importálási/exportálási feladatok és a használt meghajtók állapotának megtekintéséről.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72821442"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Az Azure Import/Export-feladatok állapotának megtekintése

Ez a cikk az Azure importálási/exportálási feladatok meghajtó- és feladatállapotának megtekintésével kapcsolatos információkat tartalmaz. Az Azure importálási/exportálási szolgáltatás segítségével biztonságosan továbbíthat nagy mennyiségű adatot az Azure Blobs és az Azure Files. A szolgáltatás is használják az adatok exportálására az Azure Blob storage.The service is used to export data from Azure Blob storage.  

## <a name="view-job-and-drive-status"></a>Feladat és meghajtó állapotának megtekintése
Az importálási vagy exportálási feladatok állapotát az Azure Portalon követheti nyomon. Kattintson az **Importálás/exportálás** fülre. A feladatok listája megjelenik az oldalon.

![Feladat állapotának megtekintése](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Feladat állapotának megtekintése

Attól függően, hogy a meghajtó hol van a folyamatban, az alábbi feladatállapotok egyike jelenik meg.

| Feladat állapota | Leírás |
|:--- |:--- |
| Létrehozás | A feladat létrehozása után az állapota **Létrehozás.** Amíg a feladat **létrehozási** állapotban van, az Importálás/exportálás szolgáltatás feltételezi, hogy a meghajtókat nem szállították az adatközpontba. Egy feladat legfeljebb két hétig maradhat ebben az állapotban, majd a szolgáltatás automatikusan törli azt. |
| Shipping | A csomag szállítása után frissítenie kell a nyomon követési információkat az Azure Portalon.  Ez a feladatot szállítási állapottá **alakítja.** A munka legfeljebb két hétig a **szállítási** állapotban marad. 
| Megérkezett | Miután az összes meghajtó tanévenként érkezett az adatközpontba, a feladat állapota **Beérkezett**lesz állítva. |
| Átvitele | Ha legalább egy meghajtó feldolgozása megkezdődött, a feladat állapota **Átkerülése**lesz. További információ: [Meghajtó-állapotok](#view-drive-status). |
| Csomagolás | Miután az összes meghajtó feldolgozása befejeződött, a feladat **csomagolási** állapotba kerül, amíg a meghajtókat vissza nem szállítják Önnek. |
| Befejezve | Miután az összes meghajtót visszaküldték Önnek, ha a feladat hiba nélkül befejeződött, akkor a feladat **befejeződött**. A feladat automatikusan törlődik 90 nap után a **Befejezett** állapotban. |
| Lezárt | Miután az összes meghajtót visszaszállítják Önnek, ha a feladat feldolgozása során bármilyen hiba történt, a feladat **zárt**. A feladat automatikusan törlődik 90 nap után **zárt** állapotban. |

## <a name="view-drive-status"></a>A meghajtók állapotának megtekintése

Az alábbi táblázat az egyes meghajtók életciklusát ismerteti, ahogy átvált egy importálási vagy exportálási feladaton keresztül. A feladat egyes meghajtóinak aktuális állapota az Azure Portalon látható.

Az alábbi táblázat azegyes állapotokat ismerteti, amelyeken a feladat egyes meghajtói áthaladhatnak.

| Meghajtó állapota | Leírás |
|:--- |:--- |
| Megadott | Importálási feladat esetén, amikor a feladat az Azure Portalon jön létre, a meghajtó kezdeti állapota **megadva van.** Exportálási feladat esetén, mivel a feladat létrehozásakor nincs megadva meghajtó, a kezdeti meghajtóállapota **Beérkezett.** |
| Megérkezett | A meghajtó **átvált a Beérkezett** állapotba, amikor az Importálás/exportálás szolgáltatás feldolgozta a hajózási társaságtól importálási feladathoz kapott meghajtókat. Exportálási feladat esetén a kezdeti meghajtóállapota a **Beérkezett** állapot. |
| Soha nem érkezett | A meghajtó a **NeverReceived** állapotba kerül, amikor egy feladat csomagja megérkezik, de a csomag nem tartalmazza a meghajtót. A meghajtó is ebbe az állapotba kerül, ha már két hét telt el azóta, hogy a szolgáltatás megkapta a szállítási információkat, de a csomag még nem érkezett meg az adatközpontba. |
| Átvitele | A meghajtó átkerül az **átviteli** állapotba, amikor a szolgáltatás elkezdi átvinni az adatokat a meghajtóról az Azure Storage-ba. |
| Befejezve | A meghajtó **a Befejezett** állapotba kerül, ha a szolgáltatás hiba nélkül sikeresen átvitte az összes adatot.
| CompletedMoreInfo | A meghajtó **az CompletedMoreInfo** állapotba kerül, ha a szolgáltatás problémákat észlelt az adatok nak a meghajtóról vagy a meghajtóra történő másolása közben. Az információk tartalmazhatnak hibákat, figyelmeztetéseket vagy a blobok felülírásával kapcsolatos tájékoztató üzeneteket.
| Visszaszállítás | A meghajtó akkor kerül a **Visszaszállítás** állapotba, ha az adatközpontból visszaszállították a feladó címére. |

Ez a lemezkép az Azure Portalon egy példafeladat meghajtóállapotát jeleníti meg:

![Meghajtó állapotának megtekintése](./media/storage-import-export-service/drivestate.png)

Az alábbi táblázat ismerteti a meghajtó meghibásodási állapotait és az egyes állapotok esetében végrehajtott műveleteket.

| Meghajtó állapota | Esemény | Felbontás / Következő lépés |
|:--- |:--- |:--- |
| Soha nem érkezett | Egy **NeverReceived** jelölésű meghajtó (mivel nem érkezett meg a feladat szállítmányának részeként) egy másik szállítmányba érkezik. | A műveleti csapat áthelyezi a meghajtót **a Beérkezett**meghajtóra. |
| N/A | Egy olyan meghajtó, amely nem része egyetlen feladatnak sem, egy másik feladat részeként érkezik az adatközpontba. | A meghajtó egy további meghajtóként van megjelölve, és az eredeti csomaghoz társított feladat befejezésekor visszakapja. |

## <a name="time-to-process-job"></a>Feladat feldolgozásának ideje
Az importálási/exportálási feladat feldolgozásához szükséges idő számos tényezőtől függ, például:

-  Szállítási idő
-  Betöltés az adatközpontban
-  A feladat típusa és a másolt adatok mérete
-  Lemezek száma egy feladatban. 

Az import/exportálás i szolgáltatás nem rendelkezik SLA-val, de a szolgáltatás arra törekszik, hogy a másolatot a lemezek fogadását követő 7–10 napon belül töltse ki. Az Azure Portalon közzétett állapot mellett a REST API-k is használhatók a feladat előrehaladásának nyomon követésére. A [Listafeladatok](/previous-versions/azure/dn529083(v=azure.100)) művelet API-hívásának készültségi szint paramétere biztosítja a másolás százalékos előrehaladását.


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatok átvitele az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Azure Import Export REST API-minta](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
