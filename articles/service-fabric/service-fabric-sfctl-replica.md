---
title: Az Azure Service Fabric CLI - sfctl replika |} Microsoft Docs
description: A Service Fabric CLI sfctl replika parancsok ismerteti.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: ba67a2a20d3f3e8e9fbccb2674cea500bfbde3fb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-replica"></a>sfctl-replika
A szolgáltatáspartíciók tartozó replikák kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    Telepített  | Lekérdezi a Service Fabric-csomópont telepített replika részleteit.|
|    telepített listája| Lekérdezi a Service Fabric-csomópont telepítve replikák listáját.|
|    állapot    | A Service Fabric állapotalapú szolgáltatási replika- vagy állapotmentes szolgáltatások állapotának beolvasása.|
|    információ      | A Service Fabric-partíción replika információ lekérése.|
|    lista      | A Service Fabric szolgáltatás partíció replikák információ lekérése.|
|    eltávolítás    | Eltávolítja a csomóponton futó szolgáltatás replika.|
|    report-health| A Service Fabric-replika health jelentést küld.|
|    Indítsa újra a   | A szolgáltatás a replika egy csomóponton futó megőrzött szolgáltatás újraindul.|


## <a name="sfctl-replica-deployed"></a>telepített sfctl replika
Lekérdezi a Service Fabric-csomópont telepített replika részleteit.

Lekérdezi a telepített Service Fabric-csomópont replika részleteit. Adatok tartalmazzák a szolgáltatás típusa, a szolgáltatás nevét, a jelenlegi szolgáltatásműveletet, a jelenlegi szolgáltatásműveletet start ideje, partícióazonosító:, replika/példány azonosítója, jelentett terhelés és egyéb információkat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| --partícióazonosító [szükséges]| A partíció identitását.|
| --másodpéldány-azonosító [szükséges]| A replika azonosítója.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-replica-health"></a>sfctl a replika állapota
A Service Fabric állapotalapú szolgáltatási replika- vagy állapotmentes szolgáltatások állapotának beolvasása.

A Service Fabric replika állapotának beolvasása. Az EventsHealthStateFilter használatával szűrhetők a replika állapota alapján jelentett állapotával kapcsolatos események gyűjtése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| --másodpéldány-azonosító [szükséges]| A replika azonosítója.|
| --események-rendszerállapot-állapot-szűrő| A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| – időtúllépés -t             | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                  | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o              | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                  | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes                | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-replica-info"></a>sfctl replika adatai
A Service Fabric-partíción replika információ lekérése.

A válasz tartalmazza az azonosítója, szerepkör, állapot, állapotát, csomópont nevét, hasznos Üzemidő, és más a replika adatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| --másodpéldány-azonosító [szükséges]| A replika azonosítója.|
| ---folytatási  | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-replica-list"></a>sfctl listáján
A Service Fabric szolgáltatás partíció replikák információ lekérése.

A GetReplicas végpont a megadott partíció a replikák vonatkozó adatokat ad vissza.
A respons például az azonosító, szerepkör, állapot, rendszerállapot, csomópont, hasznos üzemidő és más a replika adatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges]| A partíció identitását.|
| ---folytatási  | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha további eredménytelen majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és példákat.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-replica-remove"></a>sfctl replika eltávolítása
Eltávolítja a csomóponton futó szolgáltatás replika.

Ez az API a Service Fabric replika hiba replika távolítsa el a Service Fabric-fürt szimulálja. Az Eltávolítás bezárása után a replika, a replika a szerepkör átkerül a None, majd eltávolítja az összes a fürtből a replika állapota információt. Ez az API teszteli a replika állapota eltávolítás elérési útját, és a jelentés tartalék állandó elérési útja ügyfél API-k használatával szimulálja. Figyelmeztetés - ott nem ez az API használata esetén végrehajtott biztonsági ellenőrzések. Ez az API nem megfelelő használata állapotalapú szolgáltatások adatvesztést eredményezhet. Emellett a forceRemove jelző hatással van az egy folyamatban futó összes többi replika.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| --partícióazonosító [szükséges]| A partíció identitását.|
| --másodpéldány-azonosító [szükséges]| A replika azonosítója.|
| --force-remove        | Távolítsa el a Service Fabric-alkalmazás vagy szolgáltatás kényszerített módon a biztonságos leállításának feladatütemezési áthaladás nélkül. Ez a paraméter használható kényszerített módon törli egy alkalmazás vagy szolgáltatás mely törlésre van, amely megakadályozza, hogy a kódjának hibái miatt csatornainicializálásnak szabályos replikák zárja be.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és példákat.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-replica-restart"></a>sfctl replika újraindítása
A szolgáltatás a replika egy csomóponton futó megőrzött szolgáltatás újraindul.

A szolgáltatás a replika egy csomóponton futó megőrzött szolgáltatás újraindul. Figyelmeztetés - ott nem ez az API használata esetén végrehajtott biztonsági ellenőrzések. Ez az API nem megfelelő használata állapotalapú szolgáltatások rendelkezésre állásának elvesztéséhez vezethet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| --partícióazonosító [szükséges]| A partíció identitását.|
| --másodpéldány-azonosító [szükséges]| A replika azonosítója.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és példákat.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
