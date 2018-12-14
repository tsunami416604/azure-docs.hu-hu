---
title: A HDInsight - Azure WebHCat-hibák megértése és megoldása
description: Ismerje meg, hogyan körülbelül gyakran előforduló hibák által visszaadott WebHCat a HDInsight és azok megoldását.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 0d27b16690516becc586678f8cf3edea234feeea
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383398"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>A HDInsight a webhcat használatával fogadott hibák megértése és megoldása

Ismerje meg hibaüzenetek érkeztek az WebHCat használata a HDInsight és azok megoldását. WebHCat belsőleg ügyféloldali eszközök, például az Azure PowerShell és a Data Lake Tools for Visual Studio.

## <a name="what-is-webhcat"></a>Mit jelent a WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) egy olyan REST API a [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), amely egy táblázat, valamint a tárolási réteg az Apache Hadoop. WebHCat a HDInsight-fürtökön alapértelmezés szerint engedélyezve van, és feladatok elküldéséhez, a feladat állapotát, stb. a fürthöz való bejelentkezés nélkül használják különböző eszközöket.

## <a name="modifying-configuration"></a>Konfigurációjának módosítása

> [!IMPORTANT]  
> A jelen dokumentumban felsorolt hibákat számos elő, amikor a rendszer túllépte a beállított maximális értéket. Ha a megoldás lépés említi, hogy egy érték módosítható, kell használnia az alábbiak egyikét a módosítás végrehajtásához:

* A **Windows** fürtök: Használjon szkriptműveletet az érték beállításához a fürt létrehozása során. További információkért lásd: [szkriptműveletek fejlesztése](hdinsight-hadoop-script-actions.md).

* A **Linux** fürtök: Az Apache Ambari (webes vagy a REST API), módosítsa az értéket használja. További információkért lásd: [kezelése HDInsight az Apache Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Túllépik a következő alapértelmezett értékek, ha azt WebHCat teljesítményét, vagy hibákat okoznak:

| Beállítás | Művelet | Alapértelmezett érték |
| --- | --- | --- |
| [yarn.Scheduler.Capacity.maximum – alkalmazások][maximum-applications] |Aktív lehet egyidejű feladatok maximális száma (függőben lévő vagy futó) |10,000 |
| [templeton.exec.max-procs][max-procs] |Is kiszolgálható kérelmek maximális száma |20 |
| [mapreduce.jobhistory.max ezredmásodperces kora][max-age-ms] |A feladatelőzmények napok számának megmaradnak. |7 nap |

## <a name="too-many-requests"></a>Túl sok a kérelem

**HTTP-állapotkód**: 429

| Ok | Megoldás: |
| --- | --- |
| Túllépte az egyidejű kérések maximális / perc (alapértelmezett érték 20) WebHCat által kiszolgált |Csökkentse az egyidejű kérelmek maximális száma nagyobb a számítási feladat annak biztosítása érdekében, hogy Ön nem küldenek, vagy növelje a párhuzamos kérés módosításával `templeton.exec.max-procs`. További információkért lásd: [konfiguráció módosítása](#modifying-configuration) |

## <a name="server-unavailable"></a>A kiszolgáló nem érhető el

**HTTP-állapotkód**: 503

| Ok | Megoldás: |
| --- | --- |
| Ezzel az állapotkóddal általában akkor fordul elő az elsődleges és másodlagos Átjárócsomópont a fürt közötti feladatátvétel során |Várjon két percet, majd próbálja megismételni a műveletet |

## <a name="bad-request-content-could-not-find-job"></a>Hibás kérelem tartalma: Nem található a feladat

**HTTP-állapotkód**: 400

| Ok | Megoldás: |
| --- | --- |
| Feladat részletei törölve lettek a feladatelőzmények által iterálásakor |Feladatelőzmények megőrzési idő alapértelmezés szerint 7 nap. Az alapértelmezett megőrzési időtartamot is módosítható módosításával `mapreduce.jobhistory.max-age-ms`. További információkért lásd: [konfiguráció módosítása](#modifying-configuration) |
| Feladat leállította a feladatátvétel miatt |Ismételje meg a feladat elküldése két percre |
| Egy érvénytelen feladatazonosító lett megadva. |Ellenőrizze, hogy helyes-e-e a feladat azonosítója |

## <a name="bad-gateway"></a>Hibás átjáró

**HTTP-állapotkód**: 502

| Ok | Megoldás: |
| --- | --- |
| Belső szemétgyűjtés történik, a WebHCat folyamaton belül |Várjon, amíg befejeződik, vagy indítsa újra a WebHCat szolgáltatást szemétgyűjtés |
| Az erőforrás-kezelő szolgáltatás válaszára várakozás időkorlátja. Ez a hiba akkor fordulhat elő, ha aktív kérelmek száma a beállított maximális értéket (alapértelmezés: 10 000) |Várjon, amíg a jelenleg futó feladatok elvégzéséhez, vagy növelheti az egyidejű feladat módosításával `yarn.scheduler.capacity.maximum-applications`. További információkért lásd: a [módosítása konfigurációs](#modifying-configuration) szakaszban. |
| Minden feladat keresztül beolvasására tett kísérlet a [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) hívás közben `Fields` értékre van állítva `*` |Nem kérnek le *összes* feladat részletei. Helyette használjon `jobid` bizonyos feladatazonosító csak nagyobb feladatok részleteinek lekérése. És ne használja `Fields` |
| Átjárócsomópont feladatátvevő során leállt a WebHCat-szolgáltatás |Két percig várjon, majd próbálja megismételni a műveletet |
| Nincsenek a Webhcaten keresztül elküldött több mint 500 függőben lévő feladatok |Várjon, amíg az aktuálisan folyamatban lévő feladat befejeződött, további feladatok elküldése előtt |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
