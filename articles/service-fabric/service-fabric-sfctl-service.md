---
title: Az Azure Service Fabric CLI - sfctl szolgáltatás |} Microsoft Docs
description: A Service Fabric CLI sfctl parancsok ismerteti.
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
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 5b30d3732ff00e5bb79e2d58a9f0b3e5b29dedf8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-service"></a>sfctl service
Létrehozása, törlése, és szolgáltatás, a típusú szolgáltatásokat és a service-csomagok kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    alkalmazás-neve       | Lekérdezi a szolgáltatás a Service Fabric-alkalmazás nevét.|
|    kód csomaglista | Lekérdezi a Service Fabric-csomópont telepítve kód csomagok listáját.|
|    létrehozás         | A leírás hoz létre a megadott Service Fabric-szolgáltatás.|
|    törlés         | Törli a meglévő Service Fabric-szolgáltatás.|
|    telepített típusa  | A Service Fabric-fürt egyik csomópontján központilag telepített alkalmazás egy megadott szolgáltatás típusú információ lekérése.|
|    telepített típus-listában| A Service Fabric-fürt egyik csomópontján telepített alkalmazások szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása.|
|    leírás    | Lekérdezi a meglévő Service Fabric szolgáltatás leírását.|
|get-container-logs| A Service Fabric-csomópont telepítve tároló tároló naplóinak beolvasása.|
|    állapot         | A megadott Service Fabric-szolgáltatás állapotának beolvasása.|
|    információ           | Az adott szolgáltatás a Service Fabric-alkalmazás tartozó információ lekérése.|
|    lista           | Az alkalmazás azonosítóját. a megadott alkalmazáshoz tartozó összes szolgáltatás információ lekérése|
|    Manifest       | A jegyzékfájl egy szolgáltatástípus leíró lekérése.|
|    csomag telepítése | Letölti a megadott szolgáltatás jegyzékben megadott csomópont kép gyorsítótárába társított csomagokat.|
|    csomag – állapot | A service-csomag állapota információ lekérése a Service Fabric-csomópont és az alkalmazás telepítve egy adott alkalmazáshoz.|
|    csomag-adatai   | Lekérdezi a Service Fabric-csomópont pontosan a megadott névnek megfelelő telepített service-csomagok listáját.|
|    csomag – lista   | Lekérdezi a Service Fabric-csomópont telepített service-csomagok listáját.|
|    Helyreállítás        | Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani a megadott szolgáltatás, amely a kvórum elvesztése jelenleg Beragadt, hogy.|
|    report-health  | A Service Fabric-szolgáltatás állapotának jelentést küld.|
|    megoldás        | Hárítsa el a Service Fabric-partícióhoz.|
|    Típus-listában      | A Service Fabric-fürtök kiépítése alkalmazástípusok által támogatott szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása.|
|    Frissítés         | Frissíti az adott szolgáltatást az adott frissítés leírása.|


## <a name="sfctl-service-create"></a>sfctl szolgáltatás létrehozása
A leírás hoz létre a megadott Service Fabric-szolgáltatás.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-id [szükséges]| Az alkalmazás identitását. Ez általában az a teljes azonosító nélkül az alkalmazás a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| --a név [szükséges]| A szolgáltatás neve. Ezt az azonosítót. gyermeke kell           Ez az a teljes név többek között a `fabric:` URI. Például szolgáltatás `fabric:/A/B` alkalmazás gyermeke `fabric:/A`.|
| --szolgáltatástípus [szükséges]| A szolgáltatás típusának neve.|
| --aktiválási mód     | Az aktiválás módja a service-csomag.|
| --megkötések         | Az egy elhelyezési korlátozás karakterláncként. Egy elhelyezési korlátozás csomópont-tulajdonságok logikai kifejezésen, és lehetővé teszik a szolgáltatás korlátozása adott csomópontok szolgáltatás követelményeinek megfelelően. Például szeretné elhelyezni a csomópontok NodeType esetén kék szolgáltatás adja meg a következőket: "NodeColor kék ==".|
| --korrelált szolgáltatás  | A célszolgáltatás függ a neve.|
| --korrelációs         | A szolgáltatás igazítás kapcsolatot használó meglévő szolgáltatással összefüggéseket.|
| --dns-name            | A létrehozandó szolgáltatás DNS-nevét. A Service Fabric DNS-rendszer szolgáltatás ezt a beállítást engedélyezni kell.|
| ---példányszám      | A példányok száma. Ez csak az állapotmentes szolgáltatások vonatkozik.|
| --int-séma          | Azt jelzi, hogy a szolgáltatás egységesen kell particionálható között számos különböző előjel nélküli egész számot.|
| --int-séma-száma    | Az egész belül partíciók száma időintervallumát (egységes egész partícióséma) kulcs létrehozásához.|
| rendszer-magas – int     | A kulcs egész tartomány, ha egységes egész partícióséma használatával végéig.|
| rendszer-alacsony – int      | A kulcs egészek tartományába, ha egységes egész partícióséma használatával elindítása.|
| --betöltési-metrikák        | Amikor terheléselosztás szolgáltatási csomópontjai között használt mérőszámok JSON-kódolású listája.|
| --min-replica-set-size| A replikakészlet minimális méretének beállítása egy számot. Ez csak állapotalapú szolgáltatások vonatkozik.|
| --move-cost           | A szolgáltatás áthelyezés költséget határozza meg. Lehetséges értékek a következők: "Nulla", "Alacsony", "Közepes", "Magas".|
| --nevű séma        | Azt jelzi, hogy a szolgáltatás több elnevezett partíciót kell rendelkeznie.|
| --nevű séma-listában   | JSON-kódolású listája particionálásához a szolgáltatás között, ha a nevesített partícióséma használatával.|
| --nem megőrzött állapot  | Igaz értéke esetén ez azt jelzi a szolgáltatás nem a helyi lemezen tárolt állandó állapotban vannak, vagy csak állapot tárolja a memóriában.|
| --elhelyezési-házirend-listájában  | JSON-kódolású elhelyezési házirendeket a szolgáltatás listája, és esetleg hozzá tartozó tartomány nevét. Házirendek lehetnek egy vagy több: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --kvórum-adatveszteség-wait    | A maximális időtartamot (másodpercben), amelynek partíció számára engedélyezett a kvórum elvesztése alkalmas állapotban. Ez csak állapotalapú szolgáltatások vonatkozik.|
| – a replika-újraindítás-wait| Az időtartam (másodpercben), ha egy replika leáll, és amikor létrejön egy új replika között. Ez csak állapotalapú szolgáltatások vonatkozik.|
| --Egypéldányos-séma    | Azt jelzi, a szolgáltatás kell rendelkezik egy olyan partíciót, vagy a nem particionált szolgáltatás.|
| --készenléti által a replika megőrzése  | A maximális időtartamot (másodpercben), mely készenléti replikák karbantartása eltávolításuk előtt. Ez csak állapotalapú szolgáltatások vonatkozik.|
| --állapotalapú alkalmazások és szolgáltatások            | Azt jelzi, hogy a szolgáltatás egy olyan állapotalapú szolgáltatás.|
| --az állapot nélküli           | Azt jelzi, hogy a szolgáltatás egy olyan állapot nélküli szolgáltatás.|
| --target-replica-set-size| A replikakészlet méretének beállítása egy számot. Ez csak állapotalapú szolgáltatások vonatkozik.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-delete"></a>sfctl szolgáltatás törlése
Törli a meglévő Service Fabric-szolgáltatás.

Törli a meglévő Service Fabric-szolgáltatás. Egy szolgáltatás akkor törlése előtt létre kell hozni. Alapértelmezés szerint a Service Fabric megpróbálja zárja be a szolgáltatás replikák biztonságos módon, és törölje a szolgáltatást. Azonban ha szolgáltatás szabályosan bezárása a replika problémákat okoz, a törlési művelet hosszú időt vehet igénybe vagy elakadnak. Használja a választható ForceRemove jelzőt a szabályos szekvenciazáró kihagyásához és a kényszerített módon törölni a szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve megkülönbözteti a fabric: / myapp/app1/svc1 ", a szolgáltatás identitás lenne" myapp ~ app1 ~ svc1 "6.0 + és" myapp/app1/svc1"a korábbi verziókban.|
| --force-remove      | Távolítsa el a Service Fabric-alkalmazás vagy szolgáltatás kényszerített módon a biztonságos leállításának feladatütemezési áthaladás nélkül. Ez a paraméter használható kényszerített módon törli egy alkalmazás vagy szolgáltatás mely törlésre van, amely megakadályozza, hogy a kódjának hibái miatt csatornainicializálásnak szabályos replikák zárja be.|
| – időtúllépés -t        | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése             | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h           | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o         | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés             | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes           | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-description"></a>sfctl szolgáltatás leírása
Lekérdezi a meglévő Service Fabric szolgáltatás leírását.

Lekérdezi a meglévő Service Fabric szolgáltatás leírását. A szolgáltatás kell létrehozni, mielőtt annak leírását érhető el.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric: / myapp/app1/svc1", a szolgáltatás identitás lenne "myapp ~ app1 ~ svc1" 6.0 + és "myapp/app1/svc1" a korábbi verziókban.|
| – időtúllépés -t        | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése             | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h           | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o         | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés             | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes           | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-health"></a>sfctl szolgáltatásának állapota
A megadott Service Fabric-szolgáltatás állapotának beolvasása.

Lekérdezi a megadott szolgáltatás az egészségügyi adatokat. Az EventsHealthStateFilter küldött, az állapota alapján a szolgáltatás állapotával kapcsolatos események gyűjtése szűréséhez használja. Használja a PartitionsHealthStateFilter szűrése adott vissza partíciók száma a gyűjteményben. Ha megad egy szolgáltatás, amely a health Store adatbázisban nem létezik, ez a parancsmag hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric: / myapp/app1/svc1", a szolgáltatás identitás lenne "myapp ~ app1 ~ svc1" 6.0 + és "myapp/app1/svc1" a korábbi verziókban.|
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
|--Állapotstatisztika kizárása     | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba.|
| --partíciók-rendszerállapot-állapot-szűrő| Lehetővé teszi, hogy a partíciók egészségügyi állapot objektumok szűrése az eredmény abban a állapota alapján szolgáltatás állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak azokat a partíciókat a szűrőnek megfelelő adja vissza. Minden olyan partíciónak összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban "6" majd állapotát a partíciók OK (2) és a figyelmeztető (4) a HealthState értékét is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik.                  A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| – időtúllépés -t                 | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                      | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                    | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                  | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                  Alapértelmezett: JSON-ná.|
| --lekérdezés                      | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és példákat.|
| – részletes                    | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-info"></a>sfctl szolgáltatás adatai
Az adott szolgáltatás a Service Fabric-alkalmazás tartozó információ lekérése.

A megadott Service Fabric-alkalmazáshoz tartozó megadott szolgáltatás információt ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric: / myapp/app1/svc1", a szolgáltatás identitás lenne "myapp ~ app1 ~ svc1" 6.0 + és "myapp/app1/svc1" a korábbi verziókban.|
| – időtúllépés -t            | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-list"></a>sfctl szolgáltatás listája
Az alkalmazás azonosítóját. a megadott alkalmazáshoz tartozó összes szolgáltatás információ lekérése

Az alkalmazás azonosítóját. a megadott alkalmazáshoz tartozó összes szolgáltatásokkal kapcsolatos adatokat adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| ---folytatási    | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --service-type-name     | A szolgáltatás típusának neve lekérdezni a szolgáltatások szűrésére használatos.|
| – időtúllépés -t            | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-manifest"></a>sfctl szolgáltatás jegyzék
A jegyzékfájl egy szolgáltatástípus leíró lekérése.

A jegyzékfájl egy szolgáltatástípus leíró lekérése. A válasz tartalmazza a szolgáltatás jegyzék XML karakterláncként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve| Az alkalmazástípus neve.|
| ---alkalmazástípus-verziója [szükséges]| Az alkalmazástípus verziója.|
| --jegyzékfájl-szolgáltatásnév [szükséges]| A Service Fabric-fürt alkalmazástípust részeként regisztrált szolgáltatás jegyzékfájl neve.|
| – időtúllépés -t                      | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                       Alapértelmezett: JSON-ná.|
| --lekérdezés                           | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és példákat.|
| – részletes                         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-recover"></a>sfctl szolgáltatás helyreállítása
Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani a megadott szolgáltatás, amely a kvórum elvesztése jelenleg Beragadt, hogy.

Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani a megadott szolgáltatás, amely a kvórum elvesztése jelenleg Beragadt, hogy. Ez a művelet csak akkor hajtható végre, ha nem működnek a replikák nem állíthatók vissza. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve megkülönbözteti a fabric: / myapp/app1/svc1 ", a szolgáltatás identitás lenne" myapp ~ app1 ~ svc1 "6.0 + és" myapp/app1/svc1"a korábbi verziókban.|
| – időtúllépés -t        | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése             | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h           | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o         | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés             | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes           | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-resolve"></a>sfctl szolgáltatás feloldása
Hárítsa el a Service Fabric-partícióhoz.

Hárítsa el a Service Fabric szolgáltatás partíció, a szolgáltatás replikák végpontjai beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric: / myapp/app1/svc1", a szolgáltatás identitás lenne "myapp ~ app1 ~ svc1" 6.0 + és "myapp/app1/svc1" a korábbi verziókban.|
| --partíció-kulcs-típusa| A partíciós kulcs típusa. E paraméter megadása kötelező, ha a szolgáltatás partícióséma Int64Range vagy név. A lehetséges értékek követi. -Nincs (1) – azt jelzi, hogy a PartitionKeyValue paraméter nincs megadva. Ez a partíciók particionálás egypéldányosként séma érvénytelen. Ez az alapértelmezett érték. Az érték 1. -Int64Range (2) – azt jelzi, hogy a PartitionKeyValue paraméter egy int64 partíciós kulcs. Ez a partíciók particionálás Int64Range séma érvénytelen. A 2 érték. -Nevű (3) – azt jelzi, hogy a PartitionKeyValue paraméter a partíció nevét. Ez a partíciók particionálás nevesített séma érvénytelen. Az érték 3.|
| --partíció-kulcs-érték  | Partíciós kulcs. Ez azért szükséges, ha a szolgáltatás partícióséma Int64Range vagy név.|
| --previous-rsp-version | A korábban fogadott válasz a verzió mező értéke. Ez azért szükséges, ha a felhasználó ismer, hogy az eredmény volt azonosítóértékeket korábban már elavult.|
| – időtúllépés -t        | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése             | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h           | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o         | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés             | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes           | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-service-update"></a>sfctl szolgáltatás frissítése
Frissíti az adott szolgáltatást az adott frissítés leírása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója| A célszolgáltatás frissítéséhez. Ez általában az a teljes azonosító nélkül a szolgáltatást a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha a szolgáltatás neve "fabric: / myapp/app1/svc1", a szolgáltatásidentitás lenne "myapp ~ az app1 ~ svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban.|
| --megkötések         | Az egy elhelyezési korlátozás karakterláncként. Egy elhelyezési korlátozás csomópont-tulajdonságok logikai kifejezésen, és lehetővé teszik a szolgáltatás korlátozása adott csomópontok szolgáltatás követelményeinek megfelelően. Például szeretné elhelyezni a csomópontok NodeType esetén kék szolgáltatás adja meg a következőket: "NodeColor kék ==".|
| --korrelált szolgáltatás  | A célszolgáltatás függ a neve.|
| --korrelációs         | A szolgáltatás igazítás kapcsolatot használó meglévő szolgáltatással összefüggéseket.|
| ---példányszám      | A példányok száma. Ez csak az állapotmentes szolgáltatások vonatkozik.|
| --betöltési-metrikák        | JSON-kódolású listája metrikák használni terheléselosztás csomópontjai között.|
| --min-replica-set-size| A replikakészlet minimális méretének beállítása egy számot. Ez csak állapotalapú szolgáltatások vonatkozik.|
| --move-cost           | A szolgáltatás áthelyezés költséget határozza meg. Lehetséges értékek a következők: "Nulla", "Alacsony", "Közepes", "Magas".|
| --elhelyezési-házirend-listájában  | JSON-kódolású elhelyezési házirendeket a szolgáltatás listája, és esetleg hozzá tartozó tartomány nevét. Házirendek lehetnek egy vagy több: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --kvórum-adatveszteség-wait    | A maximális időtartamot (másodpercben), amelynek partíció számára engedélyezett a kvórum elvesztése alkalmas állapotban. Ez csak állapotalapú szolgáltatások vonatkozik.|
| – a replika-újraindítás-wait| Az időtartam (másodpercben), ha egy replika leáll, és amikor létrejön egy új replika között. Ez csak állapotalapú szolgáltatások vonatkozik.|
| --készenléti által a replika megőrzése  | A maximális időtartamot (másodpercben), mely készenléti replikák karbantartása eltávolításuk előtt. Ez csak állapotalapú szolgáltatások vonatkozik.|
| --állapotalapú alkalmazások és szolgáltatások            | Azt jelzi, hogy a célként megadott szolgáltatás egy olyan állapotalapú szolgáltatás.|
| --az állapot nélküli           | Azt jelzi, hogy a célként megadott szolgáltatás egy olyan állapot nélküli szolgáltatás.|
| --target-replica-set-size| A replikakészlet méretének beállítása egy számot. Ez csak állapotalapú szolgáltatások vonatkozik.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
