---
title: Az Azure Service Fabric CLI - sfctl partition |} Microsoft Docs
description: "A Service Fabric CLI sfctl partíció parancsok ismerteti."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 99756378f2106707b4f6d634a1183d5c32243ee2
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="sfctl-partition"></a>sfctl partíció
Lekérdezi és partíciók valamelyik szolgáltatás kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    adatvesztés      | Ez az API a megadott partíció adatvesztés kapott.|
|    adatok-adatveszteség-állapot  | Lekérdezi a StartDataLoss API használatának partíció adatok elvesztését műveletnek az előrehaladását.|
|    Rendszerállapot         | A megadott Service Fabric-partíción állapotának beolvasása.|
|    információ           | A Service Fabric-partíción információ lekérése.|
|    lista           | Lekérdezi a Service Fabric-szolgáltatás partícióinak listáját.|
|    betöltés           | Lekérdezi a megadott Service Fabric-partíción terhelését.|
|    betöltési-alaphelyzetbe állítása     | Alaphelyzetbe állítja a jelenlegi terhelés a Service Fabric-partíción.|
|    kvórum-elvesztése    | Egy adott állapot-nyilvántartó szolgáltatása partíció kvórum elvesztése kapott.|
|    kvórum-adatveszteség-állapot| Lekérdezi a StartQuorumLoss API használatának partíciók a kvórum elvesztése műveletnek az előrehaladását.|
|    Helyreállítás        | Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani egy adott partícióra, amely a kvórum elvesztése jelenleg Beragadt, hogy.|
|    helyreállítás – minden    | A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a bármely (beleértve a rendszerszolgáltatások) szolgáltatást, amely a kvórum elvesztése jelenleg marad.|
|    a jelentés-állapota  | A Service Fabric-partíción health jelentést küld.|
|    Indítsa újra a        | Ez az API vagy egésze a replikák és a példány a megadott partíció újraindul.|
|    Újraindítás-állapot | Lekérdezi a StartPartitionRestart használatának PartitionRestart műveletnek az előrehaladását.|
|    SVC-név       | Egy partíció Service Fabric-szolgáltatás nevének beolvasása.|


## <a name="sfctl-partition-health"></a>sfctl partíció állapota
A megadott Service Fabric-partíción állapotának beolvasása.

Lekérdezi a megadott partíció üzemállapotával kapcsolatos adatokat. Az EventsHealthStateFilter küldött, az állapota alapján a szolgáltatás állapotával kapcsolatos események gyűjtése szűréséhez használja.
A ReplicasHealthStateFilter használatával szűrhetők a partíción ReplicaHealthState objektumok gyűjteménye. Javasoljuk, hogy a health Store adatbázisban nem létezik megadása esetén a parancsmagnak hibát ad vissza. .

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| --események-rendszerállapot-állapot-szűrő  | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket.                Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.                -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel.                Az érték 65535.|
|--Állapotstatisztika kizárása   | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba.|
| --replikák-rendszerállapot-állapot-szűrő| Az Objektumgyűjtemény ReplicaHealthState a partíción szűrését teszi lehetővé. Az érték tagokat, vagy bitenkénti műveletek HealthStateFilter tagjai lehet lekérni. Csak a szűrőnek megfelelő replikák adja vissza. Összesített állapotát értékeléséhez használt összes replikát. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| – időtúllépés -t               | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                    | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                  | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                Alapértelmezett: JSON-ná.|
| --lekérdezés                    | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/. |
| – részletes                  | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-partition-info"></a>sfctl partíció adatait
A Service Fabric-partíción információ lekérése.

A partíciók végpont a megadott partíció információt ad vissza. A válasz tartalmazza a Partícióazonosító particionálási sémát információkat, a partíció, állapot, állapotát és egyéb adatait a partíció által támogatott kulcsok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-partition-list"></a>sfctl partition listában
Lekérdezi a Service Fabric-szolgáltatás partícióinak listáját.

Lekérdezi a Service Fabric-szolgáltatás partícióinak listáját. A s a Partícióazonosító és a particionálási sémát adatait, a kulcsok a partíció, állapot, állapotát és egyéb adatait a partíció által támogatott.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric://myapp/app1/svc1", a szolgáltatás identitás lenne "myapp ~ az app1 ~ svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban.|
| ---folytatási| A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál.         Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| – időtúllépés -t        | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése             | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h           | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o         | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés             | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes           | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-partition-load"></a>sfctl partíció betöltése
Lekérdezi a megadott Service Fabric-partíción terhelését.

A megadott partíció információt ad vissza. A válasz terhelés információk listáját tartalmazza. Minden egyes információk közé tartozik a terhelési metrika neve, az érték és a jelentett legutóbbi UTC formátumban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-partition-recover"></a>sfctl partíció helyreállítása
Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani egy adott partícióra, amely a kvórum elvesztése jelenleg Beragadt, hogy.

Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani egy adott partícióra, amely a kvórum elvesztése jelenleg Beragadt, hogy. Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-partition-restart"></a>sfctl partíció újraindítása
Ez az API vagy egésze a replikák és a példány a megadott partíció újraindul.

Ez az API akkor hasznos, a feladatátvétel tesztelése. Ha célként egy állapotmentes szolgáltatások partíció, restartpartitionmode objektum AllReplicasOrInstances kell lennie. Az a folyamat az azonos OperationID azonosítójú segítségével GetPartitionRestartProgress API hívása. .

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges]| Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva.|
| --partícióazonosító [szükséges]| A partíció identitását.|
| – Újraindítás partíció-módú [szükséges]| -Érvénytelen, mert a fenntartott.  API nem továbbítja. -AllReplicasOrInstances - összes replikák és a partíció-példány egyszerre újraindul. -OnlyActiveSecondaries - csak a másodlagos replikák újraindul. .|
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric://myapp/app1/svc1", a szolgáltatás identitás lenne "myapp ~ az app1 ~ svc1" 6.0 + és "myapp/app1/svc1" előző v ersions.|
| – időtúllépés -t                    | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                         | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                       | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                     | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                     Alapértelmezett: JSON-ná.|
| --lekérdezés                         | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes                       | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>Következő lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
