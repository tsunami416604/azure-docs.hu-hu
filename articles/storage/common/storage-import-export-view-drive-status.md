---
title: Azure importálási/exportálási feladatok állapotának megtekintése |} Microsoft Docs
description: Ismerje meg az importálási/exportálási feladatok és a használt meghajtókat állapotának megtekintése.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 176cbf190b6442682a222eb4f24b6583fb87a46b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660882"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Azure importálási/exportálási feladatok állapotának megtekintése

Ez a cikk bemutatja, hogy miként az Azure importálási/exportálási feladatok meghajtót és a feladat állapotának megtekintéséhez. Az Azure Import/Export szolgáltatás segítségével biztonságosan nagy mennyiségű adat átvitele Azure Blobok és Azure fájlokat. A szolgáltatás segítségével exportál adatokat az Azure Blob Storage tárolót is szolgál.  

## <a name="view-job-and-drive-status"></a>Feladat és a meghajtó állapotának megtekintése
Az importálás állapotának nyomon követése, vagy exportálni a feladatokat az Azure portálról. Kattintson a **Import/Export** fülre. A feladatokra lapján jelenik meg.

![Feladat állapotának megtekintése](./media/storage-import-export-service/jobstate.png)


## <a name="view-job-status"></a>Feladat állapotának megtekintése

Attól függően, hogy a meghajtó az a folyamat a következő feladatállapotok egyikét látja.

| Feladat állapota | Leírás |
|:--- |:--- |
| Létrehozás | A feladat létrehozása után állapotában értéke **létrehozása**. Amíg a feladat a **létrehozása** állapotba kerül, az Import/Export szolgáltatás azt feltételezi, hogy rendelkezik a meghajtók nem lett kiadva az adatközpontban. Egy feladat két héttel, amely után a rendszer automatikusan törli a szolgáltatás ebben az állapotban maradhat. |
| Szállítás | Miután a csomag küldje el, frissítenie kell a nyomkövetési információk az Azure portálon.  Ez a feladat be bekapcsolása **szállítási** állapotát. A feladat marad a **szállítási** két héttel állapot. 
| Érkezett | Miután az összes meghajtó az Adatközpont érkeznek, a feladat állapota értéke **fogadott**. |
| Átvitel | Miután legalább egy meghajtó már megkezdődött a feldolgozás, a feladat állapota értéke **átadása**. További információkért látogasson el [meghajtó állapotok](#view-drive-status). |
| Csomagolás | Után az összes meghajtó feldolgozása befejeződött, a feladat kerül **csomagolás** állapot, amíg a meghajtók szállítják vissza. |
| Befejeződött | Miután az összes meghajtó szállított vissza, ha a feladat hiba nélkül befejeződött, akkor a feladat értéke **befejezve**. A feladatot a rendszer automatikusan törli a 90 nap után a **befejezve** állapotát. |
| Lezárva | Miután az összes meghajtó szállított vissza, ha a feladat feldolgozása során hiba történt, a feladat beállítása **lezárva**. A feladatot a rendszer automatikusan törli a 90 nap után a **lezárva** állapotát. |

## <a name="view-drive-status"></a>Meghajtó állapotának megtekintése

Az alábbi táblázat ismerteti a meghajtók egyéni életciklusát, akkor átkerül egy importálási vagy exportálási feladat keresztül. Minden olyan meghajtó egy feladat aktuális állapotát az Azure-portálon látható.

A következő táblázat ismerteti az egyes állapot esetében egy feladat minden olyan meghajtó továbbítása is.

| Meghajtó állapotát | Leírás |
|:--- |:--- |
| A megadott | Az importálási feladatnak a feladat létrehozásakor az Azure-portálon, a kezdeti egy meghajtó állapota **megadott**. Exportálási feladat, a meghajtó nem adható meg a feladat jön létre, mert a kezdeti meghajtó állapota **fogadott**. |
| Érkezett | A meghajtó közeledik, hogy a **fogadott** állapot, amikor az Import/Export szolgáltatás feldolgozta-e a meghajtókat, hogy az importálás a szállítási vállalati érkezett. Az exportálási feladat, a kezdeti meghajtó van a **fogadott** állapotát. |
| NeverReceived | A meghajtó áthelyezése a **NeverReceived** feladat csomag megérkeznek, de a csomag nem tartalmaz a meghajtó állapotát. Ha két héten lett, mert a szolgáltatás a szállítási adatokat kapott, de a csomag még nem érkezett meg a datacenter, a meghajtó is felkerült ebben az állapotban. |
| Átvitel | A meghajtó áthelyezése a **átadása** állapot, ha a szolgáltatás megkezdi az adatok átvitele Azure Storage a meghajtót. |
| Befejeződött | A meghajtó áthelyezése a **befejezve** állapot, ha a szolgáltatás sikeresen átadta minden az adatok hibásak.
| CompletedMoreInfo | A meghajtó áthelyezése a **CompletedMoreInfo** állapot, ha a szolgáltatás észlelt problémákat adatok másolása az vagy a meghajtóra. Az információk között szerepelhet hibák, figyelmeztetések és információs üzenetek blobok felülírására.
| ShippedBack | A meghajtó áthelyezése a **ShippedBack** állapota akkor azt teljesítették a az adatközpontban a visszatérési címre. |

Az Azure-portálon a lemezkép egy példa feladat meghajtó állapotát jeleníti meg:

![Meghajtó állapotának megtekintése](./media/storage-import-export-service/drivestate.png)

A következő táblázat ismerteti a meghajtó hiba állapotok és az egyes állapotokhoz végrehajtott műveleteket.

| Meghajtó állapotát | Esemény | Megoldás / a következő lépés |
|:--- |:--- |:--- |
| NeverReceived | A jelölés szerint meghajtót **NeverReceived** (mert nem érkezett a feladat szállítási részeként) egy másik szállítási érkezik. | A műveleti csapata helyezi át a meghajtó **fogadott**. |
| – | Egy meghajtót, amely nem része a feladat egy másik feladat részeként datacenter érkezik. | A meghajtó egy extra meghajtó van megjelölve, és küld vissza, ha az eredeti csomagot társított feladat befejeződött. |

## <a name="time-to-process-job"></a>Feldolgozási feladatot az idő
Importálási/exportálási feladatok feldolgozásához szükséges időt, mint ez számos tényezőtől függően változik:

-  Szállítási idő
-  Az adatközpontjában betöltése
-  Feladat típusa és az adatok másolásának mérete
-  A feladatok lemezek számát. 

Import/Export szolgáltatás nem rendelkezik egy SLA-t, de a szolgáltatás nagy hangsúlyt fektet, a Másolás befejezése 7 – 10 nap, a lemezek fogadása után. Az Azure portálon közzé állapota REST API-k segítségével nyomon követheti a feladat előrehaladását. A készültségi paramétere a [lista feladatok]() művelet API-hívás biztosít a másolási előrehaladás százalékosan kifejezve.


## <a name="next-steps"></a>További lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatátvitel az AzCopy parancssori segédprogram](storage-use-azcopy.md)
* [Az Azure importálási exportálása REST API minta](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

