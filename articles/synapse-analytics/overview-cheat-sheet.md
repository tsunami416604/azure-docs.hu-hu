---
title: Cheat sheet - Azure Synapse Analytics
description: Útmutató a felhasználónak az Azure Synapse Analytics szolgáltatásban
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424656"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Az Azure Synapse Analytics csalási lapja

[!INCLUDE [preview](includes/note-preview.md)]

Az Azure Synapse Analytics cheat sheet végigvezeti a szolgáltatás alapfogalmait és a fontos parancsokat. Ez a cikk hasznos mind az új tanulók, és azok számára, akik szeretnék kiemeli az alapvető Azure Synapse témakörök.

## <a name="architecture"></a>Architektúra

> [!div class="mx-imgBorder"]
>![Szinapszis építészet](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Alapelvek
| Főnevek és igék                         | Művelet       |
|:---                                 |:---                 |
| **Szinapszis munkaterület (előzetes verzió)** | Biztonságos együttműködési határ az Azure felhőalapú vállalati elemzéseinek biztosításához. A munkaterület egy adott régióban van telepítve, és társított ADLSg2 fiókkal és fájlrendszerrel rendelkezik (ideiglenes adatok tárolására). A munkaterület egy erőforráscsoport alatt van. |
| **SQL Analytics**   | Elemzéseket futtathatok készletekkel vagy igény szerinti képességekkel.  |
| **SQL-készlet**   | 0-N SQL kiépített erőforrások a megfelelő adatbázisokat lehet telepíteni egy munkaterületen. Minden SQL-készlethez tartozik egy adatbázis. Az SQL-készlet manuálisan vagy automatikusan méretezhető, szüneteltethető és folytatható. Egy SQL-készlet 100 DWU-ról 30 000 DWU-ra skálázható.       |
| **SQL igény szerinti (előzetes verzió)**   | Nagyméretű adatokhoz készült elosztott adatfeldolgozó rendszer, amely lehetővé teszi a T-SQL-lekérdezések futtatását a Data Lake adatai felett. Kiszolgáló nélküli, így nem kell kezelnie az infrastruktúrát.       |
|**Apache Spark** | Spark-futási idő egy Spark-készletben használt. A jelenlegi támogatott verzió a Spark 2.4 Python 3.6.1, Scala 2.11.12, .NET támogatás apache spark 0.5 és Delta Lake 0.3.  | 
| **Apache Spark-készlet (előzetes verzió)**  | 0-n Spark kiépített erőforrások a megfelelő adatbázisokat lehet telepíteni egy munkaterületen. A Spark-készlet automatikusan szüneteltethető, folytatható és méretezhető.  |
| **Spark alkalmazás**  |   Ez egy illesztőprogram-folyamatból és egy sor végrehajtó folyamatból áll. A Spark-alkalmazás fut egy Spark-készleten.            |
| **Spark-munkamenet**  |   Egy szikraalkalmazás egyesített belépési pontja. Lehetővé teszi a Spark különböző funkcióival és kisebb számú konstrukcióval való interakciót. A jegyzetfüzet futtatásához létre kell hozni egy munkamenetet. A munkamenet beállítható úgy, hogy adott számú, adott méretű végrehajtón fusson. A jegyzetfüzet-munkamenet alapértelmezett konfigurációja 2 közepes méretű végrehajtón való futtatás. |
| **SQL-kérelem**  |   A művelet, például egy lekérdezés sql készleten vagy sql-on-igény. |
|**Adatintegráció**| Lehetővé teszi a különböző források közötti adatok betöltését és a munkaterületen vagy a munkaterületen kívül futó tevékenységek vezénylését.| 
|**Összetevők**| Koncepció, amely magában foglalja az adatforrások kezeléséhez, fejlesztéséhez, vezényléséhez és megjelenítéséhez szükséges összes objektumot.|
|**Jegyzetfüzet**| A Scala, a PySpark, a C#és a SparkSQL interaktív és reaktív adattudományi és mérnöki felülete. |
|**Spark feladatdefiníció**|Felület a Spark-feladat elküldéséhez a kód és a függőségek tartalmazó szerelvény jar.|
|**Adatfolyam**|  Teljes vizuális élményt nyújt, és nincs szükség kódolásra a big data átalakításához. Minden optimalizálás és végrehajtás kezelése a szerver nélküli módon. |
|**SQL-szkript**| Fájlba mentett SQL-parancsok készlete. Egy SQL-parancsfájl egy vagy több SQL utasítást is tartalmazhat. Sql-kérelmek sql készleten vagy igény szerinti SQL-en keresztül történő futtatására használható.|
|**Folyamat**| A tevékenységeket egymással együtt végző tevékenységek logikai csoportosítása.|
|**Tevékenység**| Olyan műveleteket határoz meg, amelyeket olyan adatokon hajt végre, mint az adatok másolása, jegyzetfüzet vagy SQL parancsfájl futtatása.|
|**Eseményindító**| Folyamat végrehajtása. Ez lehet fuss kézi vagy gépiesen ( táblázat, bukdácsoló ablak vagy esemény- kiindulópontul szolgáló).|
|**Társított szolgáltatások**| Kapcsolati karakterláncok, amelyek meghatározzák a munkaterület külső erőforrásokhoz való csatlakozásához szükséges kapcsolati adatokat.|
|**Adatkészlet**|  Azon adatok elnevezett nézete, amelyek egyszerűen rámutatnak vagy hivatkoznak a tevékenységben bemenetként és kimenetként használandó adatokra. Egy csatolt szolgáltatáshoz tartozik.|

## <a name="next-steps"></a>További lépések

- [Munkaterület létrehozása](quickstart-create-workspace.md)
- [A Synapse Studio használata](quickstart-synapse-studio.md)
- [SQL-készlet létrehozása](quickstart-create-sql-pool.md)
- [Sql igény szerinti használata](quickstart-sql-on-demand.md)
- [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool.md)

