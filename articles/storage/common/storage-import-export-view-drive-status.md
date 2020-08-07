---
title: Azure import/export-feladatok állapotának megtekintése | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg az Azure import/export-feladatok és a használt meghajtók állapotát. Ismerje meg azokat a tényezőket, amelyek befolyásolják, hogy mennyi ideig tart a feladatok feldolgozása.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: bf44b24b9222072bf5f1493b36b0dfcca24b5389
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923611"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Az Azure Import/Export-feladatok állapotának megtekintése

Ez a cikk azt ismerteti, hogyan lehet megtekinteni a meghajtó és a feladat állapotát az Azure import/export feladatok számára. Az Azure import/export szolgáltatás használatával nagy mennyiségű adatok biztonságosan vihetők át az Azure-Blobokra és a Azure Filesra. A szolgáltatás az Azure Blob Storage-ból származó adatok exportálására is használható.  

## <a name="view-job-and-drive-status"></a>Feladatok és meghajtók állapotának megtekintése
Az importálási vagy exportálási feladatok állapotát a Azure Portal követheti nyomon. Kattintson az **Importálás/exportálás** fülre. A feladatok listája megjelenik az oldalon.

![Feladatok állapotának megtekintése](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Feladat állapotának megtekintése

A következő feladatok egyike jelenik meg attól függően, hogy a meghajtó hol található a folyamatban.

| Feladatok állapota | Leírás |
|:--- |:--- |
| Létrehozás | A feladatok létrehozása után az állapota a **Létrehozás**értékre van állítva. Amíg a feladatsor **létrehozza** az állapotot, az importálási/exportálási szolgáltatás feltételezi, hogy a meghajtókat nem szállították el az adatközpontba. A feladatok akár két hétig is maradhatnak ebben az állapotban, ami után a szolgáltatás automatikusan törli azt. |
| Szállítás | A csomag szállítása után frissítenie kell a követési információkat a Azure Portal.  Ez a feladatot **szállítási** állapotba kapcsolja. A feladatok legfeljebb két hétig maradnak a **szállítási** állapotban. 
| Megérkezett | Miután az összes meghajtó beérkezett az adatközpontba, a feladatsor értéke **fogadott**. |
| Átadó | Ha legalább egy meghajtó megkezdte a feldolgozást, a feladattípus az **átvitelre**van beállítva. További információért lépjen a [meghajtó állapota](#view-drive-status)elemre. |
| Csomagolás | Miután az összes meghajtó befejezte a feldolgozást, a rendszer **csomagolási** állapotba helyezi a feladatot, amíg a meghajtókat vissza nem szállítja. |
| Befejeződött | Miután az összes meghajtót visszaküldi a rendszer, ha a feladatot hibák nélkül végezte el, a rendszer **Befejezettre**állítja a feladatot. A feladatot a rendszer a **befejezett** állapot 90 napja után automatikusan törli. |
| Zárt | Miután az összes meghajtót visszaküldi Önnek, ha hiba történt a feladatok feldolgozásakor, a rendszer **Lezártra**állítja a feladatot. A feladatot a rendszer a 90 nap után automatikusan törli a **lezárt** állapotban. |

## <a name="view-drive-status"></a>A meghajtók állapotának megtekintése

Az alábbi táblázat az egyes meghajtók életciklusát mutatja be az importálási vagy exportálási feladatokhoz való áttérés során. A feladatok egyes meghajtóinak aktuális állapota a Azure Portal látható.

A következő táblázat ismerteti az egyes állapotokat, amelyeket a feladatok egyes meghajtói továbbítanak.

| Meghajtó állapota | Leírás |
|:--- |:--- |
| Megadott | Importálási feladatokhoz, ha a feladatot a Azure Portal hozza létre, a meghajtó kezdeti állapota meg van **adva**. Exportálási feladatokhoz, mivel a rendszer nem ad meg meghajtót a feladatok létrehozásakor, a rendszer a kezdeti meghajtó állapotát **fogadja**. |
| Megérkezett | A meghajtó átvált a **fogadott** állapotba, amikor az importálási/exportálási szolgáltatás feldolgozta az importálási feladatokhoz a hajózási vállalattól érkezett meghajtókat. Exportálási feladatok esetén a kezdeti meghajtó állapota a **fogadott** állapot. |
| NeverReceived | A meghajtó a **NeverReceived** állapotba kerül, amikor a csomag egy adott feladatokhoz érkezik, de a csomag nem tartalmazza a meghajtót. A meghajtó akkor is ebbe az állapotba kerül, ha két hét telt el, mivel a szolgáltatás megkapta a szállítási adatokat, de a csomag még nem érkezett meg az adatközpontban. |
| Átadó | A meghajtó áthelyezi az **átadási** állapotba, amikor a szolgáltatás megkezdi az adatok átvitelét a meghajtóról az Azure Storage-ba. |
| Befejeződött | A meghajtó a **befejezett** állapotba kerül, ha a szolgáltatás sikeresen áthelyezte az összes olyan hibát, amely hibák nélkül történt.
| CompletedMoreInfo | A meghajtó a **CompletedMoreInfo** állapotba kerül, amikor a szolgáltatás valamilyen hibát észlelt az adatoknak a vagy a meghajtóra történő másolása közben. Az információ tartalmazhat hibákat, figyelmeztetéseket és tájékoztató üzeneteket a Blobok felülírásával kapcsolatban.
| ShippedBack | A meghajtó a **ShippedBack** állapotba kerül, amikor az adatközpontból a visszaküldési címnek visszaszállították. |

Ez a rendszerkép a Azure Portal egy példaként szolgáló feladatokhoz tartozó meghajtó állapotát jeleníti meg:

![Meghajtó állapotának megtekintése](./media/storage-import-export-service/drivestate.png)

Az alábbi táblázat a meghajtó meghibásodási állapotait és az egyes állapotokhoz végrehajtott műveleteket ismerteti.

| Meghajtó állapota | Esemény | Megoldás/következő lépés |
|:--- |:--- |:--- |
| NeverReceived | Egy **NeverReceived** jelölésű meghajtó (mert nem érkezett a feladatokhoz a feladatainak részeként) egy másik szállítmányban érkezik. | Az operatív csapat áthelyezi a meghajtót a **fogadásba**. |
| N/A | Egy olyan meghajtó, amely nem része a feladatoknak, egy másik feladattípus részeként az adatközpontba kerül. | A meghajtó külön meghajtóként van megjelölve, és az eredeti csomaghoz társított feladatok befejezése után visszaadja. |

## <a name="time-to-process-job"></a>A feladatok feldolgozásának ideje
Az importálási/exportálási feladatok feldolgozásához szükséges idő számos tényezőtől függ, például:

-  Szállítási idő
-  Betöltés az adatközpontban
-  A másolandó adatok feladattípusa és mérete
-  Egy adott feladatokban található lemezek száma. 

Az importálási/exportálási szolgáltatás nem rendelkezik SLA-val, de a szolgáltatás arra törekszik, hogy a másolást 7 – 10 nappal a lemezek fogadása után végezze el. A Azure Portal közzétett állapot mellett a REST API-k segítségével nyomon követheti a feladatok előrehaladását. A [feladatok listázása](/previous-versions/azure/dn529083(v=azure.100)) művelet API-hívásának százalékos készültségi paramétere a másolási folyamat százalékos arányát adja meg.


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Azure importálási REST API minta](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
