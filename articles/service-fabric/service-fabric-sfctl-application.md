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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: dc57c813a6aecabc21ac3931b7294bce909778d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-application"></a>sfctl alkalmazás
Létrehozása, törlése és alkalmazásokat és típusok.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás       | A Service Fabric-alkalmazás a megadott leírás használatával hoz létre.|
| törlése       | Egy meglévő Service Fabric-alkalmazás törlése.|
| Telepített     | A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.|
| telepített állapota | Az állapotfigyelő szolgáltatás telepíthető az alkalmazás információ lekérése
                      A fabric-csomópont.|
| telepített listája| A Service Fabric-csomópont telepíthető alkalmazások listájának lekérése.|
| Rendszerállapot       | A service fabric-alkalmazás állapotának beolvasása.|
| információ         | A Service Fabric-alkalmazás információ lekérése.|
| lista         | A Service Fabric-fürt létrehozása megfelelő alkalmazások listájának beolvasása
                      a paraméterként megadott szűrőket.|
| betöltés | Lekérdezi a Service Fabric-alkalmazás adatainak betöltése. |
| Manifest     | A jegyzékfájl alkalmazástípust leíró lekérése.|
| Kiépítés    | Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt.|
| a jelentés-állapota| A Service Fabric-alkalmazás állapotának jelentést küld.|
| type         | Alkalmazás típusa megfelelő Service Fabric-fürt listájának beolvasása
                      pontosan a megadott név.|
| Típus-listában    | A Service Fabric-fürt alkalmazástípus listájának beolvasása.|
| Unprovision  | Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése.|
| Frissítés      | Elindul az alkalmazás a Service Fabric-fürt frissítése.|
| frissítés-folytatása  | Az alkalmazás a Service Fabric-fürt frissítése folytatása.|
| frissítés-visszaállítás| A jelenleg folyamatban frissítés, amely az alkalmazás visszaállítása elindítja a
                      Service Fabric-fürt.|
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
| --maximális csomópont-darabszám     | Az, hogy a Service Fabric lefoglalt kapacitás az alkalmazás csomópontok maximális száma. Ez nem jelenti azt, hogy a szolgáltatások, az alkalmazás összes azokat a csomópontokat kerülnek.|
| --metrikák            | A JSON kódolású alkalmazás kapacitás metrika leírását listája. Egy metrika van definiálva, egy nevet, az alkalmazás megtalálható minden csomópontja számára kapacitások vannak beállítva.|
| --min-csomópont-száma     | Az, hogy a Service Fabric lefoglalt kapacitás az alkalmazás csomópontok minimális száma. Ez nem jelenti azt, hogy a szolgáltatások, az alkalmazás összes azokat a csomópontokat kerülnek.|
| --Paraméterek         | A JSON-kódolású alkalmazás paraméter a beállítás felülbírálja az alkalmazás létrehozásakor alkalmazandó.|
| – időtúllépés -t         | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése              | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
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
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. -Től kezdődő
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| – force-eltávolítása |} Távolítsa el a Service Fabric-alkalmazás vagy szolgáltatás kényszerített módon a biztonságos leállításának feladatütemezési áthaladás nélkül. Ez a paraméter használható kényszerített módon törli egy alkalmazás vagy szolgáltatás mely törlésre van, amely megakadályozza, hogy a kódjának hibái miatt csatornainicializálásnak szabályos zárja be a replikák. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-deployed"></a>sfctl alkalmazása telepítve van.
A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.|
|     
### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. -Től kezdődő
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --csomópontnév [szükséges] |} A csomópont neve. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
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
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. Kiindulva, 6.0-s verzió
                                                 hierarchical names are delimited with the "~"
                                                 character. For example, if the application name is
                                                 "fabric://myapp/app1", the application identity
                                                 would be "myapp~app1" in 6.0+ and "myapp/app1" in
                                                 previous versions.|
| --telepített-alkalmazások – rendszerállapot--Állapotszűrő |} Lehetővé teszi, hogy a telepített alkalmazások állapotának állapot objektumok szűrése az eredmény abban a állapota alapján alkalmazás állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Az eredmény csak a szűrőnek megfelelő központilag telepített alkalmazások. Minden központilag telepített alkalmazások összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével.                        Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) a HealthState értékét központilag telepített alkalmazások állapotának is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik.                        A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt.                        Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |} | --események – rendszerállapot-Állapotszűrő |} A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek.                        Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |} | --kizárási állapotstatisztika |} Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |} | --szolgáltatások – rendszerállapot-Állapotszűrő |} Lehetővé teszi, hogy a szolgáltatások állapotát állapot objektumok szűrése az eredmény abban a állapota alapján szolgáltatások állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő szolgáltatásokat is megjelennek. Minden szolgáltatás összesített állapotát értékeléséhez használt.                        Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd HealthState OK (2) és a figyelmeztető (4) értékkel rendelkező szolgáltatások állapotának visszatér. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.                        -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                                 | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-info"></a>Alkalmazásinformáció sfctl
A Service Fabric-alkalmazás információ lekérése.

Vagy a Service Fabric-fürt, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban létrehozott alkalmazással kapcsolatos információkat ad vissza. A válasz tartalmazza a neve, típusa, állapot, paraméterek és egyéb az alkalmazás részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. Hierarchikus nevek vannak tagolva kiindulva 6.0-s verzió,
                                      with the "~" character. For example, if the application name
                                      is "fabric://myapp/app1", the application identity would be
                                      "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --kizárási-alkalmazás-paraméterek |} A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredmény. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                      | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                    | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                  | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.             Alapértelmezett: JSON-ná.|
| --lekérdezés                      | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                    | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-list"></a>sfctl – Alkalmazáslista
A Service Fabric-fürt létrehozása a paraméterként megadott szűrőknek megfelelő alkalmazások listájának lekérése.

A létrehozott vagy folyamatban van, a Service Fabric létrehozása a fürt és a paraméterként megadott szűrőknek megfelelő alkalmazások információ lekérése. A válasz tartalmazza a neve, típusa, állapot, paraméterek és egyéb az alkalmazás részleteit. Az alkalmazások nem férnek el a lapon, ha egy oldalra, valamint a folytatási kód, a következő lap eléréséhez használt adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
|– alkalmazás-definíció-jellegű-szűrő| ApplicationDefinitionKind a szűréshez használt
                                          application query operations. - Default - Default value.
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 0. - All -
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 65535. -
                                          ServiceFabricApplicationDescription - Filter that matches
                                          input with ApplicationDefinitionKind value
                                          ServiceFabricApplicationDescription. The value is 1. -
                                          Compose - Filter that matches input with
                                          ApplicationDefinitionKind value Compose. The value is 2.
                                          Default: 65535.|
| --alkalmazás típusnév |} Az alkalmazás típusnév lekérdezni az alkalmazások szűrésére használatos. Ez az érték nem tartalmazhatja az alkalmazástípus verziója. |} | ---folytatási |} A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |} | --kizárási-alkalmazás-paraméterek |} A jelzőt, amely megadja, hogy alkalmazás paraméterei nem tartoznak az eredmény. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                      | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                    | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                  | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.             Alapértelmezett: JSON-ná.|
| --lekérdezés                      | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                    | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-load"></a>sfctl alkalmazásterhelés
Lekérdezi a Service Fabric-alkalmazás adatainak betöltése.

        Returns the load information about the application that was created or in the process of
        being created in the Service Fabric cluster and whose name matches the one specified as the
        parameter. The response includes the name, minimum nodes, maximum nodes, the number of nodes
        the app is occupying currently, and application load metric information about the
        application.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|– [szükséges] alkalmazás-azonosító| Az alkalmazás identitását. Ez általában annak a teljes neve
                                 the application without the 'fabric:' URI scheme. Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions. |
| – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|--hibakeresése                    | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
    – Súgó -h                  | Ez egy súgóüzenet és kilépési megjelenítése.|
    – a kimeneti -o                | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett:
                                 JSON-ná.|
    --lekérdezés                    | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további
                                 útmutatást és példákat.|
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
| --hibakeresése                           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                  Alapértelmezett: JSON-ná.|
| --lekérdezés                           | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-provision"></a>sfctl alkalmazás kiépítése
Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt.
        
Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt. Erre azért szükség, mielőtt példányosítható bármely új alkalmazások.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típus-build-elérési út [szükséges]| Az alkalmazáscsomag relatív lemezképet tároló elérési útja|
| – időtúllépés -t                         | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                              | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                            | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                          | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                              | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                            | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-type"></a>sfctl alkalmazás típusa

A Service Fabric-fürt pontosan a megadott névnek megfelelő alkalmazástípus listájának beolvasása.

A kiépített típusok vagy a Service Fabric-fürt telepítése folyamatban van olyan információkat ad vissza. Ezekkel az eredményekkel alkalmazástípus, amelynek neve egyezik pontosan a paraméterként megadott egy, és amelyek megfelelnek a megadott lekérdezési paramétereket. Az alkalmazás típusa névnek megfelelő alkalmazástípus összes verziója küld vissza a rendszer, egyes egy alkalmazás típusként visszaadott verzióval. A válasz tartalmazza, a név, verzió, állapota és egyéb adatait az alkalmazás típusa. Egy egy lapozható lekérdezés, ami azt jelenti, ha nem összes alkalmazás típusú oszlopnál lapon, az eredmények több oldalon adja vissza továbbá a folytatási kód, a következő oldalon eléréséhez használható. Például ha 10 alkalmazástípusokat, de a lap csak az első 3 alkalmazástípusok megfelelő, vagy az eredmények maximális értéke 3, majd 3 adja vissza. A többi eredményt eléréséhez beolvasása következő lapjain a következő lekérdezésben a visszaadott folytatási kód használatával. Egy üres folytatási kód eredményül, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve| Az alkalmazástípus neve.|
| ---folytatási           | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --kizárási-alkalmazás-paraméterek  | A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt.|
| --maximális-eredmények                  | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz mértékű eredmények, előfordulhat, hogy elférjen a visszatérési üzenetben.|
| – időtúllépés -t                   | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                        | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
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
| ---alkalmazástípus-verziója [szükséges]| Az alkalmazástípus verziója.|
| – időtúllépés -t                      | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                  Alapértelmezett: JSON-ná.|
| --lekérdezés                           | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-application-upgrade"></a>Az alkalmazásfrissítés sfctl
Elindul az alkalmazás a Service Fabric-fürt frissítése.

Ellenőrzi a megadott alkalmazás frissítési paramétereit, és elindítja a az alkalmazás frissítését, ha a paraméterek érvényesek. Ne feledje, hogy a frissítés leírása lecseréli a meglévő alkalmazás leírása. Ez azt jelenti, hogy ha a paraméter nincs megadva, a meglévő paraméterek alkalmazásokat felülíródik üres paraméterek listáját tartalmazó. Ez az alkalmazás jegyzékében származó paraméterek alapértelmezett értékét használó alkalmazások eredményez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-id [szükséges]| Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "~" karakterrel. A
        example, if the application name is 'fabric://myapp/app1', the application identity would be
        'myapp~app1' in 6.0+ and 'myapp/app1' in previous versions.|
| – az alkalmazás-verziója [szükséges] |} Cél Alkalmazásverzió. |} | – [szükséges] Paraméterek |} Egy JSON-kódolású alkalmazás paraméter a beállítás felülbírálja a alkalmazás verzióra alkalmazandó. |} | – alapértelmezett-szolgáltatás-állapotházirend |} JSON-kódolású állapotellenőrzése típusú egy szolgáltatás alapértelmezés szerint használt állapotházirend megadását. |} | --Hiba műveleti |} Milyen műveletet kell végrehajtani, amikor egy figyelt frissítés észlel figyelési házirend vagy állapotfigyelő házirend megsértésének. |} | – kényszerített újraindítás |} Kényszerített újraindítása a folyamatok még akkor is, a frissítés során a kód verziója nem változott. |} | – rendszerállapot-ellenőrzés-újrapróbálkozási-timeout |} Mennyi ideig, majd ismételje meg állapotfigyelő értékelések, amikor az alkalmazás vagy a fürt állapota nem megfelelő a sikertelen művelet előtt végrehajtása. Ezredmásodpercben.  Alapértelmezett: PT0H10M0S. |} | – rendszerállapot-ellenőrzés-stabil-időtartam |} Ennyi idő alatt, hogy az alkalmazás vagy a fürt kell maradnia kifogástalan előtt a frissítés előrehalad az a következő frissítési tartományra.            Ezredmásodpercben.  Alapértelmezett: PT0H2M0S. |} | – rendszerállapot-ellenőrzés-várakozási-időtartam |} A frissítési tartományok házirendek alkalmazása előtt befejezése után várjon időtartam. Ezredmásodpercben.            Alapértelmezett érték: 0. |} |} --maximális nem megfelelő alkalmazások |} Az engedélyezett maximális százalékos aránya a nem megfelelő a telepített alkalmazások. Jelenik meg egy 0 és 100 közötti számot. |} | --mód |} A működés közbeni frissítés során állapotának figyeléséhez használt módot.            Alapértelmezett: UnmonitoredAuto. |} | – a replika--ellenőrzés-időtúllépés beállítása |} A maximális időt blokkolhatja a frissítési tartományok feldolgozása, illetve a rendelkezésre állási az adatvesztés elkerülése érdekében, amikor nincsenek váratlan problémák. Mérése másodpercben történik. |} | --szolgáltatás állapotházirend |} JSON a service type állapotházirend szolgáltatás típusának neve / térkép kódolva. A térkép azonban üres alapértelmezett lehet. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |} | --frissítés-tartományi-timeout |} Mennyi ideig mindegyik frissítési tartományon van FailureAction végrehajtása előtt. Ezredmásodpercben.  Alapértelmezett: P10675199DT02H48M05.4775807S. |} | --frissítés-időtúllépés |} Ennyi idő alatt a összesített frissítéséből FailureAction végrehajtása előtt. Ezredmásodpercben.  Alapértelmezett: P10675199DT02H48M05.4775807S. |} | – Figyelmeztetés, hiba |} Állapotfigyelő értékelési figyelmeztetések a azonos kiegészített hibaként. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                     | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
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
|--lemezképtárolóba-karakterlánc| Cél lemezképet tárolja az alkalmazáscsomag feltöltéséhez.  Alapértelmezett:
                         háló: Lemezképtárolóba.|
| --megjelenítése van folyamatban  | A nagyméretű csomagok fájl feltöltési folyamatáról megjelenítése.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése       | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h     | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o   | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés       | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és
                       Példák.|
| – részletes     | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>Következő lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).