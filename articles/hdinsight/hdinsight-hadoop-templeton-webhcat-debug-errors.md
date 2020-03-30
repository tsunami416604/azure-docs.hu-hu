---
title: A WebHCat hibáinak megértése és megoldása a HDInsightban – Azure
description: Ismerje meg, hogyan szólhat a WebHCat által a HDInsight on visszaadott gyakori hibákról, és hogyan oldhatja meg azokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638850"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>A WebHCattől kapott hibák megértése és megoldása HDInsightban

Ismerje meg a WebHCat HDInsight-mal való használata során kapott hibákat, és hogyan oldhatja meg azokat. A WebHCat-et belsőleg használják az ügyféloldali eszközök, például az Azure PowerShell és a Data Lake Tools for Visual Studio.

## <a name="what-is-webhcat"></a>Mi az a WebHCat

[A WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog)REST API-ja, amely az Apache Hadoop egy tábla és tárolófelügyeleti rétege. A WebHCat alapértelmezés szerint engedélyezve van a HDInsight-fürtökön, és különböző eszközök használják a feladatok elküldésére, a feladat állapotának beküldésére és így tovább, anélkül, hogy bejelentkezne a fürtbe.

## <a name="modifying-configuration"></a>Konfiguráció módosítása

A dokumentumban felsorolt hibák közül több a beállított maximális érték túllépése miatt fordul elő. Amikor a megoldási lépés megemlíti, hogy módosíthatja az értéket, az Apache Ambari (web vagy REST API) segítségével módosítsa az értéket. További információ: [HDInsight kezelése az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Alapértelmezett konfiguráció

Ha a következő alapértelmezett értékeket lépi túl, az ronthatja a WebHCat teljesítményét, vagy hibákat okozhat:

| Beállítás | Művelet | Alapértelmezett érték |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-alkalmazások][maximum-applications] |Az egyidejűleg (függőben lévő vagy futó) aktív feladatok maximális száma |10,000 |
| [templeton.exec.max-procs][max-procs] |Az egyidejűleg kézbesíthető kérelmek maximális száma |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |A feladatelőzmények megőrzésére eltelt napok száma |7 nap |

## <a name="too-many-requests"></a>Túl sok kérés

**HTTP állapotkód**: 429

| Ok | Megoldás: |
| --- | --- |
| Túllépte a WebHCat által percenként kiszolgált maximális egyidejű kérelmeket (alapértelmezett 20) |Csökkentse a munkaterhelést, hogy ne küldjön be több, mint az egyidejű kérelmek maximális `templeton.exec.max-procs`száma, vagy növelje az egyidejű kérelemkorlátot módosítással. További információ: [Konfiguráció módosítása](#modifying-configuration) |

## <a name="server-unavailable"></a>A kiszolgáló nem érhető el

**HTTP állapotkód**: 503

| Ok | Megoldás: |
| --- | --- |
| Ez az állapotkód általában a fürt elsődleges és másodlagos headnode-ja közötti feladatátvétel során fordul elő. |Várjon két percet, majd próbálkozzon újra a művelettel |

## <a name="bad-request-content-could-not-find-job"></a>Hibás kérés tartalom: Nem található állás

**HTTP Állapotkód**: 400

| Ok | Megoldás: |
| --- | --- |
| A munkaköri adatokat megtisztította a munkaelőzmények et. |A feladatelőzmények alapértelmezett megőrzési ideje 7 nap. Az alapértelmezett megőrzési időszak a `mapreduce.jobhistory.max-age-ms`módosításával módosítható. További információ: [Konfiguráció módosítása](#modifying-configuration) |
| Job-ot megölték egy feladatátvétel miatt. |Feladat beküldésének újrapróbálkozása legfeljebb két percig |
| Érvénytelen feladatazonosítót használt |Annak ellenőrzése, hogy helyes-e a feladatazonosítója |

## <a name="bad-gateway"></a>Hibás átjáró

**HTTP állapotkód**: 502

| Ok | Megoldás: |
| --- | --- |
| A belső szemétgyűjtés a WebHCat folyamaton belül történik |Várakozás a webhcat szolgáltatás befejezésére vagy újraindítására |
| Időout vár a válasz a ResourceManager szolgáltatás. Ez a hiba akkor fordulhat elő, ha az aktív alkalmazások száma a beállított maximumra megy (alapértelmezett 10 000) |Várja meg, amíg a jelenleg futó feladatok befejeződnek, vagy a program módosításával megkell növelni az egyidejű feladatkorlátot. `yarn.scheduler.capacity.maximum-applications` További információt a [Konfiguráció módosítása](#modifying-configuration) című szakaszban talál. |
| Az összes feladat beolvasásának megkísérlése a [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) híváson keresztül, miközben `Fields``*` |Ne olvassa el *az összes* munkarészletet. Ehelyett `jobid` használja a feladatok részleteinek lekéréséhez csak nagyobb, mint bizonyos feladatazonosító. Vagy ne használja`Fields` |
| A WebHCat szolgáltatás nem működik a HeadNode feladatátvétel során |Várjon két percet, majd próbálkozzon újra a művelettel |
| Több mint 500 függőben lévő feladat van beküldve a WebHCat-en keresztül |Várjon, amíg a jelenleg függőben lévő feladatok befejeződnek, mielőtt további feladatokat küldene be |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
