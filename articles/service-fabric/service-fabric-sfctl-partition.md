---
title: Azure Service Fabric CLI- sfctl partíció
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Egy szolgáltatás partícióinak kezeléséhez szükséges parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905836"
---
# <a name="sfctl-partition"></a>sfctl-partíció
Bármely szolgáltatás partícióinak lekérdezése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| adatvesztés | Ez az API adatvesztést okoz a megadott partícióhoz. |
| adatvesztés állapota | Beszerzi a StartDataLoss API-val megkezdett partícióadat-veszteségművelet folyamatát. |
| Egészségügyi | Letöltője a megadott Service Fabric-partíció állapotát. |
| Info | A Service Fabric-partícióra vonatkozó információk leése. |
| lista | Egy Service Fabric-szolgáltatás partícióinak listájának letöltője. |
| betöltés | Beszerzi a megadott Service Fabric partíció betöltési adatait. |
| terhelés visszaállítása | Alaphelyzetbe állítja a Service Fabric partíció aktuális terhelését. |
| kvórum-veszteség | Kvórumvesztést idéz elő egy adott állapotalapú szolgáltatáspartícióhoz. |
| kvórum-veszteség-állapot | A StartQuorumLoss API-val megkezdett partíciók kvórumveszteség-műveletének folyamatát kapja meg. |
| Visszaszerez | Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani egy adott partíciót, amely jelenleg beragadt a kvórumveszteség. |
| vissza-minden | Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani minden olyan szolgáltatást (beleértve a rendszerszolgáltatásokat is), amelyek jelenleg kvórumveszteségben ragadtak. |
| jelentés-egészségügy | Állapotjelentést küld a Service Fabric partíción. |
| restart | Ez az API újraindítja a megadott partíció néhány vagy összes replikáját vagy példányát. |
| újrakezdés állapota | A StartPartitionRestart használatával megkezdett PartitionRestart művelet folyamatának letiltása. |
| svc-név | Lekéri a Service Fabric szolgáltatás nevét egy partícióhoz. |

## <a name="sfctl-partition-data-loss"></a>sfctl partíció adatvesztés
Ez az API adatvesztést okoz a megadott partícióhoz.

A partíció OnDataLossAsync API-jának hívását indítja el.  Ez az API adatvesztést okoz a megadott partícióhoz. Elindítja a partíció OnDataLoss API-hívását. A tényleges adatvesztés a megadott DataLossMode-tól függ.
- PartialDataLoss: Csak a kópiák kvóruma törlődik, és OnDataLoss aktiválódik a partíciót, de a tényleges adatvesztés attól függ, hogy a jelenléte a repülés közbeni replikáció.  
- FullDataLoss: Az összes replikák törlődnek, így minden adat elvész, és OnDataLoss aktiválódik. Ezt az API-t csak egy állapotalapú szolgáltatással kell meghívni a célként. Az API-t egy rendszerszolgáltatással a célként való hívása nem ajánlott.

> [!NOTE]   
> Az API hívása után nem vonható vissza. A Megszakítás művelet hívása csak a végrehajtást és a rendszer belső állapotának törlését teszi lehetővé. Nem állítja vissza az adatokat, ha a parancs elég messzire haladt ahhoz, hogy adatvesztést okozzon. Hívja meg a GetDataLossProgress API-t ugyanazzal az OperationId azonosítóval az API-val indított műveletadatainak adására.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --adatvesztés-mód [Kötelező] | Ez a felsorítás átkerül a StartDataLoss API-ba, hogy jelezze, milyen típusú adatvesztést kell indukálni. |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --partition-id [Kötelező] | A partíció identitása. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partíció adatvesztési állapota
Beszerzi a StartDataLoss API-val megkezdett partícióadat-veszteségművelet folyamatát.

Leveszi a StartDataLoss használatával az OperationId használatával elindított adatvesztési művelet előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --partition-id [Kötelező] | A partíció identitása. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-health"></a>sfctl partíció állapota
Letöltője a megadott Service Fabric-partíció állapotát.

Az EventsHealthStateFilter használatával szűrheti a szolgáltatáson jelentett állapotesemények gyűjteményét az állapot alapján. A ReplicasHealthStateFilter segítségével szűrje a partitionon lévő ReplicaHealthState objektumok gyűjteményét. Ha olyan partíciót ad meg, amely nem létezik a health store-ban, a kérés hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --események-állapot-állapot-szűrő | Lehetővé teszi az állapot alapján visszaadott HealthEvent objektumok gyűjteményének szűrését. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az összes eseményt, amelynek HealthState értéke OK (2) és Figyelmeztetés (4) visszaadja.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --kizárás-egészségügyi statisztikák | Azt jelzi, hogy az állapotstatisztikát vissza kell-e adni a lekérdezés eredményének részeként. Alapértelmezés szerint hamis. A statisztikák az Ok, Figyelmeztetés és Hiba állapotban lévő gyermekentitások számát mutatják. |
| --replikák-állapot-állapot-szűrő | Lehetővé teszi a ReplicaHealthState objektumok gyűjteményének szűrését a partíción. Az érték a HealthStateFilter tagjainak tagjaitól vagy bitenkénti műveleteiből szerezhető be. Csak a szűrőnek megfelelő replikák at adja vissza a rendszer. Az összes replikák lesznek használva az összesített állapot kiértékelésére. Ha nincs megadva, az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték lehet ezeknek az értékeknek a kombinációja, amelyeket bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az ÖSSZES HEALTHState értékű esemény (2) és figyelmeztetés (4) lesz visszaadva. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-info"></a>sfctl partíció infó
A Service Fabric-partícióra vonatkozó információk leése.

A megadott partícióra vonatkozó információk bekéselése. A válasz tartalmazza a partíció azonosítóját, particionálási séma adatait, a partíció által támogatott kulcsokat, állapotot, állapotot és a partíció egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-list"></a>sfctl partíciólista
Egy Service Fabric-szolgáltatás partícióinak listájának letöltője.

A válasz tartalmazza a partíció azonosítóját, particionálási séma adatait, a partíció által támogatott kulcsokat, állapotot, állapotot és a partíció egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-load"></a>sfctl partíció terhelése
Beszerzi a megadott Service Fabric partíció betöltési adatait.

Információt ad vissza egy megadott partíció terheléséről. A válasz tartalmazza a Service Fabric-partícióterhelési jelentések listáját. Minden jelentés tartalmazza a terhelési metrika nevét, értékét és az utc-ben jelentett utolsó jelentett időt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-load-reset"></a>sfctl partíció terhelés-reset
Alaphelyzetbe állítja a Service Fabric partíció aktuális terhelését.

Visszaállítja a Service Fabric partíció aktuális terhelését a szolgáltatás alapértelmezett terhelésére.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partíció kvórumveszteség
Kvórumvesztést idéz elő egy adott állapotalapú szolgáltatáspartícióhoz.

Ez az API a szolgáltatás ideiglenes kvórumveszteség-helyzetéhez hasznos. Hívja meg a GetQuorumLossProgress API-t ugyanazzal az OperationId azonosítóval, hogy információt ad vissza az API-val indított műveletről. Ez csak állapotalapú, megőrzött (HasPersistedState==true) szolgáltatásokon hívható meg.  Ne használja ezt az API-t állapotnélküli szolgáltatásokon vagy csak állapotalapú memórián belüli szolgáltatásokon.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --partition-id [Kötelező] | A partíció identitása. |
| --kvórum-veszteség időtartama [Kötelező] | Az az idő, amerre a partíció kvórumveszteségben marad.  Ezt másodpercben kell megadni. |
| --kvórum-veszteség-mód [Kötelező] | Ez a felsorítás átkerül a StartQuorumLoss API-ba, jelezve, hogy milyen típusú kvórumveszteséget kell előidézni. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partíció kvórum-veszteség-állapot
A StartQuorumLoss API-val megkezdett partíciók kvórumveszteség-műveletének folyamatát kapja meg.

A StartQuorumLoss szolgáltatással kezdődő kvórumveszteség-művelet előrehaladásának lekezdete a megadott OperationId használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --partition-id [Kötelező] | A partíció identitása. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-recover"></a>sfctl partíció helyreállítása
Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani egy adott partíciót, amely jelenleg beragadt a kvórumveszteség.

Ezt a műveletet csak akkor kell végrehajtani, ha ismeretes, hogy a nem működő replikák nem állíthatók helyre. Az API helytelen használata adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-recover-all"></a>sfctl partíció vissza-minden
Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani minden olyan szolgáltatást (beleértve a rendszerszolgáltatásokat is), amelyek jelenleg kvórumveszteségben ragadtak.

Ezt a műveletet csak akkor kell végrehajtani, ha ismeretes, hogy a nem működő replikák nem állíthatók helyre. Az API helytelen használata adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-report-health"></a>sfctl partíció jelentés-állapot
Állapotjelentést küld a Service Fabric partíción.

A megadott Service Fabric-partíció állapotát jelenti. A jelentésnek tartalmaznia kell az állapotjelentés forrására és a jelentett tulajdonságra vonatkozó információkat. A jelentés egy Service Fabric átjáró partíció, amely továbbítja a health Store. A jelentést az átjáró elfogadhatja, de az egészségügyi tároló további ellenőrzés után elutasítja. Például a health store elutasíthatja a jelentést, mert egy érvénytelen paraméter, mint például egy elavult sorszám. Ha meg szeretné tudni, hogy a jelentés alkalmazva lett-e az állapottárolóban, ellenőrizze, hogy a jelentés megjelenik-e az események szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --health-property [Kötelező] | Az egészségügyi információk tulajdonsága. <br><br> Egy entitás különböző tulajdonságokállapot-jelentésekkel rendelkezhet. A tulajdonság egy karakterlánc, és nem egy rögzített felsorolás, amely lehetővé teszi az előjelentés rugalmasságát a jelentést kiváltó állapot kategorizálására. Például egy "LocalWatchdog" SourceId azonosítóval rendelkező jelentéskészítő figyelheti a csomóponton rendelkezésre álló lemez állapotát, így jelentheti az "AvailableDisk" tulajdonságot az adott csomóponton. Ugyanaz a jelentéskészítő figyelheti a csomópont-kapcsolatot, így jelentheti a "Kapcsolat" tulajdonságot ugyanazon a csomóponton. Az állapottárolóban ezeket a jelentéseket külön állapoteseményekként kezeli a megadott csomópont. A SourceId-dal együtt a tulajdonság egyedileg azonosítja az állapotinformációkat. |
| --állapot[Kötelező] | A lehetséges\: értékek a következők: "Érvénytelen", "Ok", "Figyelmeztetés", "Hiba", "Ismeretlen". |
| --partition-id [Kötelező] | A partíció identitása. |
| --source-id [Kötelező] | A forrásnév, amely azonosítja az ügyfél/figyelő/rendszerösszetevő, amely létrehozta az állapotadatokat. |
| --leírás | Az egészségügyi információk leírása. <br><br> Szabad szöveget jelöl, amely a jelentéssel kapcsolatos emberi olvasható információk hozzáadására szolgál. A leírás maximális karakterlánchossza 4096 karakter. Ha a megadott karakterlánc hosszabb, a program automatikusan csonkolja. Csonkoláskor a leírás utolsó karakterei a "[Csonka]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi a felhasználóknak, hogy csonkolás történt. Ne feledje, hogy csonkoláskor a leírás kevesebb mint 4096 karaktert tartalmaz az eredeti karakterláncból. |
| --azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e küldeni. <br><br> Egy állapotjelentés egy Service Fabric átjáró alkalmazás, amely továbbítja a health Store. Ha az Immediate értéke igaz, a rendszer azonnal elküldi a jelentést a HTTP-átjáróból a health Store-ba, függetlenül attól, hogy a HTTP átjáróalkalmazás által használt háló-ügyfél-beállításokat használja.If Immediate is set to true, the report is sent immediately from HTTP Gateway to the health store, regardless of the fabric client settings that the HTTP Gateway Application is using. Ez akkor hasznos, ha a kritikus jelentéseket a lehető leghamarabb el kell küldeni. Az időzítéstől és az egyéb feltételektől függően a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró be van zárva, vagy ha az üzenet nem éri el az átjárót. Ha az Immediate értéke hamis, a rendszer a http-átjáró ból származó állapotügyfél-beállítások alapján küldi el a jelentést. Ezért a rendszer a HealthReportSendInterval konfigurációnak megfelelően lesz kötegelve. Ez az ajánlott beállítás, mert lehetővé teszi, hogy az állapotjelző ügyfél optimalizálja az állapotjelentési üzeneteket az állapottárolónak, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a jelentések nem kerülnek azonnal elküldésre. |
| --remove-when-expired | Érték, amely azt jelzi, hogy a jelentés törlődik-e az állapottárolóból, amikor lejár. <br><br> Ha értéke igaz, a jelentés törlődik a health Store lejárta után. Ha hamis ra van állítva, a jelentés lejártakor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeres időközönként jelentést, meg kell állítani a RemoveWhenExpired false (alapértelmezett). Ily módon a jelentéskészítő problémák (pl. holtpont), és nem tud jelentést, az entitás kiértékelése hiba, amikor az állapotjelentés lejár. Ez az entitást hibaállapotként jelzi. |
| --szekvenciaszám | Az állapotjelentés sorozatszáma numerikus karakterláncként. <br><br> A jelentéssorszámot az állapottároló az elavult jelentések észlelésére használja. Ha nincs megadva, a jelentés hozzáadásakor az állapotügyfél automatikusan létrehoz egy sorszámot. |
| --idő-out -t | Alapértelmezett\: 60. |
| --ttl | Az az időtartam, amelyre ez az állapotjelentés érvényes. Ez a mező Az Időtartam megadásához Az ISO8601 formátumot használja. <br><br> Amikor az ügyfelek rendszeres időközönként jelentést küldenek, az életidejéneknél nagyobb gyakorisággal kell jelentéseket küldeniük. Ha az ügyfelek az átmenetről számolnak be, beállíthatják, hogy az élet ideje végtelen. Amikor lejár az életidő, az állapotadatokat tartalmazó állapotesemény törlődik az állapottárolóból, ha az RemoveWhenExpired igaz, vagy hiba esetén kiértékelve, ha az RemoveWhenExpired hamis. Ha nincs megadva, az élethez való idő nem éri el a végtelen értéket. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-restart"></a>sfctl partíció újraindítása
Ez az API újraindítja a megadott partíció néhány vagy összes replikáját vagy példányát.

Ez az API feladatátvétel teszteléséhez hasznos. Ha állapotnélküli szolgáltatáspartíciót céloz meg, a RestartPartitionMode programnak AllReplicasOrInstances értékűnek kell lennie. Hívja meg a GetPartitionRestartProgress API-t ugyanazzal az OperationId azonosítóval a folyamat lehívásához.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --partition-id [Kötelező] | A partíció identitása. |
| --restart-partition-mode [Kötelező] | Írja le, hogy mely partíciókat kell újraindítani. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-restart-status"></a>sfctl partíció újraindítás-állapota
A StartPartitionRestart használatával megkezdett PartitionRestart művelet folyamatának letiltása.

Beszerzi a StartPartitionRestart programmal indított PartitionRestart folyamatát a megadott OperationId használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --partition-id [Kötelező] | A partíció identitása. |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-svc-name"></a>sfctl partíció svc-név
Lekéri a Service Fabric szolgáltatás nevét egy partícióhoz.

Lekéri a szolgáltatás nevét a megadott partícióhoz. 404-es hibát ad vissza, ha a partícióazonosító nem létezik a fürtben.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.
