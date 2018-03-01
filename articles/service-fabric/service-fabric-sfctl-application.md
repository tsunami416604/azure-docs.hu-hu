---
title: "Azure Service Fabric CLI - sfctl alkalmazással |} Microsoft Docs"
description: "Ismerteti a Service Fabric CLI sfctl alkalmazás parancsait."
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
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 3a10437d0a2d680e586ada6a87750a69453c1f0c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-application"></a>sfctl-alkalmazás
Létrehozása, törlése és alkalmazásokat és típusok.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás       | A Service Fabric-alkalmazás a megadott leírás használatával hoz létre.|
| törlés       | Egy meglévő Service Fabric-alkalmazás törlése.|
| Telepített     | A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.|
| telepített állapota | Az alkalmazások telepítése a Service Fabric-csomópont állapotát információ lekérése.|
| telepített listája| A Service Fabric-csomópont telepíthető alkalmazások listájának lekérése.|
| állapot       | A service fabric-alkalmazás állapotának beolvasása.|
| információ         | A Service Fabric-alkalmazás információ lekérése.|
| lista         | A Service Fabric-fürt létrehozása a paraméterként megadott szűrőknek megfelelő alkalmazások listájának lekérése.|
| betöltés | Lekérdezi a Service Fabric-alkalmazás adatainak betöltése. |
| Manifest     | A jegyzékfájl alkalmazástípust leíró lekérése.|
| Kiépítés    | Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt a .sfpkg csomag segítségével a külső áruházban, vagy pedig az alkalmazáscsomag az image store.|
| report-health| A Service Fabric-alkalmazás állapotának jelentést küld.|
| type         | A Service Fabric-fürt pontosan a megadott névnek megfelelő alkalmazástípus listájának beolvasása.|
| Típus-listában    | A Service Fabric-fürt alkalmazástípus listájának beolvasása.|
| Unprovision  | Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése.|
| Frissítés      | Elindul az alkalmazás a Service Fabric-fürt frissítése.|
| frissítés-folytatása  | Az alkalmazás a Service Fabric-fürt frissítése folytatása.|
| frissítés-visszaállítás| Elindítja a Service Fabric-fürt visszaállítása az alkalmazás jelenleg folyamatban lévő frissítése folyamatban.|
| frissítés-állapot  | Lekérdezi a részletek a legújabb frissítés végre ezt az alkalmazást.|
| Feltöltése       | A Service Fabric-alkalmazás csomag másolása az image store.|

## <a name="sfctl-application-create"></a>sfctl alkalmazás létrehozása
A Service Fabric-alkalmazás a megadott leírás használatával hoz létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| [szükséges] – alkalmazás-neve| Az alkalmazás neve például a "fabric:" URI-séma.|
| [szükséges] – alkalmazás-típusa| Az alkalmazás típusnév található az alkalmazás jegyzékében.|
| – az alkalmazás-verziója [szükséges]| Az alkalmazás jegyzékében meghatározott az alkalmazástípus verziója.|
| --max-node-count     | Az, hogy a Service Fabric lefoglalt kapacitás az alkalmazás csomópontok maximális száma. Ez nem jelenti azt, hogy a szolgáltatások, az alkalmazás összes azokat a csomópontokat kerülnek.|
| --metrikák            | A JSON kódolású alkalmazás kapacitás metrika leírását listája. Egy metrika van definiálva, egy nevet, az alkalmazás megtalálható minden csomópontja számára kapacitások vannak beállítva.|
| --min-node-count     | Az, hogy a Service Fabric lefoglalt kapacitás az alkalmazás csomópontok minimális száma. Ez nem jelenti azt, hogy a szolgáltatások, az alkalmazás összes azokat a csomópontokat kerülnek.|
| --Paraméterek         | A JSON-kódolású alkalmazás paraméter a beállítás felülbírálja az alkalmazás létrehozásakor alkalmazandó.|
| – időtúllépés -t         | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug              | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h            | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o          | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés              | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes            | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-delete"></a>sfctl alkalmazás törlése
Egy meglévő Service Fabric-alkalmazás törlése.

Egy meglévő Service Fabric-alkalmazás törlése. Az alkalmazás azt törlése előtt létre kell hozni. Egy alkalmazás törlésekor az alkalmazás részét képező összes szolgáltatás. Alapértelmezés szerint a Service Fabric megpróbálja zárja be a szolgáltatás replikák biztonságos módon, és törölje a szolgáltatást. Azonban ha szolgáltatás szabályosan bezárása a replika problémákat okoz, a törlési művelet hosszú időt vehet igénybe vagy elakadnak. Használja a választható ForceRemove jelzőt a szabályos szekvenciazáró kihagyhatja, és az alkalmazás és az összes hozzá tartozó szolgáltatások kényszerített törlése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| --force-remove          | Távolítsa el a Service Fabric-alkalmazás vagy szolgáltatás kényszerített módon a biztonságos leállításának feladatütemezési áthaladás nélkül. Ez a paraméter használható kényszerített módon törli egy alkalmazás vagy szolgáltatás mely törlésre van, amely megakadályozza, hogy a kódjának hibái miatt csatornainicializálásnak szabályos replikák zárja be.|
| – időtúllépés -t            | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-deployed"></a>sfctl alkalmazása telepítve van.
A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.

A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.  A lekérdezés által visszaadott rendszer alkalmazással kapcsolatos adatok, ha a megadott Alkalmazásazonosító rendszer alkalmazáshoz. Eredmények foglalnia az aktív, aktiválása és állapotok letöltése a központilag telepített alkalmazások. Ez a lekérdezés megköveteli, hogy a csomópont neve megegyezik a fürt csomópontjait. A lekérdezés sikertelen lesz, ha a megadott csomópont neve nem mutat minden aktív Service Fabric-csomópontokon a fürtön.
     
### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| --csomópontnév [szükséges]| A csomópont neve.|
| – időtúllépés -t            | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-health"></a>alkalmazás állapotának sfctl
A service fabric-alkalmazás állapotának beolvasása.

A service fabric-alkalmazás heath állapotának beolvasása. A válasz Ok, hiba vagy figyelmeztetés állapotát jelzi. Ha az entitás a health Store adatbázisban nem található, hibaüzenetet adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| --deployed-applications-health-state-filter| Lehetővé teszi, hogy a telepített alkalmazások állapotának állapot objektumok szűrése az eredmény abban a állapota alapján alkalmazás állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Az eredmény csak a szűrőnek megfelelő központilag telepített alkalmazások. Minden központilag telepített alkalmazások összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) a HealthState értékét központilag telepített alkalmazások állapotának is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| --events-health-state-filter            | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| --exclude-health-statistics | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba.|
| --services-health-state-filter          | Lehetővé teszi, hogy a szolgáltatások állapotát állapot objektumok szűrése az eredmény abban a állapota alapján szolgáltatások állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő szolgáltatásokat is megjelennek. Minden szolgáltatás összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd szolgáltatások állapota az OK (2) és a figyelmeztető (4) a HealthState értékét adja vissza. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| – időtúllépés -t                            | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                                 | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes                               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-info"></a>Alkalmazásinformáció sfctl
A Service Fabric-alkalmazás információ lekérése.

Vagy a Service Fabric-fürt, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban létrehozott alkalmazással kapcsolatos információkat ad vissza. A válasz tartalmazza a neve, típusa, állapot, paraméterek és egyéb az alkalmazás részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| --kizárási-alkalmazás-paraméterek| A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt.|
| – időtúllépés -t                 | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                      | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                    | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                  | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.             Alapértelmezett: JSON-ná.|
| --lekérdezés                      | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes                    | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-list"></a>sfctl – Alkalmazáslista
A Service Fabric-fürt létrehozása a paraméterként megadott szűrőknek megfelelő alkalmazások listájának lekérése.

A létrehozott vagy folyamatban van, a Service Fabric létrehozása a fürt és a paraméterként megadott szűrőknek megfelelő alkalmazások információ lekérése. A válasz tartalmazza a neve, típusa, állapot, paraméterek és egyéb az alkalmazás részleteit. Az alkalmazások nem férnek el a lapon, ha egy oldalra, valamint a folytatási kód, a következő lap eléréséhez használt adja vissza. Szűrők ApplicationTypeName és ApplicationDefinitionKindFilter nem adható meg egyszerre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
|– alkalmazás-definíció-jellegű-szűrő| A ApplicationDefinitionKind, amelyen megadhatók az Service Fabric-alkalmazás használ szűrésére használatos. -Alapértelmezett - alapértelmezett értéket, amely az "All" kiválasztásával azonos funkciót hajt végre. Az érték 0. -Az összes - szűrő, amely megfelel a bemeneti bármely ApplicationDefinitionKind értékkel. Az érték 65535. -ServiceFabricApplicationDescription - szűrő, amely megfelel a bemeneti ServiceFabricApplicationDescription ApplicationDefinitionKind értékkel. Az érték 1. Egyező bemeneti ApplicationDefinitionKind értékű Compose - Compose - szűrő. A 2 érték.|
| ---alkalmazástípus-neve      | Az alkalmazás típusnév lekérdezni az alkalmazások szűrésére használatos. Ez az érték nem lehet az alkalmazástípus verziója.|
| ---folytatási         | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --kizárási-alkalmazás-paraméterek| A jelzőt, amely megadja, hogy alkalmazás paramétereit az eredmény nem tartoznak.|
| --max-results|A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezéseket annyi eredmények belefoglalása, előfordulhat, hogy elférjen a visszatérési üzenetben.|
| – időtúllépés -t                 | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                      | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                    | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                  | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.             Alapértelmezett: JSON-ná.|
| --lekérdezés                      | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                    | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-load"></a>sfctl alkalmazásterhelés
Lekérdezi a Service Fabric-alkalmazás adatainak betöltése.

A létrehozott alkalmazással kapcsolatban, vagy a Service Fabric-fürt, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban terhelés információkat ad vissza. A válasz a nevét, a minimális csomópontok, a csomópontok maximális száma, a csomópontok az alkalmazás jelenleg elfoglaló-e, és alkalmazás terhelési metrika az alkalmazással kapcsolatos információkat tartalmazza.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|– [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – időtúllépés -t               | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|--debug                    | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
    – Súgó -h                  | Ez egy súgóüzenet és kilépési megjelenítése.|
    – a kimeneti -o                | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
    --lekérdezés                    | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
    – részletes                  | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-manifest"></a>Az alkalmazásjegyzék sfctl
A jegyzékfájl alkalmazástípust leíró lekérése.
        
A jegyzékfájl alkalmazástípust leíró lekérése. A válasz tartalmazza az alkalmazás jegyzékében XML karakterláncként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve| Az alkalmazástípus neve.|
| ---alkalmazástípus-verziója [szükséges]| Az alkalmazástípus verziója.|
| – időtúllépés -t                      | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                  Alapértelmezett: JSON-ná.|
| --lekérdezés                           | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes                         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-provision"></a>sfctl alkalmazás kiépítése
Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt a SFPKG csomag segítségével a külső áruházban, vagy pedig az alkalmazáscsomag az image store.

A Service Fabric alkalmazástípus a fürttel látja el. Erre azért szükség, mielőtt példányosítható bármely új alkalmazások. A telepítési művelet végrehajtható, vagy egy megadott a relativePathInImageStore, vagy a külső SFPKG URI segítségével alkalmazáscsomagot. Ha – külső-provision be van állítva, ez a parancs vár lemezképtárolóhoz

kiépítéséhez.
        


### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-package-download-uri| Az elérési utat, amelyről a ".sfpkg" alkalmazáscsomagra alkalmazáscsomag letölthető HTTP vagy HTTPS protokollt használ. Csak egy külső store-ból történő üzembe helyezéséhez. Az alkalmazáscsomag tárolhatók a GET műveletet letölteni a fájlt biztosító külső áruházban. Támogatott protokollok HTTP és HTTPS, és az elérési út OLVASÁSI hozzáférést kell engedélyeznie.|
| – alkalmazás-típus-build-elérési útja       | Kiépítés kind lemezképtárolóhoz csak. Az alkalmazáscsomag a az image store az előzetes feltöltési művelet során megadott relatív elérési útja. |
| ---alkalmazástípus-neve| Csak egy külső store-ból történő üzembe helyezéséhez. Az alkalmazás neve megtalálható az alkalmazás jegyzékében alkalmazástípus nevét jelöli.|
| ---alkalmazástípus-verziója| Csak egy külső store-ból történő üzembe helyezéséhez. Az alkalmazástípus verziója található az alkalmazás jegyzékében az alkalmazástípus verziója jelöli.|
| --külső-kiépítés| A hely, ahol regisztrált vagy kiépítve a alkalmazáscsomagot. Azt jelzi, hogy a kiépítés külső tárolóhoz korábban feltöltött alkalmazás csomagjában. Az alkalmazáscsomag a bővítmény *.sfpkg végződik.|
| --nem-wait| Azt jelzi-e kiépítés jöjjön létre aszinkron módon.  Ha igaz értéke esetén a telepítési művelet visszaadja mikor fogadták el a kérelmet a rendszer, és a telepítési művelet által bármely időkorlátja nélkül folytatódik. Az alapértelmezett értéke hamis. A nagy alkalmazáscsomagok esetén ajánlott, TRUE értékre állítja.|
| – időtúllépés -t                      | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                              | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                            | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                          | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                              | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes                            | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-type"></a>sfctl alkalmazás típusa

A Service Fabric-fürt pontosan a megadott névnek megfelelő alkalmazástípus listájának beolvasása.

A kiépített típusok vagy a Service Fabric-fürt telepítése folyamatban van olyan információkat ad vissza. Ezekkel az eredményekkel alkalmazástípus, amelynek neve egyezik pontosan a paraméterként megadott egy, és amelyek megfelelnek a megadott lekérdezési paramétereket. Az alkalmazás típusa névnek megfelelő alkalmazástípus összes verziója küld vissza a rendszer, egyes egy alkalmazás típusként visszaadott verzióval. A válasz tartalmazza, a név, verzió, állapota és egyéb adatait az alkalmazás típusa. Egy egy lapozható lekérdezés, ami azt jelenti, ha nem összes alkalmazás típusú oszlopnál lapon, az eredmények több oldalon adja vissza továbbá a folytatási kód, a következő oldalon eléréséhez használható. Például ha 10 alkalmazástípusokat, de a lap csak az első 3 alkalmazástípusok megfelelő, vagy a maximális eredmények 3 értékre van beállítva, majd 3 adja vissza. A többi eredményt eléréséhez beolvasása következő lapjain a következő lekérdezésben a visszaadott folytatási kód használatával. Egy üres folytatási kód eredményül, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve| Az alkalmazástípus neve.|
| ---alkalmazástípus-verziója        | Az alkalmazástípus verziója.|
| ---folytatási           | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --kizárási-alkalmazás-paraméterek  | A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt.|
| --max-results                  | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz annyi eredményeket, előfordulhat, hogy elférjen a visszatérési üzenetben.|
| – időtúllépés -t                   | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                        | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                      | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                    | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.               Alapértelmezett: JSON-ná.|
| --lekérdezés                        | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                      | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-unprovision"></a>sfctl alkalmazás unprovision
Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése.

Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése. Ez a művelet csak akkor hajtható végre, ha az alkalmazás összes példányát az alkalmazástípus törölve lett. Az alkalmazástípus regisztrációjának törlése, ha új alkalmazáspéldány nem hozható létre az adott alkalmazás típusa.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve| Az alkalmazástípus neve.|
| ---alkalmazástípus-verziója [szükséges]| Az alkalmazás jegyzékében meghatározott az alkalmazástípus verziója.|
|--async-parameter                    | A jelző-e leépíteni aszinkron módon történjen-e. Ha igaz értéke esetén a unprovision művelet értéket ad eredményül, ha a rendszer elfogadta a kérést, és a unprovision továbbra is fennáll semmilyen időkorlátja nélkül. Az alapértelmezett értéke hamis. Azt javasoljuk azonban nagy alkalmazáscsomagok kiépített igaz értékre állítani.|
| – időtúllépés -t                      | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                  Alapértelmezett: JSON-ná.|
| --lekérdezés                           | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes                         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-upgrade"></a>Az alkalmazásfrissítés sfctl
Elindul az alkalmazás a Service Fabric-fürt frissítése.

Ellenőrzi a megadott alkalmazás frissítési paramétereit, és elindítja a az alkalmazás frissítését, ha a paraméterek érvényesek. Frissítés leírása a felváltja a meglévő alkalmazás leírása. Ez azt jelenti, hogy a paraméter nincs megadva, a meglévő paraméterek alkalmazásokat a rendszer felülírja üres paraméterek listáját tartalmazó. Ez az alkalmazás jegyzékében származó paraméterek alapértelmezett értékét használó alkalmazások eredményez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-id [szükséges]| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. Például, ha az alkalmazás neve nem "fabric: / myapp/app1", az Alkalmazásidentitás lenne "myapp ~ az app1" 6.0 + és "myapp/app1" korábbi verzióiban.|
| – az alkalmazás-verziója [szükséges]| Cél alkalmazás verziója.|
| – [szükséges] Paraméterek| Egy JSON-kódolású alkalmazás paraméter a beállítás felülbírálja a alkalmazás verzióra alkalmazandó.|
| --default-service-health-policy| JSON kódolású állapotellenőrzése típusú egy szolgáltatás alapértelmezés szerint használt állapotházirend megadását.|
| --failure-action            | Milyen műveletet kell végrehajtani, amikor egy figyelt frissítés figyelési házirend vagy állapotfigyelő házirend megsértésének észlel.|
| --force-restart             | Kényszerített újraindítása a folyamatok még akkor is, a frissítés során a kód verziója nem változott.|
| --health-check-retry-timeout| Mennyi ideig, majd ismételje meg állapotfigyelő értékelések, amikor az alkalmazás vagy a fürt állapota nem megfelelő a sikertelen művelet előtt végrehajtása. Ezredmásodpercben.  Alapértelmezett: PT0H10M0S.|
| --health-check-stable-duration | Ennyi idő alatt, hogy az alkalmazás vagy a fürt kell maradnia kifogástalan előtt a frissítés előrehalad az a következő frissítési tartományra.            Ezredmásodpercben.  Alapértelmezett: PT0H2M0S.|
| --health-check-wait-duration| A frissítési tartományok házirendek alkalmazása előtt befejezése után várjon időtartam. Ezredmásodpercben.            Alapértelmezett: 0.|
| --max-unhealthy-apps        | Az engedélyezett maximális százalékos aránya a nem megfelelő a telepített alkalmazások. Egy 0 és 100 közötti számot jelöli.|
| --mód                      | A működés közbeni frissítés során állapotának figyeléséhez használt módot.            Alapértelmezett: UnmonitoredAuto.|
| --replica-set-check-timeout | A maximális időt blokkolhatja a frissítési tartományok feldolgozása, illetve a rendelkezésre állási az adatvesztés elkerülése érdekében, amikor nincsenek váratlan problémák. Mérése másodpercben történik.|
| --service-health-policy     | JSON a service type állapotházirend szolgáltatás típusának neve / térkép kódolva. A térkép üres azonban alapértelmezés szerint.|
| – időtúllépés -t                | Időtúllépését másodpercben.  Alapértelmezett: 60.|
| --upgrade-domain-timeout    | Mennyi ideig mindegyik frissítési tartományon van FailureAction végrehajtása előtt. Ezredmásodpercben.  Default:            P10675199DT02H48M05.4775807S.|
| --frissítés – időtúllépés           | Ennyi idő alatt a összesített frissítéséből FailureAction végrehajtása előtt. Ezredmásodpercben.  Default:            P10675199DT02H48M05.4775807S.|
| --warning-as-error          | Állapotfigyelő értékelési figyelmeztetések a azonos kiegészített hibaként.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                     | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                   | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                 | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.            Alapértelmezett: JSON-ná.|
| --lekérdezés                     | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                   | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-upload"></a>sfctl alkalmazás feltöltése
A Service Fabric-alkalmazás csomag másolása az image store.

A fájl feltöltési folyamatáról opcionálisan megjeleníteni a csomagban. Töltse fel folyamatban van a rendszer elküldi `stderr`.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] elérési útja| Alkalmazáscsomag helyi elérési útja.|
|--imagestore-string| Cél lemezképet tárolja az alkalmazáscsomag feltöltéséhez.  Default: fabric:ImageStore.|
| --megjelenítése van folyamatban  | A nagyméretű csomagok fájl feltöltési folyamatáról megjelenítése.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug       | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h     | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o   | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés       | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes     | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
