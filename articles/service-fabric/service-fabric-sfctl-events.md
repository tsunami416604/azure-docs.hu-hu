---
title: Azure Service Fabric CLI- sfctl események
description: A Service Fabric CLI sfctl eseményparancsainak ismertetése.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906105"
---
# <a name="sfctl-events"></a>sfctl események
Események lekérése az eseménytárolóból (ha az EventStore szolgáltatás már telepítve van).

Az EventStore rendszerszolgáltatás bármely >=6.4-et futtató SFRP-fürtkonfigurációs frissítéssel adható hozzá. Ellenőrizze a következő\: \:url https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| minden alkalmazás-lista | Leveszi az összes alkalmazással kapcsolatos eseményt. |
| összes csomópont-lista | Az összes csomóponthoz kapcsolódó esemény leszámítása. |
| összes partíció-lista | Leveszi az összes partícióval kapcsolatos eseményt. |
| all-services-lista | Leveszi az összes Szolgáltatásokkal kapcsolatos eseményt. |
| alkalmazáslista | Alkalmazással kapcsolatos események lekérése. |
| fürtlista | Leveszi az összes fürthöz kapcsolódó eseményt. |
| csomópontlista | Csomóponttal kapcsolatos események leése. |
| partition-all-replikák-lista | Lekéri a partíció összes Replikákkal kapcsolatos eseményét. |
| partíció-lista | Leaszkezése egy partícióval kapcsolatos eseményeket. |
| partíció-replika-lista | Leaszkezése a partícióreplikával kapcsolatos eseményeket. |
| szolgáltatáslista | Szolgáltatással kapcsolatos események leése. |

## <a name="sfctl-events-all-applications-list"></a>sfctl események minden alkalmazás-lista
Leveszi az összes alkalmazással kapcsolatos eseményt.

A válasz az ApplicationEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl események all-nodes-list
Az összes csomóponthoz kapcsolódó esemény leszámítása.

A válasz a NodeEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl események all-partitions-list
Leveszi az összes partícióval kapcsolatos eseményt.

A válasz a PartitionEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-all-services-list"></a>sfctl események all-services-list
Leveszi az összes Szolgáltatásokkal kapcsolatos eseményt.

A válasz a ServiceEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-application-list"></a>sfctl események alkalmazáslistája
Alkalmazással kapcsolatos események lekérése.

A válasz az ApplicationEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-cluster-list"></a>sfctl események fürtlistája
Leveszi az összes fürthöz kapcsolódó eseményt.

A válasz a ClusterEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-node-list"></a>sfctl események csomópontlistája
Csomóponttal kapcsolatos események leése.

A válasz a NodeEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl események partíció-all-replikák-lista
Lekéri a partíció összes Replikákkal kapcsolatos eseményét.

A válasz a ReplicaEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --partition-id [Kötelező] | A partíció identitása. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-partition-list"></a>sfctl események partíció-lista
Leaszkezése egy partícióval kapcsolatos eseményeket.

A válasz a PartitionEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --partition-id [Kötelező] | A partíció identitása. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl események partíció-replika-lista
Leaszkezése a partícióreplikával kapcsolatos eseményeket.

A válasz a ReplicaEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A replika azonosítója. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-service-list"></a>sfctl események szolgáltatáslistája
Szolgáltatással kapcsolatos események leése.

A válasz a ServiceEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --end-time-utc [Kötelező] | A lookup lekérdezés befejezési időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --start-time-utc [Kötelező] | A lekérdezési lekérdezés kezdő időpontja az ISO UTC yyyy-MM-ddTHH\:mm\:ssZ-ben. |
| --események-típusú szűrő | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza a FabricEvents típusait, amelyeket csak a válaszban kell szerepeltetni. |
| --kizárás-elemzés-események | Ez a param letiltja az AnalysisEvents lekérését, ha true teljesül. |
| --skip-korrelációs-keres | Ez a param letiltja a CorrelatedEvents információk keresését, ha true teljesül. ellenkező esetben a CorrelationEvents feldolgozódik, és a HasCorrelatedEvents mező minden FabricEvent lesz feltöltve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

