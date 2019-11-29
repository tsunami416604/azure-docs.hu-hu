---
title: Webhcaten-hibák megértése és megoldása a HDInsight-ben – Azure
description: Ismerkedjen meg a Webhcaten által a HDInsight-on visszaadott gyakori hibákkal és azok megoldásával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5c103482771b829730d009d65283a54ec1d8eb8a
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555019"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>A Webhcaten által a HDInsight-on fogadott hibák ismertetése és megoldása

További információ a Webhcaten és a HDInsight használatával fogadott hibákról, valamint azok megoldásáról. A Webhcaten az ügyféloldali eszközök, például a Azure PowerShell és a Visual Studio Data Lake eszközei belsőleg használják.

## <a name="what-is-webhcat"></a>Mi az a Webhcaten?

A [webhcaten](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a Apache Hadoop [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), táblázat-és tárolási felügyeleti rétegének REST API. A Webhcaten alapértelmezés szerint engedélyezve van a HDInsight-fürtökön, és számos eszköz használja a feladatok elküldésére, a feladat állapotára stb. a fürtbe való bejelentkezés nélkül.

## <a name="modifying-configuration"></a>Konfiguráció módosítása

> [!IMPORTANT]  
> A dokumentumban felsorolt hibák némelyike miatt a rendszer túllépte a beállított maximumot. Ha a megoldási lépés megemlíti, hogy módosítható egy érték, a módosítás végrehajtásához a következők egyikét kell használnia:

* **Windows** -fürtök esetén: parancsfájl-művelet használatával konfigurálja az értéket a fürt létrehozása során. További információ: parancsfájl- [műveletek fejlesztése](hdinsight-hadoop-script-actions-linux.md).

* **Linux** -fürtök esetén: az érték módosításához használja az Apache Ambari (web vagy REST API). További információ: [HDInsight kezelése Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)


### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Ha túllépi a következő alapértelmezett értékeket, akkor csökkenhet a Webhcaten teljesítménye, vagy hibákat okozhat:

| Beállítás | Funkció | Alapértelmezett érték |
| --- | --- | --- |
| [fonal. Scheduler. Capacity. max-Applications][maximum-applications] |A egyszerre aktív feladatok maximális száma (függőben vagy fut) |10,000 |
| [Templeton. exec. max-procs][max-procs] |Az egyszerre kiszolgálható kérelmek maximális száma |20 |
| [MapReduce. jobhistory. max-Age-MS][max-age-ms] |Azon napok száma, ameddig a feladatok előzményei megmaradnak |7 nap |

## <a name="too-many-requests"></a>Túl sok kérelem

**Http-állapotkód**: 429

| Ok | Felbontás |
| --- | --- |
| Túllépte a percenkénti Webhcaten által kiszolgált maximális egyidejű kéréseket (alapértelmezett érték: 20). |Csökkentse a munkaterhelést, és győződjön meg arról, hogy nem küldi el az egyidejű kérések maximális számát, vagy növelje az egyidejű kérések korlátját a `templeton.exec.max-procs`módosításával. További információ: [konfiguráció módosítása](#modifying-configuration) |

## <a name="server-unavailable"></a>A kiszolgáló nem érhető el

**Http-állapotkód**: 503

| Ok | Felbontás |
| --- | --- |
| Ez az állapotkód általában a fürt elsődleges és másodlagos Átjárócsomóponthoz közötti feladatátvétel során fordul elő |Várjon két percet, majd próbálja megismételni a műveletet. |

## <a name="bad-request-content-could-not-find-job"></a>Helytelen kérelem tartalma: nem található a feladattípus.

**Http-állapotkód**: 400

| Ok | Felbontás |
| --- | --- |
| A feladatok részleteit tisztábban megtisztították |A feladatok előzményeinek alapértelmezett megőrzési ideje 7 nap. Az alapértelmezett megőrzési időszak a `mapreduce.jobhistory.max-age-ms`módosításával módosítható. További információ: [konfiguráció módosítása](#modifying-configuration) |
| A feladatot egy feladatátvétel miatt meggyilkolták |Feladat beküldésének újrapróbálása legfeljebb két percig |
| A rendszer érvénytelen AZONOSÍTÓJÚ feladatot használt |Ellenőrizze, hogy helyes-e a feladatokhoz tartozó azonosító |

## <a name="bad-gateway"></a>Hibás átjáró

**Http-állapotkód**: 502

| Ok | Felbontás |
| --- | --- |
| A belső adatgyűjtési folyamat a Webhcaten folyamaton belül következik be |Várjon, amíg befejeződik a Webhcaten-szolgáltatás begyűjtése vagy újraindítása |
| Időtúllépés a erőforráskezelő szolgáltatás válaszára való várakozáskor. Ez a hiba akkor fordulhat elő, ha az aktív alkalmazások száma a beállított maximális értékre kerül (alapértelmezett 10 000). |Várjon, amíg a jelenleg futó feladatok befejeződik, vagy növelje az egyidejű feladat korlátját a `yarn.scheduler.capacity.maximum-applications`módosításával. További információ: a [konfiguráció módosítása](#modifying-configuration) szakasz. |
| Az összes feladat beolvasására tett kísérlet a [/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) hívása közben, míg `Fields` beállítása `*` |Ne kérje le *az összes* feladatot. Ehelyett a `jobid` használatával kéri le a feladatok részleteit, amelyek csak bizonyos feladat-AZONOSÍTÓnál nagyobbak. Vagy ne használja a `Fields` |
| A Webhcaten szolgáltatás nem érhető el a Átjárócsomóponthoz feladatátvétel során |Várjon két percet, és ismételje meg a műveletet. |
| Több mint 500 függőben lévő feladat van elküldve a Webhcaten-on keresztül |Várjon, amíg a jelenleg függőben lévő feladatok befejeződtek a további feladatok elküldése előtt |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
