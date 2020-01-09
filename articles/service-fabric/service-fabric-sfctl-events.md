---
title: Azure Service Fabric CLI – sfctl események
description: A parancssori felület sfctl eseményeinek Service Fabric ismerteti.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 35dfbdb3f8b84e797d35c8d7ec1020509f7e8b57
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466051"
---
# <a name="sfctl-events"></a>sfctl események
Események beolvasása az események tárolójából (ha a EventStore szolgáltatás már telepítve van).

A EventStore rendszerszolgáltatás a > = 6.4-t futtató bármely SFRP-fürtön egy konfigurációs frissítéssel bővíthető. Tekintse meg a következő URL-címet\: HTTPS\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| összes alkalmazás – lista | Az összes alkalmazáshoz kapcsolódó esemény beolvasása. |
| összes csomópont – lista | Az összes csomóponttal kapcsolatos esemény beolvasása. |
| összes partíció – lista | Az összes partícióval kapcsolatos esemény beolvasása. |
| összes szolgáltatás – lista | Minden szolgáltatással kapcsolatos esemény beolvasása. |
| alkalmazás-lista | Az alkalmazással kapcsolatos események beolvasása. |
| fürt – lista | A fürttel kapcsolatos összes esemény beolvasása. |
| csomópontok listája | Csomóponttal kapcsolatos események beolvasása. |
| partíció – összes replika – lista | Egy partíció összes replikával kapcsolatos eseményének beolvasása. |
| partíciók listája | Egy partícióval kapcsolatos események beolvasása. |
| partíció-replika-lista | Egy partíciós replika-kapcsolódó események beolvasása. |
| szolgáltatások listája | Szolgáltatással kapcsolatos események beolvasása. |

## <a name="sfctl-events-all-applications-list"></a>sfctl-események – összes alkalmazás – lista
Az összes alkalmazáshoz kapcsolódó esemény beolvasása.

A válasz a ApplicationEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl események – összes csomópont – lista
Az összes csomóponttal kapcsolatos esemény beolvasása.

A válasz a NodeEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl-események – összes partíció – lista
Az összes partícióval kapcsolatos esemény beolvasása.

A válasz a PartitionEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-all-services-list"></a>sfctl-események – összes szolgáltatás listája
Minden szolgáltatással kapcsolatos esemény beolvasása.

A válasz a ServiceEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-application-list"></a>sfctl események alkalmazás-lista
Az alkalmazással kapcsolatos események beolvasása.

A válasz a ApplicationEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric\:" URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a "\~" karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric\:/MyApp/App1", az alkalmazás identitása "SajátPr\~App1" lesz a korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-cluster-list"></a>sfctl-események fürtjének listája
A fürttel kapcsolatos összes esemény beolvasása.

A válasz a ClusterEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-node-list"></a>sfctl-események csomópont – lista
Csomóponttal kapcsolatos események beolvasása.

A válasz a NodeEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl-események partíciója – összes replika – lista
Egy partíció összes replikával kapcsolatos eseményének beolvasása.

A válasz a ReplicaEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-partition-list"></a>sfctl-események partíciójának listája
Egy partícióval kapcsolatos események beolvasása.

A válasz a PartitionEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl Events Partition – replika-lista
Egy partíciós replika-kapcsolódó események beolvasása.

A válasz a ReplicaEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A replika azonosítója. |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-events-service-list"></a>sfctl Events Service – lista
Szolgáltatással kapcsolatos események beolvasása.

A válasz a ServiceEvent objektumok listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --befejezési idő – UTC [kötelező] | A keresési lekérdezés befejezési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric\:" URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a "\~" karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric\:/MyApp/App1/svc1", a szolgáltatás identitása a korábbi verziókban a következő: "SajátPr\~App1\~svc1" (6.0 + és "SajátPr/App1/svc1"). |
| --Start-time-UTC [kötelező] | Egy keresési lekérdezés kezdési időpontja az ISO UTC éééé-hh-NNTóó\:mm\:ssZ. |
| --Events-types-Filter | Ez egy vesszővel elválasztott karakterlánc, amely meghatározza, hogy milyen típusú FabricEvents kell szerepelnie a válaszban. |
| --kizárás-elemzés – események | Ez a param letiltja a AnalysisEvents lekérését, ha az igaz értéket adja át. |
| – Kihagyás – korreláció – keresés | Ez a param letiltja a CorrelatedEvents információinak keresését, ha az igaz értéket adja át. Ellenkező esetben a CorrelationEvents lekérése folyamatban van, és a HasCorrelatedEvents mező minden FabricEvent feltöltve lesz. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

