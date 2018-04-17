---
title: Ismertetés és hárítsa el a WebHCat-hibákat a HDInsight - Azure |} Microsoft Docs
description: Ismerje meg, hogyan körülbelül gyakori hibák által visszaadott WebHCat a HDInsight és azok megoldását.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 03805086bf1cff3f6d2a27a46960d736538bf57b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Ismertetés és a HDInsight WebHCat hibák megoldásához

További információk a HDInsight, és azok megoldását WebHCat használatakor hibák. WebHCat belsőleg ügyféloldali eszközök, például Azure PowerShell és a Data Lake Tools for Visual Studio.

## <a name="what-is-webhcat"></a>Mi az a WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) egy REST API a [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), egy tábla és a Hadoop tárolási alkalmazáskezelési réteg. WebHCat a HDInsight-fürtökön alapértelmezés szerint engedélyezve van, és segítségével különböző eszközök feladatokat küldhet el, beolvasni a feladat állapotát, stb. a fürthöz való bejelentkezés nélkül.

## <a name="modifying-configuration"></a>Konfigurációjának módosítása

> [!IMPORTANT]
> A jelen dokumentumban felsorolt hibák számos fordulhat elő, mert túllépte a beállított maximális. A feloldási lépés akkor említi, hogy egy érték módosítható, ha kell használnia az alábbiak egyikét a módosítás végrehajtásához:

* A **Windows** fürtök: egy Parancsfájlművelettel az érték beállítása a fürt létrehozása során. További információkért lásd: [Parancsfájlműveletek fejlesztése](hdinsight-hadoop-script-actions.md).

* A **Linux** fürtök: használata Ambari (web vagy a REST API) értékét módosítani. További információkért lásd: [kezelése HDInsight Ambari használatával](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Alapértelmezett konfigurációja

Ha a következő alapértelmezett értékek számát, azt WebHCat teljesítményét, vagy hibákat okozhatnak:

| Beállítás | Művelet | Alapértelmezett érték |
| --- | --- | --- |
| [yarn.Scheduler.Capacity.maximum-alkalmazások][maximum-applications] |Az aktív lehet egyidejű feladatok maximális száma (folyamatban vagy fut) |10,000 |
| [templeton.exec.max-procs][max-procs] |Egyidejűleg szolgáltatható kérelmek maximális száma |20 |
| [mapreduce.jobhistory.max-kor-ms][max-age-ms] |Az, hogy hány napig feladatelőzmények megmaradnak |7 nap |

## <a name="too-many-requests"></a>Túl sok a kérelem

**HTTP-állapotkód**: 429

| Ok | Megoldás: |
| --- | --- |
| Túllépte a maximális egyidejű kérelmek / perc (alapértelmezett érték 20) WebHCat által kiszolgált |Csökkentse a munkaterhelés győződjön meg arról, hogy Ön nem küldenek több, mint az egyidejű kérelmek maximális számát, vagy növelje az egyidejűleg futtatható kérelmek maximális módosításával `templeton.exec.max-procs`. További információkért lásd: [konfiguráció módosítása](#modifying-configuration) |

## <a name="server-unavailable"></a>A kiszolgáló nem érhető el

**HTTP-állapotkód**: 503-as

| Ok | Megoldás: |
| --- | --- |
| Ez az állapot kód általában akkor fordul elő, az elsődleges és másodlagos HeadNode a fürt közötti feladatátvétel során |Várjon két percet, majd próbálja megismételni a műveletet |

## <a name="bad-request-content-could-not-find-job"></a>Hibás kérés tartalma: nem található feladat

**HTTP-állapotkód**: 400

| Ok | Megoldás: |
| --- | --- |
| Feladat részleteinek törölve lettek a feladatelőzmények által tisztító |Feladatelőzmények megőrzési idő alapértelmezés szerint 7 nap. Az alapértelmezett megőrzési időtartamot a módosításával lehet megváltoztatni `mapreduce.jobhistory.max-age-ms`. További információkért lásd: [konfiguráció módosítása](#modifying-configuration) |
| Feladat leállította a feladatátvétel miatt |Ismételje meg a feladat elküldése legfeljebb két percig |
| Feladatazonosító érvénytelen lett megadva. |Annak ellenőrzése, hogy a feladat azonosítója helyes-e |

## <a name="bad-gateway"></a>Hibás átjáró

**HTTP-állapotkód**: 502-es

| Ok | Megoldás: |
| --- | --- |
| Belső szemétgyűjtés folyamatban van a WebHCat folyamaton belül |Várjon, amíg a szemétgyűjtő befejeződését, vagy indítsa újra a WebHCat szolgáltatást |
| Az erőforrás-kezelő szolgáltatás válaszára várakozás időkorlátja lejárt. Ez a hiba akkor fordulhat elő, amikor aktív kérelmek száma a megadott maximális értéket (alapértelmezett érték 10 000) |Várjon, amíg a jelenleg futó feladat befejeződik, vagy növelje az egyidejű feladat korlát módosításával `yarn.scheduler.capacity.maximum-applications`. További információkért lásd: a [módosítása konfigurációs](#modifying-configuration) szakasz. |
| Minden feladat keresztül beolvasására tett kísérlet a [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) hívás közben `Fields` beállítása `*` |Nem beolvasni a *összes* feladat részletei. Ehelyett használja `jobid` beolvasni csak nagyobb, mint egyes feladatazonosítót a feladat részleteit. Vagy, ne használja `Fields` |
| A WebHCat-szolgáltatás nem működik HeadNode feladatátvétel során |Két perc várakozás, majd próbálja megismételni a műveletet |
| Webhcaten keresztül küldött 500-nál több függőben lévő feladatok |Várjon, amíg jelenleg függő feladatok már befejeződtek több feladat elküldése előtt |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
