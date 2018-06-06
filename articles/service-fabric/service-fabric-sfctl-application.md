---
title: Azure Service Fabric CLI - sfctl alkalmazással |} Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl alkalmazás parancsait.
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
ms.openlocfilehash: 3ecc5a03ff1847dc11c5a5047e35566a4e68fec2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763697"
---
# <a name="sfctl-application"></a>sfctl-alkalmazás
Létrehozása, törlése és alkalmazásokat és típusok.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | A Service Fabric-alkalmazás a megadott leírás használatával hoz létre. |
| törlés | Egy meglévő Service Fabric-alkalmazás törlése. |
| Telepített | A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése. |
| telepített állapota | Az alkalmazások telepítése a Service Fabric-csomópont állapotát információ lekérése. |
| telepített listája | A Service Fabric-csomópont telepíthető alkalmazások listájának lekérése. |
| állapot | A service fabric-alkalmazás állapotának beolvasása. |
| információ | A Service Fabric-alkalmazás információ lekérése. |
| lista | A Service Fabric-fürt létrehozása a megadott szűrőknek megfelelő alkalmazások listájának lekérése. |
| betöltés | Lekérdezi a Service Fabric-alkalmazás adatainak betöltése. |
| Manifest | A jegyzékfájl alkalmazástípust leíró lekérése. |
| Kiépítés | Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt a .sfpkg csomag segítségével a külső áruházban, vagy pedig az alkalmazáscsomag az image store. |
| report-health | A Service Fabric-alkalmazás állapotának jelentést küld. |
| type | A Service Fabric-fürt pontosan a megadott névnek megfelelő alkalmazástípus listájának beolvasása. |
| Típus-listában | A Service Fabric-fürt alkalmazástípus listájának beolvasása. |
| Unprovision | Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése. |
| Frissítés | Elindul az alkalmazás a Service Fabric-fürt frissítése. |
| frissítés-folytatása | Az alkalmazás a Service Fabric-fürt frissítése folytatása. |
| frissítés-visszaállítás | Elindítja a Service Fabric-fürt visszaállítása az alkalmazás jelenleg folyamatban lévő frissítése folyamatban. |
| frissítés-állapot | Lekérdezi a részletek a legújabb frissítés végre ezt az alkalmazást. |
| feltöltés | A Service Fabric-alkalmazás csomag másolása az image store. |

## <a name="sfctl-application-create"></a>sfctl alkalmazás létrehozása
A Service Fabric-alkalmazás a megadott leírás használatával hoz létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| [szükséges] – alkalmazás-neve | Az alkalmazás neve például a "fabric\:" URI-séma. |
| [szükséges] – alkalmazás-típusa | Az alkalmazás típusnév található az alkalmazás jegyzékében. |
| – az alkalmazás-verziója [szükséges] | Az alkalmazás jegyzékében meghatározott az alkalmazástípus verziója. |
| --max-node-count | Ha a Service Fabric rendszer tartalékkapacitás ehhez az alkalmazáshoz csomópontok maximális száma. Vegye figyelembe, hogy ez nem jelenti azt, hogy az alkalmazás szolgáltatások helyez el a összes azokat a csomópontokat. |
| --metrikák | A JSON kódolású alkalmazás kapacitás metrika leírását listája. Egy metrika van definiálva, egy nevet, az alkalmazás megtalálható minden csomópontja számára kapacitások vannak beállítva. |
| --min-csomópont-száma | Ha a Service Fabric rendszer tartalékkapacitás ehhez az alkalmazáshoz csomópontok minimális száma. Vegye figyelembe, hogy ez nem jelenti azt, hogy az alkalmazás szolgáltatások helyez el a összes azokat a csomópontokat. |
| --Paraméterek | A JSON-kódolású alkalmazás paraméter a beállítás felülbírálja az alkalmazás létrehozásakor alkalmazandó. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-delete"></a>sfctl alkalmazás törlése
Egy meglévő Service Fabric-alkalmazás törlése.

Egy meglévő Service Fabric-alkalmazás törlése. Az alkalmazás azt törlése előtt létre kell hozni. Az alkalmazás törlése eltávolítja az alkalmazás részét képező összes szolgáltatás. A Service Fabric alapértelmezés szerint megpróbálja zárja be a szolgáltatás replikák biztonságos módon, és törölje a szolgáltatást. Azonban ha egy szolgáltatás szabályosan bezárása a replika problémákat okoz, a delete művelet hosszú időt vehet igénybe vagy elakadnak. Használja a választható ForceRemove jelzőt a szabályos szekvenciazáró kihagyhatja, és az alkalmazás és az összes hozzá tartozó szolgáltatások kényszerített törlése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --force-remove | Távolítsa el a Service Fabric-alkalmazás vagy szolgáltatás kényszerített módon a biztonságos leállításának feladatütemezési áthaladás nélkül. Ez a paraméter használható kényszerített módon törli egy alkalmazás vagy szolgáltatás mely törlésre van, amely megakadályozza, hogy a kódjának hibái miatt csatornainicializálásnak szabályos replikák zárja be. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-deployed"></a>sfctl alkalmazása telepítve van.
A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.

A Service Fabric-csomópont központi telepítésű alkalmazás információ lekérése.  A lekérdezés által visszaadott rendszer alkalmazással kapcsolatos adatok, ha a megadott Alkalmazásazonosító rendszer alkalmazáshoz. Eredmények foglalnia az aktív, aktiválása és állapotok letöltése a központilag telepített alkalmazások. Ez a lekérdezés megköveteli, hogy a csomópont neve megegyezik a fürt csomópontjait. A lekérdezés sikertelen lesz, ha a megadott csomópont neve nem mutat minden aktív Service Fabric-csomópontokon a fürtön.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --tartalmazzák--állapota | Egy entitás állapotát tartalmazza. Ha a paraméter értéke HAMIS, vagy nincs megadva, akkor visszaadott állapotát értéke "Ismeretlen". Ha true értéke esetén a lekérdezés kerül a csomópont-és az állapotszolgáltatás rendszer párhuzamosan előtt egyesítve lesznek az eredmények. A lekérdezés eredményeként drágább, és hosszabb időt vehet igénybe. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-deployed-health"></a>telepített alkalmazás sfctl-állapota
Az alkalmazások telepítése a Service Fabric-csomópont állapotát információ lekérése.

Az alkalmazások telepítése a Service Fabric-csomópont állapotát információ lekérése. Az EventsHealthStateFilter HealthEvent objektumok állapota alapján a telepített alkalmazás jelentett gyűjtemény opcionálisan szűréséhez használja. A DeployedServicePackagesHealthStateFilter DeployedServicePackageHealth számára készült állapota alapján opcionálisan szűréséhez használja.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --Deployed-Service-Packages-Health-State-Filter | Lehetővé teszi, hogy a telepített szolgáltatások csomag egészségügyi állapot visszaadott objektumok a telepített alkalmazás állapotának lekérdezés eredménye szűrését a állapota alapján. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak telepített csomagokat a szűrőnek megfelelő visszaadott szolgáltatás. A telepített alkalmazás összesített állapotának értékeléséhez használt összes telepített szervizcsomagok. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet egy bitenkénti "Vagy" operátor segítségével értékek együtt. Például ha a megadott érték azonban 6 majd állapotát a szolgáltatás csomagok OK (2) és a figyelmeztető (4) a HealthState értékét is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-deployed-list"></a>sfctl telepített – Alkalmazáslista
A Service Fabric-csomópont telepíthető alkalmazások listájának lekérése.

A Service Fabric-csomópont telepíthető alkalmazások listájának lekérése. Az eredmények nem tartalmaznak telepített rendszer alkalmazások adatait kivéve, ha explicit módon kellettek az azonosítót. Eredmények foglalnia az aktív, aktiválása és állapotok letöltése a központilag telepített alkalmazások. Ez a lekérdezés megköveteli, hogy a csomópont neve megegyezik a fürt csomópontjait. A lekérdezés sikertelen lesz, ha a megadott csomópont neve nem mutat minden aktív Service Fabric-csomópontokon a fürtön.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --tartalmazzák--állapota | Egy entitás állapotát tartalmazza. Ha a paraméter értéke HAMIS, vagy nincs megadva, a visszaadott állapotát "Ismeretlen". Ha true értéke esetén a lekérdezés kerül a csomópont-és az állapotszolgáltatás rendszer párhuzamosan előtt egyesítve lesznek az eredmények. A lekérdezés eredményeként drágább, és hosszabb időt vehet igénybe. |
| --max-results | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz annyi eredményeket, előfordulhat, hogy elférjen a visszatérési üzenetben. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-health"></a>alkalmazás állapotának sfctl
A service fabric-alkalmazás állapotának beolvasása.

A service fabric-alkalmazás heath állapotának beolvasása. A válasz Ok, hiba vagy figyelmeztetés állapotát jelzi. Ha az entitás nem található a health Store adatbázisban, hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --deployed-applications-health-state-filter | Lehetővé teszi, hogy a telepített alkalmazások állapotának állapot objektumok szűrése az eredmény abban a állapota alapján alkalmazás állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Az eredmény csak a szűrőnek megfelelő központilag telepített alkalmazások. Minden központilag telepített alkalmazások összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) a HealthState értékét központilag telepített alkalmazások állapotának is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |
| --services-health-state-filter | Lehetővé teszi, hogy a szolgáltatások állapotát állapot objektumok szűrése az eredmény abban a állapota alapján szolgáltatások állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő szolgáltatásokat is megjelennek. Minden szolgáltatás összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd HealthState OK (2) és a figyelmeztető (4) értékkel rendelkező szolgáltatások állapotának visszatér.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-info"></a>Alkalmazásinformáció sfctl
A Service Fabric-alkalmazás információ lekérése.

Vagy a Service Fabric-fürt, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban létrehozott alkalmazással kapcsolatos információkat ad vissza. A válasz tartalmazza a neve, típusa, állapot, paraméterek és egyéb az alkalmazás részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --kizárási-alkalmazás-paraméterek | A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-list"></a>sfctl – Alkalmazáslista
A Service Fabric-fürt létrehozása a megadott szűrőknek megfelelő alkalmazások listájának lekérése.

A létrehozott vagy folyamatban van, a Service Fabric létrehozása a fürt és a megadott szűrőknek megfelelő alkalmazások információ lekérése. A válasz tartalmazza a neve, típusa, állapot, paraméterek és egyéb az alkalmazás részleteit. Az alkalmazások nem férnek el a lapon, ha egy oldalra, valamint a folytatási kód, amely segítségével a következő oldal adja vissza. Szűrők ApplicationTypeName és ApplicationDefinitionKindFilter nem adható meg egyszerre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-definíció-jellegű-szűrő | Használja a szűréshez ApplicationDefinitionKind, amely, akkor ez a Service Fabric-alkalmazás meghatározásához használt eszköz.  <br> -Alapértelmezett - alapértelmezett értéket, amely az "All" kiválasztásával azonos funkciót hajt végre. Az érték 0.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely ApplicationDefinitionKind értékkel. Az érték 65535.  <br> -ServiceFabricApplicationDescription - szűrő, amely megfelel a bemeneti ServiceFabricApplicationDescription ApplicationDefinitionKind értékkel. Az érték 1.  <br> Egyező bemeneti ApplicationDefinitionKind értékű Compose - Compose - szűrő. A 2 érték. |
| ---alkalmazástípus-neve | Az alkalmazás típusnév lekérdezni az alkalmazások szűrésére használatos. Ez az érték nem lehet az alkalmazástípus verziója. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --kizárási-alkalmazás-paraméterek | A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt. |
| --max-results | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz annyi eredményeket, előfordulhat, hogy elférjen a visszatérési üzenetben. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-load"></a>sfctl alkalmazásterhelés
Lekérdezi a Service Fabric-alkalmazás adatainak betöltése.

A létrehozott alkalmazással kapcsolatban, vagy a Service Fabric-fürt, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban terhelés információkat ad vissza. A válasz a nevét, a minimális csomópontok, a csomópontok maximális száma, a csomópontok az alkalmazás jelenleg elfoglaló-e, és alkalmazás terhelési metrika az alkalmazással kapcsolatos információkat tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-manifest"></a>Az alkalmazásjegyzék sfctl
A jegyzékfájl alkalmazástípust leíró lekérése.

A jegyzékfájl alkalmazástípust leíró lekérése. A válasz tartalmazza az alkalmazás jegyzékében XML karakterláncként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| ---alkalmazástípus-verziója [szükséges] | Az alkalmazástípus verziója. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-provision"></a>sfctl alkalmazás kiépítése
Rendelkezések vagy regiszterekben a Service Fabric-alkalmazás írja be a fürtöt a .sfpkg csomag segítségével a külső áruházban, vagy pedig az alkalmazáscsomag az image store.

A Service Fabric alkalmazástípus a fürttel látja el. Erre azért szükség, mielőtt példányosítható bármely új alkalmazások. A telepítési művelet végrehajtható, vagy egy megadott a relativePathInImageStore, vagy a külső .sfpkg URI segítségével alkalmazáscsomagot. Ha – külső-provision be van állítva, ez a parancs lemezképet tároló létesítése fog várt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-package-download-uri | Az elérési utat, amelyről a ".sfpkg" alkalmazáscsomagra alkalmazáscsomag letölthető HTTP vagy HTTPS protokollt használ. <br><br> Kiépítés típusú külső csak tárolja. Az alkalmazáscsomag tárolhatók a GET műveletet letölteni a fájlt biztosító külső áruházban. Támogatott protokollok HTTP és HTTPS, és az elérési út OLVASÁSI hozzáférést kell engedélyeznie. |
| – alkalmazás-típus-build-elérési útja | Kiépítés kind lemezképtárolóhoz csak. Az alkalmazáscsomag a az image store az előzetes feltöltési művelet során megadott relatív elérési útja. |
| ---alkalmazástípus-neve | Kiépítés típusú külső csak tárolja. Az alkalmazás neve megtalálható az alkalmazás jegyzékében alkalmazástípus nevét jelöli. |
| ---alkalmazástípus-verziója | Kiépítés típusú külső csak tárolja. Az alkalmazástípus verziója található az alkalmazás jegyzékében az alkalmazástípus verziója jelöli. |
| --külső-kiépítés | A hely, ahol regisztrált vagy kiépítve a alkalmazáscsomagot. Azt jelzi, hogy a kiépítés külső tárolóhoz korábban feltöltött alkalmazás csomagjában. Az alkalmazáscsomag a bővítmény *.sfpkg végződik. |
| --nem-wait | Azt jelzi-e kiépítés jöjjön létre aszinkron módon. <br><br> Ha igaz értéke esetén a telepítési művelet visszaadja mikor fogadták el a kérelmet a rendszer, és a telepítési művelet által bármely időkorlátja nélkül folytatódik. Az alapértelmezett értéke hamis. A nagy alkalmazáscsomagok esetén ajánlott, TRUE értékre állítja. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-report-health"></a>a jelentés-állapotának sfctl
A Service Fabric-alkalmazás állapotának jelentést küld.

A jelentés a megadott Service Fabric-alkalmazás állapotának. A jelentés az állapotjelentést és amelyen jelentett tulajdonság információt kell tartalmaznia. A jelentést küld a Service Fabric átjáró alkalmazás, amely továbbítja a health Store adatbázisban. A jelentés lehet, hogy fogadja el az átjárót, de elutasította a health Store adatbázisban a felesleges érvényesítése után. A health Store adatbázisban például utasíthatja a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Tekintse meg, hogy a jelentés a health Store adatbázisban alkalmazta, alkalmazás állapotának beolvasása, és ellenőrizze, hogy megjelenik-e a jelentést.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. <br><br> Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – rendszerállapot-tulajdonság [szükséges] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterlánc, és nem a jelentéskészítő rugalmas a jelentés állapot feltétel kategorizálásának rögzített enumerálási tulajdonság. Olyan jelentéskészítői SourceId "LocalWatchdog" a figyelheti például a csomóponton a rendelkezésre álló lemez állapotának, jelentést ezen a csomóponton "AvailableDisk" tulajdonság. Az azonos jelentéskészítői figyelheti a csomópont csatlakozási, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események a megadott csomópont tekintendők. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [szükséges] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Warning", "Error", "Ismeretlen". |
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

## <a name="sfctl-application-type"></a>sfctl alkalmazás típusa
A Service Fabric-fürt pontosan a megadott névnek megfelelő alkalmazástípus listájának beolvasása.

A kiépített típusok vagy a Service Fabric-fürt telepítése folyamatban van olyan információkat ad vissza. Ezekkel az eredményekkel alkalmazástípus, amelynek neve egyezik pontosan a paraméterként megadott egy, és amelyek megfelelnek a megadott lekérdezési paramétereket. Az alkalmazás típusa névnek megfelelő alkalmazástípus összes verziója küld vissza a rendszer, egyes egy alkalmazás típusként visszaadott verzióval. A válasz tartalmazza, a név, verzió, állapota és egyéb adatait az alkalmazás típusa. Egy egy lapozható lekérdezés, ami azt jelenti, ha nem összes alkalmazás típusú oszlopnál lapon egy oldalnyi, eredményeket ad vissza továbbá a folytatási kód, amely segítségével a következő oldal. Például ha 10 alkalmazástípusokat, de a lap csak az első három alkalmazástípus megfelelő, vagy a maximális eredmény értéke 3, majd 3 adja vissza. A többi eredményt eléréséhez beolvasása következő lapjain a következő lekérdezésben a visszaadott folytatási kód használatával. Egy üres folytatási kód eredményül, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| ---alkalmazástípus-verziója | Az alkalmazástípus verziója. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --kizárási-alkalmazás-paraméterek | A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt. |
| --max-results | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz annyi eredményeket, előfordulhat, hogy elférjen a visszatérési üzenetben. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-type-list"></a>sfctl típusa – Alkalmazáslista
A Service Fabric-fürt alkalmazástípus listájának beolvasása.

A kiépített típusok vagy a Service Fabric-fürt telepítése folyamatban van olyan információkat ad vissza. Mindegyik alkalmazástípus verziója egy alkalmazás típusát adja vissza a rendszer. A válasz tartalmazza, a név, verzió, állapota és egyéb adatait az alkalmazás típusa. Egy egy lapozható lekérdezés, ami azt jelenti, ha nem összes alkalmazás típusú oszlopnál lapon egy oldalnyi, eredményeket ad vissza továbbá a folytatási kód, amely segítségével a következő oldal. Például ha 10 alkalmazástípusokat, de a lap csak az első három alkalmazástípus megfelelő, vagy a maximális eredmény értéke 3, majd 3 adja vissza. A többi eredményt eléréséhez beolvasása következő lapjain a következő lekérdezésben a visszaadott folytatási kód használatával. Egy üres folytatási kód eredményül, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típus-definíció-jellegű-szűrő | Használja a szűréshez ApplicationTypeDefinitionKind, ez a módszer a Service Fabric-alkalmazás típusa határozza meg.  <br> -Alapértelmezett - alapértelmezett értéket, amely az "All" kiválasztásával azonos funkciót hajt végre. Az érték 0.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely ApplicationTypeDefinitionKind értékkel. Az érték 65535.  <br> -ServiceFabricApplicationPackage - szűrő, amely megfelel a bemeneti ServiceFabricApplicationPackage ApplicationTypeDefinitionKind értékkel. Az érték 1.  <br> Egyező bemeneti ApplicationTypeDefinitionKind értékű Compose - Compose - szűrő. A 2 érték. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --kizárási-alkalmazás-paraméterek | A jelzőt, amely megadja, hogy alkalmazás paraméterei nem kerülnek bele az eredményt. |
| --max-results | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz annyi eredményeket, előfordulhat, hogy elférjen a visszatérési üzenetben. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-unprovision"></a>sfctl alkalmazás unprovision
Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése.

Eltávolítja, vagy a fürtből a Service Fabric alkalmazástípus regisztrációjának törlése. Ez a művelet csak akkor hajtható végre, ha az alkalmazás összes példányát az alkalmazástípus törölve lett. Az alkalmazástípus regisztrációjának törlése, ha nincs új alkalmazás-példány az adott alkalmazás típusa hozható létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| ---alkalmazástípus-verziója [szükséges] | Az alkalmazás jegyzékében meghatározott az alkalmazástípus verziója. |
| --async-parameter | A jelző-e leépíteni aszinkron módon történjen-e. Ha igaz értéke esetén a unprovision művelet értéket ad eredményül, ha a rendszer elfogadta a kérést, és a unprovision továbbra is fennáll semmilyen időkorlátja nélkül. Az alapértelmezett értéke hamis. Azt javasoljuk azonban nagy alkalmazáscsomagok kiépített igaz értékre állítaná. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-upgrade"></a>Az alkalmazásfrissítés sfctl
Elindul az alkalmazás a Service Fabric-fürt frissítése.

Ellenőrzi a megadott alkalmazás frissítési paramétereit, és elindítja a az alkalmazás frissítését, ha a paraméterek érvényesek. Vegye figyelembe, hogy a frissítés leírása lecseréli a meglévő alkalmazás leírása. Ez azt jelenti, hogy ha a paraméter nincs megadva, a meglévő paraméterek alkalmazásokat felülíródik üres paraméterek listáját tartalmazó. Ez azt eredményezi, amíg az alkalmazás az alapértelmezett érték az alkalmazásjegyzék a paraméterek használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. <br><br> Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – [szükséges] alkalmazás-verziója | Cél alkalmazás verziója. |
| – [szükséges] Paraméterek | Egy JSON-kódolású alkalmazás paraméter a beállítás felülbírálja a alkalmazás verzióra alkalmazandó. |
| --default-service-health-policy | JSON kódolású állapotellenőrzése típusú egy szolgáltatás alapértelmezés szerint használt állapotházirend megadását. |
| --failure-action | Milyen műveletet kell végrehajtani, amikor egy figyelt frissítés figyelési házirend vagy állapotfigyelő házirend megsértésének észlel. |
| – force-újraindítás | Kényszerített újraindítása a folyamatok még akkor is, a frissítés során a kód verziója nem változott. |
| --health-check-retry-timeout | Mennyi ideig, majd ismételje meg állapotfigyelő értékelések, amikor az alkalmazás vagy a fürt állapota nem megfelelő a sikertelen művelet előtt végrehajtása. Ezredmásodpercben.  Alapértelmezett\: PT0H10M0S. |
| --health-check-stable-duration | Ennyi idő alatt, hogy az alkalmazás vagy a fürt kell maradnia kifogástalan előtt a frissítés előrehalad az a következő frissítési tartományra. Ezredmásodpercben.  Alapértelmezett\: PT0H2M0S. |
| --health-check-wait-duration | A frissítési tartományok házirendek alkalmazása előtt befejezése után várjon időtartam. Ezredmásodpercben.  Alapértelmezett\: 0. |
| --max-unhealthy-apps | Az engedélyezett maximális százalékos aránya a nem megfelelő a telepített alkalmazások. Egy 0 és 100 közötti számot jelöli. |
| --mód | A működés közbeni frissítés során állapotának figyeléséhez használt módot.  Alapértelmezett\: UnmonitoredAuto. |
| --replica-set-check-timeout | A maximális időt blokkolhatja a frissítési tartományok feldolgozása, illetve a rendelkezésre állási az adatvesztés elkerülése érdekében, amikor nincsenek váratlan problémák. Mérése másodpercben történik. |
| – a házirend Állapotfigyelő szolgáltatás | JSON a service type állapotházirend szolgáltatás típusának neve / térkép kódolva. A térkép üres azonban alapértelmezés szerint. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| --frissítés-tartományi-időtúllépés | Mennyi ideig mindegyik frissítési tartományon van FailureAction végrehajtása előtt. Ezredmásodpercben.  Alapértelmezett\: P10675199DT02H48M05.4775807S. |
| --frissítés – időtúllépés | Ennyi idő alatt a összesített frissítéséből FailureAction végrehajtása előtt. Ezredmásodpercben.  Alapértelmezett\: P10675199DT02H48M05.4775807S. |
| --warning-as-error | Állapotfigyelő értékelési figyelmeztetések a azonos kiegészített hibaként. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl alkalmazás frissítés-folytatása
Az alkalmazás a Service Fabric-fürt frissítése folytatása.

Folytatása egy nem figyelt manuális Service Fabric-alkalmazás frissítése. Service Fabric egyszerre több frissítési tartományt frissít. A nem figyelt manuális frissítésre a Service Fabric egy frissítési tartomány befejeződése után arra vár, hogy a folytatás előtt az API hívása a következő frissítési tartományra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --frissítés-tartománynév [szükséges] | A frissítés folytatásához frissítési tartomány neve. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-upgrade-rollback"></a>frissítés-visszaállítási sfctl alkalmazás
Elindítja a Service Fabric-fürt visszaállítása az alkalmazás jelenleg folyamatban lévő frissítése folyamatban.

Visszaállítása a jelenlegi alkalmazás elindul az előző verzióra frissíteni. Ez az API csak visszaállítása a jelenlegi folyamatban lévő frissítés, amely előre folyamatban van az új verzió használható. Ha az alkalmazás nem jelenleg frissítés alatt áll StartApplicationUpgrade API segítségével kívánt verziójával, beleértve a visszaállítása egy korábbi verzióját frissíti.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-upgrade-status"></a>sfctl alkalmazás frissítés-állapota
Lekérdezi a részletek a legújabb frissítés végre ezt az alkalmazást.

A legfrissebb alkalmazás frissítési részletek támogatási hibakeresési alkalmazás ügynökállapottal kapcsolatos hibákkal együtt állapotára vonatkozó adatokat ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-application-upload"></a>sfctl alkalmazás feltöltése
A Service Fabric-alkalmazás csomag másolása az image store.

A fájl feltöltési folyamatáról opcionálisan megjeleníteni a csomagban. Töltse fel folyamatban van a rendszer elküldi `stderr`.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] elérési útja | Alkalmazáscsomag helyi elérési útja. |
| --lemezképtárolóba-karakterlánc | Cél lemezképet tárolja az alkalmazáscsomag feltöltéséhez.  Alapértelmezett\: háló\:Lemezképtárolóba. |
| --megjelenítése van folyamatban | A nagyméretű csomagok fájl feltöltési folyamatáról megjelenítése. |

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
