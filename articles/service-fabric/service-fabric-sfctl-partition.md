---
title: Az Azure Service Fabric parancssori felület - sfctl-partíció |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-partíció parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666766"
---
# <a name="sfctl-partition"></a>sfctl-partíció
Lekérdezését, és bármely szolgáltatás partícióinak kezelését.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| adatveszteség | Az API-t fog idéz elő adatvesztés a megadott partíció. |
| a Data-adatveszteség-állapota | Lekérdezi a StartDataLoss API használatának partíció adatok elvesztése műveletnek az előrehaladását. |
| egészségügy | A megadott Service Fabric-partíción állapotának beolvasása. |
| információ | A Service Fabric-partíción adatainak beolvasása. |
| lista | Service Fabric-szolgáltatás partícióinak listáját olvassa be. |
| betöltés | A megadott Service Fabric-partíción a terhelés információkat kér le. |
| load-reset | Alaphelyzetbe állítja az aktuális terhelés egy Service Fabric-partíción. |
| kvórum elvesztése | Egy adott állapot-nyilvántartó szolgáltatási partíció kvórum elvesztése kapott. |
| kvórum – adatveszteség-állapot | Lekérdezi a StartQuorumLoss API használatának partíción, egy kvórum elvesztése műveletnek az előrehaladását. |
| helyreállítás | Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia helyreállítani egy adott partíció kvórumveszteségben jelenleg elakadt. |
| helyreállítás – minden | Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia összes szolgáltatást (beleértve a helyrendszeri szolgáltatások), amelyek jelenleg ragadnak kvórumveszteségben helyreállítani. |
| report-health | A Service Fabric-partíción health jelentést küld. |
| restart | Az API-t néhány vagy összes replikákat, vagy a megadott partíció példánya újraindul. |
| Újraindítás – állapot | Lekérdezi egy StartPartitionRestart első lépéseiben PartitionRestart műveletnek az előrehaladását. |
| svc-name | A Service Fabric-szolgáltatás egy partíció nevét olvassa be. |

## <a name="sfctl-partition-data-loss"></a>sfctl-partíció-adatvesztés
Az API-t fog idéz elő adatvesztés a megadott partíció.

A partíció OnDataLossAsync API hívása aktivál.  Az API-t fog idéz elő adatvesztés a megadott partíció. A partíció OnDataLoss API hívása aktivál. Tényleges az adatvesztést a megadott datalossmode objektum függ.  <br> -PartialDataLoss – csak a replikák kvórum el lesznek távolítva és OnDataLoss akkor aktiválódik, a partíció, de a tényleges adatvesztés szükségszerű replikációs jelenléte függ.  <br> -FullDataLoss – összes replika törlődnek, ezért az összes adat elvesztését, és OnDataLoss aktiválódik. Az API-t kell lze volat pouze egy állapotalapú szolgáltatással és a cél. Ez egy rendszer szolgáltatással és a cél API meghívása nem javasolt.

> [!NOTE] 
> Ez az API meghívása után nem lehet visszavonni. CancelOperation hívása csak állítsa le a végrehajtást és belső rendszerállapot karbantartása. Ez lesz nem helyreállíthatja az adatokat, ha a parancs elegendő fejlődött az adatvesztést okozhat. A művelet lépései az API-t adja vissza az azonos Műveletazonosítóval rendelkező az GetDataLossProgress API meghívása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] adatokat – adatveszteség-mód | Ez az enumerálás a StartDataLoss API-k számára az adatvesztést, hogy milyen típusú kerülnek. |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partíció a data-adatveszteség-állapota
Lekérdezi a StartDataLoss API használatának partíció adatok elvesztése műveletnek az előrehaladását.

Lekérdezi egy lépések StartDataLoss, az OperationId használatával a data-adatveszteség műveletnek az előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-health"></a>sfctl-partíció állapota
A megadott Service Fabric-partíción állapotának beolvasása.

EventsHealthStateFilter gyűjteményét jelentett állapota alapján a service health-események szűréséhez használja. ReplicasHealthStateFilter használatával szűrhetők a partíción ReplicaHealthState objektumok gyűjteményét. Ha megad egy partíció, amely a a health Store adatbázisban nem létezik, a kérelem hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --events-health-state-filter | A gyűjtemény állapotesemény – a visszaadott objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény segítségével kiértékelése összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, a bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd az OK (2), és figyelmeztetés (4) HealthState értékét az események vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --exclude-health-statistics | Azt jelzi-e az egészségügyi statisztikák a rendszer visszalépteti a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a gyermekek száma entitások állapota Ok, figyelmeztetés és hiba. |
| --replicas-health-state-filter | A partíció ReplicaHealthState objektumok gyűjteményét szűrését teszi lehetővé. Az érték tagjai, vagy bitenkénti műveletek HealthStateFilter tagjai szerezhető. Csak a szűrőnek megfelelő replikák lesz visszaadva. Összes replika összesített állapota kiértékeléséhez használható. Ha nincs megadva, minden bejegyzést visszaad. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd az események a HealthState értékét OK (2), és figyelmeztetés (4) lesz visszaadva. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-info"></a>sfctl-partíció adatai
A Service Fabric-partíción adatainak beolvasása.

A megadott partíció adatainak beolvasása. A válasz tartalmazza a Partícióazonosító particionálási séma információkat, a partíció, állapot, egészségügyi és egyéb részleteit a partíció által támogatott kulcsok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-list"></a>sfctl-partíció listája
Service Fabric-szolgáltatás partícióinak listáját olvassa be.

A válasz tartalmazza a Partícióazonosító particionálási séma információkat, a partíció, állapot, egészségügyi és egyéb részleteit a partíció által támogatott kulcsok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --continuation-token | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-load"></a>sfctl-partíció betöltése
A megadott Service Fabric-partíción a terhelés információkat kér le.

A terhelés egy adott partíció adatait adja vissza. A válasz egy Service Fabric-partíción terhelés jelentések listáját tartalmazza. Minden jelentés tartalmazza a metrika neve, érték és utolsó jelentett idő (UTC).

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-load-reset"></a>sfctl-partíció terhelés-alaphelyzetbe állítása
Alaphelyzetbe állítja az aktuális terhelés egy Service Fabric-partíción.

Az aktuális terhelés egy Service Fabric-partíción visszaállítja az alapértelmezett betöltése a szolgáltatás.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partíció kvórum elvesztése
Egy adott állapot-nyilvántartó szolgáltatási partíció kvórum elvesztése kapott.

Az API-t a szolgáltatás egy átmeneti kvórum elvesztése helyzet hasznos. A művelet lépései az API-t adja vissza az azonos Műveletazonosítóval rendelkező az GetQuorumLossProgress API meghívása. Ez lze volat pouze állapotalapú a megőrzött (HasPersistedState igaz ==) szolgáltatásokat.  Ne használja az API állapotmentes szolgáltatások vagy állapot-nyilvántartó memórián belüli csak szolgáltatások.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] kvórum – adatveszteség-időtartama | Mennyi ideig, amelynek a partíció kvórumveszteségben megmarad.  Ez kötelező másodpercek alatt. |
| --– adatveszteség-kvórummód [kötelező] | Ez az enumerálás a StartQuorumLoss API-k számára kvórum elvesztése, hogy milyen típusú kerülnek. |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partíció kvórum – adatveszteség-állapot
Lekérdezi a StartQuorumLoss API használatának partíción, egy kvórum elvesztése műveletnek az előrehaladását.

Lekérdezi egy lépések StartQuorumLoss, a megadott műveletazonosító használata a kvórum elvesztése-műveletnek az előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-recover"></a>sfctl-partíció helyreállítása
Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia helyreállítani egy adott partíció kvórumveszteségben jelenleg elakadt.

Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-recover-all"></a>sfctl-partíció helyreállítása – minden
Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia összes szolgáltatást (beleértve a helyrendszeri szolgáltatások), amelyek jelenleg ragadnak kvórumveszteségben helyreállítani.

Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-report-health"></a>sfctl partíció állapotjelentés
A Service Fabric-partíción health jelentést küld.

A jelentés a megadott Service Fabric-partíción állapotát. A jelentésnek tartalmaznia kell a forrás az egészségügyi jelentés és, amelyen jelentett tulajdonság vonatkozó információk. A jelentést küld egy Service Fabric gateway partíció, amely továbbítja a health Store adatbázisban. A jelentés előfordulhat, hogy fogadja el az átjárót, azonban elutasította a health Store adatbázisban a további ellenőrzést. A health Store adatbázisban például elutasíthatják a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Látható-e a jelentés a a health Store adatbázisban alkalmaztak, ellenőrizze, hogy megjelenik-e a jelentés az események szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --health-property [Required] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterláncot és a nem rögzített enumerálása, hogy az Eszközállapot-feltételt, amely elindítja a jelentés kategorizálása riporter rugalmasan tulajdonság. Például egy riporter a SourceId "LocalWatchdog" figyelheti az állapotot, a rendelkezésre álló lemez egy csomóponton, ezen a csomóponton, jelentést "AvailableDisk" tulajdonság. A ugyanolyan jelentéskészítői figyelheti a csomópont-kapcsolatban –, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonságot. A health Store adatbázisban ezek a jelentések az adott csomópont számára külön állapotesemények kell kezelni. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [kötelező] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Figyelmeztetés", "Error", "Ismeretlen". |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] adatforrás-azonosítója | Az adatforrás neve, amely azonosítja az ügyfél, a figyelő vagy a system összetevő által generált üzemállapotával kapcsolatos adatokat. |
| – Leírás | Az egészségügyi információk leírását. <br><br> Azt jelöli, szabad szöveges adja hozzá a jelentés az emberi olvasható információk segítségével. A leírás karakterlánc maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor automatikusan csonkolva lesz. Csonkolva, amikor az utolsó karakter, a leírás tartalmaz egy "[Truncated]" jelölő, és teljes karaktersorozat 4096 karakternél. Jelenlétét, a jelölő azt jelzi, hogy a felhasználók számára, hogy a csonkolási történt. Vegye figyelembe, hogy csonkolva, a leírásnak legalább 4096 karakternél, az eredeti karakterláncot. |
| – azonnali | Azt a jelzőt, amely azt jelzi, hogy a jelentést közvetlenül kell küldeni. <br><br> Egy jelentés küld egy Service Fabric gateway alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz, a jelentés azonnal címről érkezik a health Store adatbázisban, függetlenül a fabric-ügyfélbeállításokat a http-átjáró alkalmazások által használt HTTP-átjáró. Ez akkor hasznos, a kritikus fontosságú jelentések, amelyek a lehető leghamarabb kell küldeni. Attól függően, ütemezését és egyéb feltételek a jelentés elküldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Ha Immediate hamis értékre van állítva, a jelentés alapján lesz elküldve az egészségügyi ügyfélbeállításokat a HTTP-átjáró. Ezért azt fogja kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az az ajánlott beállítás, mivel így az egészségügyi ügyfél állapotfigyelő jelentési üzenetek a health Store adatbázisban, valamint az egészségügyi jelentés feldolgozása optimalizálása érdekében. Alapértelmezés szerint a rendszer a jelentések nem küldése azonnal. |
| --remove-when-expired | Érték, amely azt jelzi, hogy a jelentés törlődik a health store adatbázisból, a lejárat után. <br><br> Ha igaz értékű, a jelentés távolítja el a health Store adatbázisban után jár le. Ha az értéke HAMIS, a jelentés egy hibát, ha lejárt számít. Ez a tulajdonság értéke alapértelmezés szerint False (hamis). Amikor az ügyfelek rendszeresen készít jelentést, akkor állítsa be RemoveWhenExpired false (alapértelmezett). Ezzel a módszerrel a riporter veti fel (pl. holtpont), és nem készíthető jelentés, az entitás értékeli ki a hiba, ha lejár az állapotjelentés. Az entitás hibaállapot állapottal megőrzendő tartalomként jelöli. |
| --sequence-number | A numerikus karakterláncként egészségügyi jelentés sorszáma. <br><br> A jelentés sorszám használják a health Store adatbázisban elavult jelentések észleléséhez. Ha nincs megadva, egy megfelelő sorszám health-ügyfél által automatikusan létrehozott jelentések hozzáadásakor. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| – élettartam | Az időtartam, amelynek a jelentés érvénytelen. Ebben a mezőben adja meg az időtartamot ISO8601 formátumot használja. <br><br> Amikor az ügyfelek rendszeresen készít jelentést, élettartam-nál nagyobb gyakorisággal kell küldenek jelentéseket. Az ügyfelek jelentés az átmenet, ha azok time to live végtelen, állíthatja be. Élettartam lejár, az egészségügyi adatokat tartalmazó állapotesemény esetén vagy eltávolítja a health Store adatbázisban, ha RemoveWhenExpired igaz értékre, vagy értékelhető a hiba, ha RemoveWhenExpired hamis. Ha nem a végtelen érték az alapértelmezett élettartam adott, ideje. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-restart"></a>sfctl-partíció újraindítása
Az API-t néhány vagy összes replikákat, vagy a megadott partíció példánya újraindul.

Az API-t akkor hasznos, a feladatátvétel tesztelése. Ha használta, amelyekre az állapotmentes szolgáltatás partíció, restartpartitionmode objektum AllReplicasOrInstances kell lennie. A folyamat használatával az azonos Műveletazonosítóval GetPartitionRestartProgress API meghívásához.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] újraindítás-partíció-mód | Indítsa újra a partíciók ismertetik. |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-restart-status"></a>sfctl partíció újraindítása – állapot
Lekérdezi egy StartPartitionRestart első lépéseiben PartitionRestart műveletnek az előrehaladását.

A lépések a megadott műveletazonosító használata StartPartitionRestart PartitionRestart állapotának beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| – [kötelező] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez az azonosító általában nélkül a szolgáltatás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha a szolgáltatás neve "fabric\:/myapp/app1/svc1", a felügyeltszolgáltatás-identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-partition-svc-name"></a>sfctl-partíció-svc-neve
A Service Fabric-szolgáltatás egy partíció nevét olvassa be.

Lekérdezi a megadott partíció a szolgáltatás nevét. 404-es hibát ad vissza, ha a Partícióazonosító nem létezik a fürtben.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).
