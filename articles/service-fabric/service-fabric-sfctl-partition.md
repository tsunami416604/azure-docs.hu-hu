---
title: Az Azure Service Fabric CLI - sfctl partition |} Microsoft Docs
description: A Service Fabric CLI sfctl partíció parancsok ismerteti.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: a9455683c5fad7fad4dda62fd967da617d8a8496
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763646"
---
# <a name="sfctl-partition"></a>sfctl-partíció
Lekérdezi és partíciók valamelyik szolgáltatás kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| adatvesztés | Ez az API fog idéz elő adatvesztés, a megadott partíció. |
| adatok-adatveszteség-állapot | Lekérdezi a StartDataLoss API használatának partíció adatok elvesztését műveletnek az előrehaladását. |
| állapot | A megadott Service Fabric-partíción állapotának beolvasása. |
| információ | A Service Fabric-partíción információ lekérése. |
| lista | Lekérdezi a Service Fabric-szolgáltatás partícióinak listáját. |
| betöltés | Lekérdezi a terhelés adatokat a megadott Service Fabric-partíción. |
| betöltési-alaphelyzetbe állítása | Alaphelyzetbe állítja a jelenlegi terhelés a Service Fabric-partíción. |
| kvórum-elvesztése | Egy adott állapot-nyilvántartó szolgáltatása partíció kvórum elvesztése kapott. |
| kvórum-adatveszteség-állapot | Lekérdezi a StartQuorumLoss API használatának partíciók a kvórum elvesztése műveletnek az előrehaladását. |
| Helyreállítás | Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani egy adott partícióra, amely a kvórum elvesztése jelenleg Beragadt, hogy. |
| helyreállítás – minden | A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a bármely (beleértve a rendszerszolgáltatások) szolgáltatást, amely a kvórum elvesztése jelenleg marad. |
| report-health | A Service Fabric-partíción health jelentést küld. |
| Indítsa újra a | Ez az API vagy egésze a replikák és a példány a megadott partíció újraindul. |
| Újraindítás-állapot | Lekérdezi a StartPartitionRestart használatának PartitionRestart műveletnek az előrehaladását. |
| SVC-név | Egy partíció Service Fabric-szolgáltatás nevének beolvasása. |

## <a name="sfctl-partition-data-loss"></a>sfctl partíció adatvesztés
Ez az API fog idéz elő adatvesztés, a megadott partíció.

A partíció OnDataLossAsync API hívása indít el.  Ez az API fog idéz elő adatvesztés, a megadott partíció. A partíció OnDataLoss API hívása indít el. A megadott DataLossMode tényleges adatvesztés függ. <br> PartialDataLoss - replikák csak egy kvórum törlődnek, és OnDataLoss akkor váltódik ki, a partíció, de tényleges adatvesztés üzenetsoroktól replikációs jelenléte függ. <br>FullDataLoss - összes replika vannak OnDataLoss aktiválódik, és ezért minden adat elveszik. <br>Ez az API csak akkor szabad meghívni egy állapotalapú szolgáltatással és a cél. Ez egy rendszer szolgáltatással és a cél API hívása nem ajánlott. 
> [!NOTE]
> Ha ez az API meghívása nem lehet visszavonni. CancelOperation hívása csak a végrehajtás le, és belső rendszerállapot tisztítása. Az adatokat nem azt fogja vissza, ha a parancs feldolgozásáig hol adatvesztést okozhat. A GetDataLossProgress API-t adott vissza adatokat a műveletnek az API használatába a azonos OperationID azonosítójú hívja.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --adatok elvesztését – módú [szükséges] | Az enum adatvesztés, hogy milyen típusú jelzi a StartDataLoss API lett átadva. |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partíció adatok elvesztését-állapot
Lekérdezi a StartDataLoss API használatának partíció adatok elvesztését műveletnek az előrehaladását.

Lekérdezi a StartDataLoss, használja a OperationID azonosítójú használatába adatok elvesztését műveletnek az előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-health"></a>sfctl partíció állapota
A megadott Service Fabric-partíción állapotának beolvasása.

Lekérdezi a megadott partíció üzemállapotával kapcsolatos adatokat. Az EventsHealthStateFilter küldött, az állapota alapján a szolgáltatás állapotával kapcsolatos események gyűjtése szűréséhez használja. A ReplicasHealthStateFilter használatával szűrhetők a partíción ReplicaHealthState objektumok gyűjteménye. Javasoljuk, hogy a health Store adatbázisban nem létezik ad meg, ha a kérelem hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |
| --replicas-health-state-filter | Az Objektumgyűjtemény ReplicaHealthState a partíción szűrését teszi lehetővé. Az érték tagokat, vagy bitenkénti műveletek HealthStateFilter tagjai lehet lekérni. Az eredmény csak a szűrőnek megfelelő replikákat. Összes replika fogja kiértékelni összesített állapotát. Ha nincs megadva, az összes bejegyzés visszatér. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események visszatér. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-info"></a>sfctl partíció adatait
A Service Fabric-partíción információ lekérése.

A megadott partíció információ lekérése. A válasz tartalmazza a Partícióazonosító particionálási sémát információkat, a partíció, állapot, állapotát és egyéb adatait a partíció által támogatott kulcsok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-list"></a>sfctl partition listában
Lekérdezi a Service Fabric-szolgáltatás partícióinak listáját.

Lekérdezi a Service Fabric-szolgáltatás partícióinak listáját. A válasz tartalmazza a Partícióazonosító particionálási sémát információkat, a partíció, állapot, állapotát és egyéb adatait a partíció által támogatott kulcsok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-load"></a>sfctl partíció betöltése
Lekérdezi a terhelés adatokat a megadott Service Fabric-partíción.

A megadott partíció terhelés információt ad vissza. A válasz a Service Fabric-partíciókhoz terhelés jelentések listáját tartalmazza. Az egyes jelentések tartalmazza a terhelési metrika neve, érték és utolsó jelentett időpontja UTC szerint.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-load-reset"></a>sfctl partíció terheléselosztási állítja vissza
Alaphelyzetbe állítja a jelenlegi terhelés a Service Fabric-partíción.

A jelenlegi terhelés a Service Fabric-partíción visszaállítja az alapértelmezett betöltési szolgáltatás.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-quorum-loss"></a>kvórum-elvesztése sfctl partíció
Egy adott állapot-nyilvántartó szolgáltatása partíció kvórum elvesztése kapott.

Egy adott állapot-nyilvántartó szolgáltatása partíció kvórum elvesztése kapott.  Ez az API egy átmeneti kvórum elvesztése helyzetben a szolgáltatásban. A GetQuorumLossProgress API-t adott vissza adatokat a műveletnek az API használatába a azonos OperationID azonosítójú hívja. Ez csak nem hívható meg a állapotalapú alkalmazások és szolgáltatások megőrzött (HasPersistedState == true) szolgáltatásokat.  Használja az API állapotmentes szolgáltatásokhoz vagy állapot-nyilvántartó memórián belüli csak szolgáltatások.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --kvórum-adatveszteség-időtartam [szükséges] | Az időtartam, amelynek a partíció kvórumveszteségben megmarad.  Ez meg kell adni másodpercben. |
| – [szükséges] kvórum-adatveszteség-mód | Ez az enumerálás a kvórum elvesztése, hogy milyen típusú jelzéséhez StartQuorumLoss API lett átadva. |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partíció kvórum-adatveszteség-állapot
Lekérdezi a StartQuorumLoss API használatának partíciók a kvórum elvesztése műveletnek az előrehaladását.

Lekérdezi a StartQuorumLoss, használja a megadott OperationID azonosítójú használatába kvórum elvesztése műveletnek az előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-recover"></a>sfctl partíció helyreállítása
Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani egy adott partícióra, amely a kvórum elvesztése jelenleg Beragadt, hogy.

Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani egy adott partícióra, amely a kvórum elvesztése jelenleg Beragadt, hogy. Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-recover-all"></a>helyreállítás – minden sfctl partíció
A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a bármely (beleértve a rendszerszolgáltatások) szolgáltatást, amely a kvórum elvesztése jelenleg marad.

A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a bármely (beleértve a rendszerszolgáltatások) szolgáltatást, amely a kvórum elvesztése jelenleg marad. Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-report-health"></a>a jelentés-egészségügyi sfctl partíció
A Service Fabric-partíción health jelentést küld.

A jelentés a megadott Service Fabric-partíción állapotát. A jelentés az állapotjelentést és amelyen jelentett tulajdonság információt kell tartalmaznia. A jelentést küld a Service Fabric átjáró partíció található, amely továbbítja a health Store adatbázisban. A jelentés lehet, hogy fogadja el az átjárót, de elutasította a health Store adatbázisban a felesleges érvényesítése után. A health Store adatbázisban például utasíthatja a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Tekintse meg, hogy a jelentés a health Store adatbázisban alkalmazta, ellenőrizze, hogy a jelentés az események szakaszában jelenik meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [szükséges] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterlánc, és nem a jelentéskészítő rugalmas a jelentés állapot feltétel kategorizálásának rögzített enumerálási tulajdonság. Olyan jelentéskészítői SourceId "LocalWatchdog" a figyelheti például a csomóponton a rendelkezésre álló lemez állapotának, jelentést ezen a csomóponton "AvailableDisk" tulajdonság. Az azonos jelentéskészítői figyelheti a csomópont csatlakozási, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események a megadott csomópont tekintendők. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [szükséges] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Warning", "Error", "Ismeretlen". |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – [szükséges] adatforrás-azonosítója | A forrás nevét, amely azonosítja az ügyfél, a figyelő vagy a system összetevő, az egészségügyi adatokat generált. |
| – Leírás | Az állapotadatok leírása. <br><br> Adja hozzá a jelentés emberi olvasható információk segítségével szabad szöveg jelzi. A leírás maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor a program automatikusan levágja. Csonkolva lesz, ha az utolsó karakter, a leírás tartalmazza-e a mutatóhoz "[Truncated]", és teljes karaktersorozat 4096 karakternél. A jelölő jelzi a felhasználóknak, hogy csonkolása történt. Vegye figyelembe, hogy csonkolja, amikor a leírás legfeljebb 4096 karakternél az eredeti karakterláncból. |
| – azonnali | A jelzőt, amely azt jelzi, hogy a jelentés azonnal kell küldeni. <br><br> Health jelentést küld a Service Fabric átjáró alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz értéke esetén a jelentés azonnal gépről küldött a health Store adatbázisban, függetlenül a fabric ügyfélbeállításaihoz a http-átjáró alkalmazások által használt HTTP-átjárót. Ez akkor hasznos, amelyek a lehető leghamarabb küldjön kritikus jelentésekhez. Attól függően, hogy időzítési és egyéb feltételek a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Immediate értéke HAMIS, ha a jelentés megfelelően küldi el az egészségügyi ügyfélbeállításokat a HTTP-átjáróról. Ezért azt fogja lehet kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az ajánlott beállítása, mivel így az egészségügyi ügyfél állapotát a health Store adatbázisban, valamint a rendszerállapot jelentésfeldolgozás üzenetek reporting optimalizálása érdekében. Alapértelmezés szerint jelentések nem küldése azonnal. |
| --eltávolítása Ha lejárt | Érték, amely azt jelzi, hogy a jelentés eltávolítsa őket a health Store adatbázisban, ha a lejár. <br><br> Ha true értéke esetén a jelentés távolítja el a health Store adatbázisban lejárata után. Ha FALSE értéke esetén a jelentés hibát adott vissza, ha lejárt a rendszer kezeli. Ez a tulajdonság értéke HAMIS, alapértelmezés szerint. Ha az ügyfelek rendszeresen jelentést, azok RemoveWhenExpired hamis (alapértelmezés) kell beállítani. Ezzel a módszerrel a jelentéskészítő problémák (pl. holtpont), és nem tud jelenteni, az entitás értékelik ki: Hiba történt, amikor az állapotjelentést lejár. Tartalomként jelöli meg, hogy hiba állapotban lévő entitást. |
| --sorszám | Az állapotjelentés numerikus karakterláncként sorszáma. <br><br> A jelentés sorszám használják a health Store adatbázisban elavult jelentések észleléséhez. Ha nincs megadva, egy megfelelő sorszám jön létre automatikusan a rendszerállapot-ügyfél által a jelentés hozzáadásakor. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| --élettartam | Az időtartam, amelynek a jelentés érvénytelen. Ez a mező ISO8601 formátumot használja az időtartam megadása. <br><br> Ha az ügyfelek rendszeresen jelentést, élettartam-nál nagyobb gyakorisággal kell küldjön jelentéseket. Az átmenet jelentik, ha azok az élettartamnak a végtelen állíthatja be. Élettartam lejár, az egészségügyi adatokat tartalmazó állapotesemény esetén meg kell vagy eltávolítani a health Store adatbázisból, ha RemoveWhenExpired true, vagy értékeli ki a hibát, ha RemoveWhenExpired hamis. Ha nem az alapértelmezett érték a végtelen értéket élő adott, ideje. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-restart"></a>sfctl partíció újraindítása
Ez az API vagy egésze a replikák és a példány a megadott partíció újraindul.

Ez az API akkor hasznos, a feladatátvétel tesztelése. Ha célként egy állapotmentes szolgáltatások partíció, restartpartitionmode objektum AllReplicasOrInstances kell lennie. Az a folyamat az azonos OperationID azonosítójú segítségével GetPartitionRestartProgress API hívása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – Újraindítás partíció-módú [szükséges] | Indítsa újra a partíciók ismertetik. |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-restart-status"></a>sfctl partíció újraindítás-állapot
Lekérdezi a StartPartitionRestart használatának PartitionRestart műveletnek az előrehaladását.

Lekérdezi a használatával a megadott OperationID azonosítójú StartPartitionRestart használatába PartitionRestart előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-partition-svc-name"></a>sfctl partíció svc-neve
Egy partíció Service Fabric-szolgáltatás nevének beolvasása.

Lekérdezi a megadott partíció a szolgáltatás nevét. 404-es hibaüzenetet ad vissza, ha a Partícióazonosító nem létezik a fürt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
