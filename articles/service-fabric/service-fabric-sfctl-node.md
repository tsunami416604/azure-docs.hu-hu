---
title: "Az Azure Service Fabric CLI - sfctl csomópont |} Microsoft Docs"
description: "A Service Fabric CLI sfctl csomópont parancsok ismerteti."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 50c7fe38d8bf7b14adf437f85c758e465e7d231d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-node"></a>sfctl-csomópont
A csomópontok, amely egy fürt része legyen kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    letiltása       | A Service Fabric fürt csomópontokat a megadott inaktiválási szándék inaktiválása.|
|    engedélyezése        | A Service Fabric fürtcsomóponton, amely inaktív aktiválása.|
|    állapot        | Lekérdezi a Service Fabric-csomópont állapotát.|
|    információ          | Egy adott csomópont a információ lekérése a Service Fabric-fürt.|
|    lista          | A Service Fabric-fürt a csomópontok listájának beolvasása.|
|    betöltés          | Lekérdezi a terhelés adatokat a Service Fabric-csomópont.|
|    remove-state  | A Service Fabric értesíti az, hogy a csomópont a megőrzött állapot véglegesen eltávolították vagy elveszett.|
|    report-health | A Service Fabric-csomópont állapotát jelentést küld.|
|    Indítsa újra a       | A Service Fabric fürt csomópont újraindul.|
|    átmenet    | Elindítja vagy leállítja egy fürt csomópontja.|
|    átmenet-állapot| Lekérdezi egy StartNodeTransition használatának műveletnek az előrehaladását.|


## <a name="sfctl-node-disable"></a>sfctl csomópont letiltása
A Service Fabric fürt csomópontokat a megadott inaktiválási szándék inaktiválása.

A Service Fabric fürt csomópontokat a megadott inaktiválási szándék inaktiválása. Miután a inaktiválása folyamatban van, az inaktiválási szándék lehet növelni, de nem csökkenthető (például egy csomópont, mely szünet biztonsági mentés inaktív inaktiválhatók további újraindítást, de nem fordítva. Csomópontok előfordulhat, hogy újra kell aktiválni az aktiválás egy csomópont műveletet bármikor után azok inaktiválása használatával. Ha el az inaktiválást nem fejeződött be Ez törli az inaktiválást. Egy csomópont leáll, és ismét elérhető lesz közben inaktiválása továbbra is hozzá kell ezen a csomóponton szolgáltatások helyezhető előtt újra kell aktiválni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| --az inaktiválást-leképezés | Ismerteti a leképezés vagy a csomópont inaktiválása okát. |
| – időtúllépés -t       | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug            | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h          | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o        | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés            | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes          | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-enable"></a>sfctl csomópont engedélyezése
A Service Fabric-fürt csomópont, mely inaktív aktiválása.

Aktiválja a Service Fabric-fürt csomópont, mely inaktív. Amennyiben az aktív, a csomópont újra lesz új replikák elhelyezéséhez életképes célja, és a csomópont fennmaradó inaktív replikákat újraaktiválni vannak.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| – időtúllépés -t       | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug            | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h          | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o        | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés            | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes          | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-health"></a>sfctl csomópont állapota
Lekérdezi a Service Fabric-csomópont állapotát.

Lekérdezi a Service Fabric-csomópont állapotát. Az EventsHealthStateFilter használatával szűrhetők a csomóponton a állapota alapján jelentett állapotával kapcsolatos események gyűjtése. Ha a csomópont neve szerint a health Store adatbázisban nem létezik, akkor ez hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| --events-health-state-filter| A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| – időtúllépés -t             | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                  | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o              | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                  | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-info"></a>sfctl csomópont adatai
Egy adott csomópont a információ lekérése a Service Fabric-fürt.

Lekérdezi az adatokat egy adott csomópont a Service Fabric Cluster.The a válasz például a neve, állapota, azonosítója, rendszerállapot, hasznos üzemidő és egyéb a csomópont részletei.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| – időtúllépés -t       | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug            | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h          | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o        | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés            | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes          | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-list"></a>sfctl csomópontlista
A Service Fabric-fürt a csomópontok listájának beolvasása.

A Service Fabric-fürt a csomópontok listájának beolvasása. A válasz tartalmazza a neve, állapota, azonosítót, rendszerállapot, hasznos üzemidő és más a csomópont részletei.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási| A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat.      Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --node-status-filter| A csomópontok a NodeStatus alapján szűrését teszi lehetővé. Csak azoknak a csomópontoknak, amely megfelelő a megadott szűrőérték adja vissza. A szűrő értéke a következők egyike lehet. Alapértelmezett: alapértelmezett.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-load"></a>sfctl csomópont betöltése
Lekérdezi a terhelés adatokat a Service Fabric-csomópont.

A Service Fabric csomópont a load vagy a meghatározott kapacitás rendelkező metrikák terhelés adatainak beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| – időtúllépés -t       | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug            | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h          | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o        | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés            | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes          | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-restart"></a>sfctl csomópont újraindítása
A Service Fabric fürt csomópont újraindul.

Újraindítja a egy Service Fabric-fürt csomópontja, amely már el van indítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges]| A csomópont neve.|
| --create-fabric-dump  | Adja meg az igaz a háló munkaterület folyamat egy biztonsági másolat létrehozásához. Ez a kis-és nagybetűket.  Alapértelmezett: False.|
| --node-instance-id | A célcsomóponton Példányazonosítója. Ha Példányazonosító van megadva, a csomópont csak akkor, ha ez megegyezik-e az aktuális példány a csomópont újraindul. Alapértelmezett érték "0" megfelelő minden példány azonosítója. Get-csomópont lekérdezéssel Példányazonosítója lehet lekérdezni.  Alapértelmezett: 0.|
| – időtúllépés -t       | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug            | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h          | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o        | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés            | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes          | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-node-transition"></a>sfctl csomópont átmenet
Elindítja vagy leállítja egy fürt csomópontja.

Elindítja vagy leállítja egy fürt csomópontja.  Egy fürtcsomópont egy folyamatot, nem pedig magát az operációs rendszer példány.
Indítja el a csomópontot, adja át a "Start" NodeTransitionType paramétereként. Csomópont leállítása a NodeTransitionType paraméter át a "Stop". Ez az API - művelet kezdődik, amikor a az API-t adja vissza, a csomópont esetleg nem végzett még tér át. Az azonos OperationID azonosítójú lekérni a műveletnek az előrehaladását a GetNodeTransitionProgress hívja. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópont-példány-azonosító [szükséges]| A csomópont a célcsomóponton Példányazonosítója. Ez GetNodeInfo API-n keresztül kell meghatározni.|
| --csomópontnév [szükséges]| A csomópont neve.|
| – [szükséges] csomópont-átviteli-típusa| Hajtsa végre az áttérés típusát jelöli.                       NodeTransitionType.Start elindít egy leállított csomópont.                       NodeTransitionType. Állítsa le, hogy egy csomópont leáll. |
| --Műveletazonosító [szükséges]| Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva.|
| --stop-duration-in-seconds [Required]| Az időtartam (másodpercben), a csomópont tartása leállt.  A minimális értéke 600, a maximális hossz 14400. Az idő lejárta után a csomópont automatikusan leállást vissza.|
| – időtúllépés -t                      | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                       Alapértelmezett: JSON-ná.|
| --lekérdezés                           | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).