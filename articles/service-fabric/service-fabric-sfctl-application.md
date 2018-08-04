---
title: Az Azure Service Fabric parancssori felület - sfctl-alkalmazás |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-alkalmazás parancsokat.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 40ec204f105b32c8b7d9e2dda6f6f3c3023b2d44
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495458"
---
# <a name="sfctl-application"></a>sfctl-alkalmazás
Létrehozása, törlése és kezelheti az alkalmazások és alkalmazás-típusok.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Service Fabric-alkalmazás a megadott leírás használatával hoz létre. |
| delete | Törli a már meglévő Service Fabric-alkalmazás. |
| üzembe helyezett | A Service Fabric-csomópont a központi telepítésű alkalmazás adatainak beolvasása. |
| üzembe helyezett állapot | A health egy Service Fabric-csomóponton üzembe helyezett alkalmazás adatainak beolvasása. |
| üzembe helyezett-list | A Service Fabric-csomóponton üzembe helyezett alkalmazások listájának beolvasása. |
| állapot | A service fabric-alkalmazás állapotának beolvasása. |
| információ | Service Fabric-alkalmazás adatainak beolvasása. |
| lista | A Service Fabric-fürt létrehozása, amely a megadott szűrőknek megfelelő alkalmazások listájának beolvasása. |
| betöltés | Lekérdezi az információ a Service Fabric-alkalmazás betöltése. |
| Manifest | A jegyzékfájl alkalmazástípust leíró beolvasása. |
| üzembe helyezése | Rendelkezések, illetve regisztrál egy Service Fabric-alkalmazás írja be a fürthöz a .sfpkg csomag használata a külső tárolójában, vagy pedig az alkalmazáscsomag a lemezképtároló. |
| report-health | A Service Fabric-alkalmazás állapotának jelentést küld. |
| type | A megadott névnek megfelelő Service Fabric-fürt alkalmazástípusok listájának beolvasása. |
| típus-list | A Service Fabric-fürt alkalmazástípusok listájának beolvasása. |
| unprovision | Eltávolítja vagy a regisztrációjának törlése a Service Fabric-alkalmazástípus a fürtből. |
| frissítés | Elindul az alkalmazás a Service Fabric-fürt frissítéséhez. |
| frissítés-folytatása | Folytatja a Service Fabric-fürt egy alkalmazás frissítéséhez. |
| frissítés-visszaállítás | Elindítja a visszaállítása a kérelmet jelenleg a folyamatban lévő frissítése a Service Fabric-fürtben. |
| frissítés – állapot | Részletek beolvasása végrehajtani ezt az alkalmazást a legújabb frissítést. |
| feltöltés | Másolja a lemezképtároló egy Service Fabric-alkalmazáscsomagot. |

## <a name="sfctl-application-create"></a>sfctl-alkalmazás létrehozása
Service Fabric-alkalmazás a megadott leírás használatával hoz létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| [kötelező] – alkalmazás-neve | Az alkalmazás neve például a "fabric\:" URI-séma. |
| [kötelező] – alkalmazás-típusa | Az alkalmazástípus neve található az alkalmazásjegyzékben. |
| [kötelező] – alkalmazás-verzió | Az alkalmazás típusát, ahogyan az az alkalmazásjegyzék verziója. |
| --max-node-count | Ahol a Service Fabric foglal le a kapacitást az alkalmazáshoz tartozó csomópontok maximális száma. Vegye figyelembe, hogy ez nem jelenti azt, hogy a szolgáltatások, az alkalmazás az összes, ezen csomópontok kell elhelyezni. |
| --metrikák | Egy JSON kódolású alkalmazás kapacitásának metrika leírásai listájának. Metrika számít, ha egy nevet, az egyes csomópontok az alkalmazás meglévő kapacitások vannak beállítva. |
| – perc – csomópont-száma | Ahol a Service Fabric foglal le a kapacitást az alkalmazáshoz tartozó csomópontok minimális száma. Vegye figyelembe, hogy ez nem jelenti azt, hogy a szolgáltatások, az alkalmazás az összes, ezen csomópontok kell elhelyezni. |
| – Paraméterek | Egy JSON kódolású alkalmazás paraméter a beállítás felülbírálja a alkalmazni az alkalmazás létrehozásakor. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-delete"></a>sfctl-alkalmazás törlése
Törli a már meglévő Service Fabric-alkalmazás.

Egy alkalmazás akkor törlése előtt kell létrehozni. Alkalmazás törlése eltávolítja az alkalmazás részét képező összes szolgáltatást. Alapértelmezés szerint a Service Fabric megpróbálja zárja be a replikák szolgáltatás normális módon, és ezután törölje a szolgáltatást. Azonban a szolgáltatás nem tudta szabályosan bezárása a replikát, ha a törlési művelet hosszú időt vehet igénybe vagy elakad. A választható ForceRemove jelző használatával a normális Bezárás feladatütemezési kihagyhatja, és az alkalmazás és az összes hozzá tartozó szolgáltatások kényszerített törlése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --force-remove | Távolítsa el a Service Fabric-alkalmazás vagy szolgáltatás kellett zárnia a szabályos leállítást feladatütemezési áthaladás nélkül. Ez a paraméter használható kényszerítve törli egy alkalmazás vagy szolgáltatás, mely törlés van időtúllépése miatt problémák egy részét a szolgáltatás-kódot, amely megakadályozza a sikeres-e bezárása replikára. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-deployed"></a>sfctl-alkalmazás telepítve
A Service Fabric-csomópont a központi telepítésű alkalmazás adatainak beolvasása.

Ez a lekérdezés eredménye rendszer alkalmazással kapcsolatos adatok, ha az alkalmazás-azonosító system alkalmazás. Eredmények tartalmazzák a aktív, aktiválása és állapotok letöltése a központilag telepített alkalmazások is. Ez a lekérdezés szükséges, hogy a csomópont neve megfelel-e a fürt csomópontjait. A lekérdezés sikertelen lesz, ha a megadott csomópont neve nem mutat bármely aktív Service Fabric csomópontjaival a fürtön.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --tartalmazzák-állapot – állapot | Egy entitás állapotát tartalmazza. Ha ez a paraméter FALSE (hamis), vagy nincs megadva, a visszaadott állapot "Ismeretlen". Ha igaz értékű, a lekérdezés kerül a csomópont és a rendszer az állapotszolgáltatás párhuzamosan mielőtt vannak az eredményeket. Ennek eredményeképpen a lekérdezés drágább, és hosszabb időt is igénybe vehet. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-deployed-health"></a>üzembe helyezett alkalmazás sfctl-health
A health egy Service Fabric-csomóponton üzembe helyezett alkalmazás adatainak beolvasása.

A health egy Service Fabric-csomóponton üzembe helyezett alkalmazás adatainak beolvasása. EventsHealthStateFilter használatával állapotesemény – objektumok jelentett állapota alapján az üzembe helyezett alkalmazást gyűjteményének igény szerint szűrni. DeployedServicePackagesHealthStateFilter használatával opcionális szűrés DeployedServicePackageHealth gyermekobjektumok állapota alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --Deployed-Service-Packages-Health-State-Filter | Lehetővé teszi a szűrés az üzemelő szolgáltatás csomag egészségügyi állapot objektumok üzembe helyezett alkalmazás állapotának lekérdezés eredményét adja vissza azok állapota alapján. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak telepített szolgáltatáshoz a szűrőnek megfelelő csomagok adja vissza. Az összes telepített szervizcsomagok segítségével kiértékelheti az üzembe helyezett alkalmazás összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, a bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd szolgáltatáscsomagok állapota OK (2), és figyelmeztetés (4) HealthState értékét a rendszer adja vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --események-állapot – állapot-szűrő | A gyűjtemény állapotesemény – a visszaadott objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény segítségével kiértékelése összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, a bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd az OK (2), és figyelmeztetés (4) HealthState értékét az események vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi-e az egészségügyi statisztikák a rendszer visszalépteti a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a gyermekek száma entitások állapota Ok, figyelmeztetés és hiba. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-deployed-list"></a>üzembe helyezett alkalmazás sfctl-list
A Service Fabric-csomóponton üzembe helyezett alkalmazások listájának beolvasása.

A Service Fabric-csomóponton üzembe helyezett alkalmazások listájának beolvasása. Az eredmények nem tartalmazzák telepített rendszer alkalmazásokkal kapcsolatos információkat, ha explicit módon kellettek tartozó azonosító. Eredmények tartalmazzák a aktív, aktiválása és állapotok letöltése a központilag telepített alkalmazások is. Ez a lekérdezés szükséges, hogy a csomópont neve megfelel-e a fürt csomópontjait. A lekérdezés sikertelen lesz, ha a megadott csomópont neve nem mutat bármely aktív Service Fabric csomópontjaival a fürtön.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --tartalmazzák-állapot – állapot | Egy entitás állapotát tartalmazza. Ha ez a paraméter FALSE (hamis), vagy nincs megadva, a visszaadott állapot "Ismeretlen". Ha igaz értékű, a lekérdezés kerül a csomópont és a rendszer az állapotszolgáltatás párhuzamosan mielőtt vannak az eredményeket. Ennek eredményeképpen a lekérdezés drágább, és hosszabb időt is igénybe vehet. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-health"></a>sfctl-alkalmazás állapota
A service fabric-alkalmazás állapotának beolvasása.

A service fabric-alkalmazás az állapottesztelés állapotát adja vissza. A válasz Ok, hiba vagy figyelmeztetés állapot jelentések. Ha az entitás nem található a health Store adatbázisban, hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --deployed-applications-health-state-filter | Lehetővé teszi, hogy a telepített alkalmazások állapotának állapot objektumok szűrése azok állapota alapján alkalmazás állapotának lekérdezés eredményét adja vissza. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő telepített alkalmazások lesz visszaadva. Összesített állapota kiértékelheti, hogy minden központilag telepített alkalmazások használhatók. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd OK (2), és figyelmeztetés (4) HealthState értékét a központilag telepített alkalmazások állapotát vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --események-állapot – állapot-szűrő | A gyűjtemény állapotesemény – a visszaadott objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény segítségével kiértékelése összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, a bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd az OK (2), és figyelmeztetés (4) HealthState értékét az események vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi-e az egészségügyi statisztikák a rendszer visszalépteti a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a gyermekek száma entitások állapota Ok, figyelmeztetés és hiba. |
| --services-health-state-filter | Lehetővé teszi, hogy a szolgáltatások állapotának állapot objektumok szűrése szolgáltatások állapotának lekérdezéséhez azok állapota alapján eredményét adja vissza. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő szolgáltatásokat adja vissza. Minden szolgáltatás segítségével kiértékelheti az összesített állapota. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd HealthState értékét OK (2), és figyelmeztetés (4) a szolgáltatások állapotát lesz visszaadva.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-info"></a>sfctl-alkalmazás adatai
Service Fabric-alkalmazás adatainak beolvasása.

Visszaadja az információkat, az alkalmazás, amely lett létrehozva, vagy a Service Fabric-fürthöz, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban. A válasz tartalmazza a nevét, típusát, állapot, paraméterek, és az alkalmazással kapcsolatos egyéb részleteket.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --alkalmazásparamétereket kizárása | A jelzőt, amely meghatározza, hogy alkalmazásparamétereket ki lesznek zárva az eredményt. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-list"></a>sfctl-alkalmazások listája
A Service Fabric-fürt létrehozása, amely a megadott szűrőknek megfelelő alkalmazások listájának beolvasása.

A létrehozott, vagy éppen létrehozás alatt álló a Service Fabric-fürt és a megadott szűrőknek megfelelő alkalmazások adatainak beolvasása. A válasz tartalmazza a nevét, típusát, állapot, paraméterek, és az alkalmazással kapcsolatos egyéb részleteket. Ha az alkalmazások nem férnek el a lapon, valamint egy folytatási kód, amely a következő lap beolvasásához használható egy oldalnyi találatot ad vissza. Szűrők ApplicationTypeName és az ApplicationDefinitionKindFilter nem adható meg egyszerre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-definition-jellegű-szűrő | Szűrés ApplicationDefinitionKind, azaz a mechanizmus segítségével határozhatók meg a Service Fabric-alkalmazás használja.  <br> -Alapértelmezett – alapértelmezett érték, amely a "Minden" lehetőség azonos funkciót hajt végre. Az érték 0.  <br> -Az összes - szűrő, amely megfelel a bemeneti ApplicationDefinitionKind értéket. Az érték 65535.  <br> -ServiceFabricApplicationDescription - szűrő, amely megfelel a bemeneti ServiceFabricApplicationDescription ApplicationDefinitionKind értékkel. Az érték az 1.  <br> -Compose - szűrő, amely megfelel a bemeneti összeállítás ApplicationDefinitionKind értékkel. A 2 érték. |
| – alkalmazás típusát | Az alkalmazástípus neve használatával szűrhetők az alkalmazások való lekérdezéséhez. Ez az érték nem tartalmazhat az alkalmazástípus-verzió. |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --alkalmazásparamétereket kizárása | A jelzőt, amely meghatározza, hogy alkalmazásparamétereket ki lesznek zárva az eredményt. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-load"></a>sfctl-alkalmazás betöltése
Lekérdezi az információ a Service Fabric-alkalmazás betöltése.

Az alkalmazás, amely lett létrehozva, vagy a Service Fabric-fürthöz, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban a terhelés információkat adja vissza. A válasz a nevét, a csomópontok minimális száma, a csomópontok maximális száma, a csomópontok az alkalmazás jelenleg kötnek-e, és application load metrika az alkalmazással kapcsolatos információkat tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-manifest"></a>sfctl-alkalmazás jegyzékfájlja
A jegyzékfájl alkalmazástípust leíró beolvasása.

A válasz tartalmazza az alkalmazás jegyzékfájlja XML karakterláncként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| – [kötelező] alkalmazás-típus-verziója | Az alkalmazástípus verziója. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-provision"></a>sfctl-alkalmazás üzembe helyezése
Rendelkezések, illetve regisztrál egy Service Fabric-alkalmazás írja be a fürthöz a .sfpkg csomag használata a külső tárolójában, vagy pedig az alkalmazáscsomag a lemezképtároló.

Látja el egy Service Fabric-alkalmazás típusa a fürthöz. Ez azért szükséges, mielőtt új alkalmazásokat lehet létrehozni. Az üzembe helyezési művelete is elvégezhető, akár az alkalmazáscsomagot a megadott a relativePathInImageStore, vagy a külső .sfpkg URI használatával. Kivéve, ha – a kiépítés külső van beállítva, ez a parancs lemezképet tároló üzembe helyezése fog várható.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-package-download-uri | Az elérési utat az ".sfpkg" alkalmazáscsomag, ahonnan a az alkalmazáscsomag segítségével lehet letölteni a HTTP vagy HTTPS-protokollt. <br><br> Külső kiépítése fájltípusnak csak tárolja. Az alkalmazáscsomag is tárolhatók egy külső tároló által biztosított művelet a fájl letöltéséhez. Támogatott protokollok HTTP a protokolu HTTPS, és az elérési út engedélyeznie kell az OLVASÁSI hozzáférést. |
| – alkalmazás-típus-build-elérési útja | Kiépítése altípus lemezképet tároló. Az alkalmazáscsomag a előzetes feltöltési művelet során megadott rendszerkép áruházbeli relatív elérési útja. |
| – alkalmazás típusát | Külső kiépítése fájltípusnak csak tárolja. Az alkalmazástípus neve az alkalmazástípus található az alkalmazásjegyzékben nevét jelöli. |
| --Alkalmazásverzió-típus | Külső kiépítése fájltípusnak csak tárolja. Az alkalmazástípus-verzió található az alkalmazásjegyzékben az alkalmazástípus verziója jelöli. |
| --külső a kiépítés | Ha regisztrált vagy kiépítve a virtuálisalkalmazás-csomag helyét. Azt jelzi, hogy a kiépítés alkalmazáscsomagot jelent, amelyet korábban feltöltött egy külső tároló számára. Az alkalmazáscsomag a bővítmény *.sfpkg végződik. |
| --no-wait | Azt jelzi-e kiépítés történjen aszinkron módon történik. <br><br> Ha értéke igaz, az üzembe helyezési művelete adja vissza. Ha a kérelmet elfogadta a rendszer, és az üzembe helyezési művelete által bármely időkorlátja nélkül folytatódik. Az alapértelmezett értéke FALSE (hamis). Nagy alkalmazáscsomagok esetén javasoljuk, hogy TRUE értékre állítja. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-report-health"></a>sfctl alkalmazás állapotjelentés
A Service Fabric-alkalmazás állapotának jelentést küld.

A jelentés a megadott Service Fabric-alkalmazás állapotát. A jelentésnek tartalmaznia kell a forrás az egészségügyi jelentés és, amelyen jelentett tulajdonság vonatkozó információk. A jelentést küld egy Service Fabric gateway alkalmazás, amely továbbítja a health Store adatbázisban. A jelentés előfordulhat, hogy fogadja el az átjárót, azonban elutasította a health Store adatbázisban a további ellenőrzést. A health Store adatbázisban például elutasíthatják a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Szeretné-e a jelentés a a health Store adatbázisban alkalmaztak, alkalmazás állapotának lekérése, és ellenőrizze, hogy megjelenik-e a jelentés.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. <br><br> Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", lesz az identitása "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – rendszerállapot-tulajdonság [kötelező] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterláncot és a nem rögzített enumerálása, hogy az Eszközállapot-feltételt, amely elindítja a jelentés kategorizálása riporter rugalmasan tulajdonság. Például egy riporter a SourceId "LocalWatchdog" figyelheti az állapotot, a rendelkezésre álló lemez egy csomóponton, ezen a csomóponton, jelentést "AvailableDisk" tulajdonság. A ugyanolyan jelentéskészítői figyelheti a csomópont-kapcsolatban –, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonságot. A health Store adatbázisban ezek a jelentések az adott csomópont számára külön állapotesemények kell kezelni. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [kötelező] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Figyelmeztetés", "Error", "Ismeretlen". |
| – [kötelező] adatforrás-azonosítója | Az adatforrás neve azonosítja az ügyfél, a figyelő vagy a system összetevője, amely az egészségügyi információk jönnek létre. |
| – Leírás | Az egészségügyi információk leírását. <br><br> Azt jelöli, szabad szöveges adja hozzá a jelentés az emberi olvasható információk segítségével. A leírás karakterlánc maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor automatikusan csonkolva lesz. Csonkolva, amikor az utolsó karakter, a leírás tartalmaz egy "[Truncated]" jelölő, és teljes karaktersorozat 4096 karakternél. Jelenlétét, a jelölő azt jelzi, hogy a felhasználók számára, hogy a csonkolási történt. Vegye figyelembe, hogy csonkolva, a leírásnak legalább 4096 karakternél, az eredeti karakterláncot. |
| – azonnali | Azt a jelzőt, amely azt jelzi, hogy a jelentés azonnal kell küldeni. <br><br> Egy jelentés küld egy Service Fabric gateway alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz, a jelentés azonnal címről érkezik a health Store adatbázisban, függetlenül a fabric-ügyfélbeállításokat a http-átjáró alkalmazások által használt HTTP-átjáró. Ez akkor hasznos, a kritikus fontosságú jelentések, amelyek a lehető leghamarabb kell küldeni. Attól függően, ütemezését és egyéb feltételek a jelentés elküldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Ha Immediate hamis értékre van állítva, a jelentés alapján lesz elküldve az egészségügyi ügyfélbeállításokat a HTTP-átjáró. Ezért azt fogja kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az az ajánlott beállítás, mivel így az egészségügyi ügyfél állapotfigyelő jelentési üzenetek a health Store adatbázisban, valamint az egészségügyi jelentés feldolgozása optimalizálása érdekében. Alapértelmezés szerint a rendszer a jelentések nem küldése azonnal. |
| --eltávolítása akkor, ha lejárt | Érték, amely azt jelzi, hogy a jelentés törlődik a health store adatbázisból, a lejárat után. <br><br> Ha igaz értékű, a jelentés távolítja el a health Store adatbázisban után jár le. Ha az értéke HAMIS, a jelentés egy hibát, ha lejárt számít. Ez a tulajdonság értéke alapértelmezés szerint False (hamis). Amikor az ügyfelek rendszeresen készít jelentést, akkor állítsa be RemoveWhenExpired false (alapértelmezett). Ezzel a módszerrel a riporter veti fel (pl. holtpont), és nem készíthető jelentés, az entitás értékeli ki a hiba, ha lejár az állapotjelentés. Az entitás hibaállapot állapottal megőrzendő tartalomként jelöli. |
| --sorszám | A numerikus karakterláncként egészségügyi jelentés sorszáma. <br><br> A jelentés sorszám használják a health Store adatbázisban elavult jelentések észleléséhez. Ha nincs megadva, egy megfelelő sorszám health-ügyfél által automatikusan létrehozott jelentések hozzáadásakor. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| – élettartam | Az időtartam, amelynek a jelentés érvénytelen. Ez a mező ISO8601 formátumot használ a időtartam megadása. <br><br> Amikor az ügyfelek rendszeresen készít jelentést, élettartam-nál nagyobb gyakorisággal kell küldenek jelentéseket. Az ügyfelek jelentés az átmenet, ha azok time to live végtelen, állíthatja be. Élettartam lejár, az egészségügyi adatokat tartalmazó állapotesemény esetén vagy eltávolítja a health Store adatbázisban, ha RemoveWhenExpired igaz értékre, vagy értékelhető a hiba, ha RemoveWhenExpired hamis. Ha nem a végtelen érték az alapértelmezett élettartam adott, ideje. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-type"></a>sfctl-alkalmazás típusa
A megadott névnek megfelelő Service Fabric-fürt alkalmazástípusok listájának beolvasása.

Az üzembe helyezett alkalmazás különböző típusainak, vagy éppen jön létre a Service Fabric-fürt információkat adja vissza. Alkalmazástípusok ezekkel az eredményekkel, amelynek a neve pontosan a egyeznie a paraméterként megadott, és amelyek megfelelnek a megadott lekérdezési paraméterek. Az alkalmazástípus az alkalmazástípus neve megfelelő összes verziója ad vissza, az egyes egy alkalmazás típusaként visszaadott verzióval. A válasz tartalmazza a név, verzió, állapot és egyéb részleteit az alkalmazás típusát. Ez a lapozható lekérdezés, ami azt jelenti, ha nem az összes alkalmazástípusok elfér egy oldalon egy oldalnyi találatot ad vissza és a egy folytatási kód, amely a következő lap beolvasásához használható. Például ha 10 alkalmazás típusa van, de a lap csak az első három alkalmazástípus esetében megfelelő, vagy az eredmények maximális értéke 3, majd három adja vissza. A többi az eredmények eléréséhez beolvasni a következő lapjain a következő lekérdezésben visszaadott folytatási tokent használatával. Egy üres folytatási tokent ad vissza, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| --Alkalmazásverzió-típus | Az alkalmazástípus verziója. |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --alkalmazásparamétereket kizárása | A jelzőt, amely meghatározza, hogy alkalmazásparamétereket ki lesznek zárva az eredményt. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-type-list"></a>sfctl típusa – Alkalmazáslista
A Service Fabric-fürt alkalmazástípusok listájának beolvasása.

Az üzembe helyezett alkalmazás különböző típusainak, vagy éppen jön létre a Service Fabric-fürt információkat adja vissza. Minden alkalmazástípus verziója adja vissza egy alkalmazás típusaként. A válasz tartalmazza a név, verzió, állapot és egyéb részleteit az alkalmazás típusát. Ez a lapozható lekérdezés, ami azt jelenti, ha nem az összes alkalmazástípusok elfér egy oldalon egy oldalnyi találatot ad vissza és a egy folytatási kód, amely a következő lap beolvasásához használható. Például ha 10 alkalmazás típusa van, de a lap csak az első három alkalmazástípus esetében megfelelő, vagy az eredmények maximális értéke 3, majd három adja vissza. A többi az eredmények eléréséhez beolvasni a következő lapjain a következő lekérdezésben visszaadott folytatási tokent használatával. Egy üres folytatási tokent ad vissza, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típus-definition-jellegű-szűrő | Használt a ApplicationTypeDefinitionKind, amely a mechanizmus, amely egy Service Fabric-alkalmazás típusa határozza meg.  <br> -Alapértelmezett – alapértelmezett érték, amely a "Minden" lehetőség azonos funkciót hajt végre. Az érték 0.  <br> -Az összes - szűrő, amely megfelel a bemeneti ApplicationTypeDefinitionKind értéket. Az érték 65535.  <br> -ServiceFabricApplicationPackage - szűrő, amely megfelel a bemeneti ServiceFabricApplicationPackage ApplicationTypeDefinitionKind értékkel. Az érték az 1.  <br> -Compose - szűrő, amely megfelel a bemeneti összeállítás ApplicationTypeDefinitionKind értékkel. A 2 érték. |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --alkalmazásparamétereket kizárása | A jelzőt, amely meghatározza, hogy alkalmazásparamétereket ki lesznek zárva az eredményt. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Eltávolítja vagy a regisztrációjának törlése a Service Fabric-alkalmazástípus a fürtből.

Ez a művelet csak akkor hajtható végre, ha az alkalmazás összes példányát az alkalmazástípus törölve lett. Az alkalmazástípus regisztrációját, ha nem új alkalmazáspéldányok Pro tento typ aplikace adott hozható létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| – [kötelező] alkalmazás-típus-verziója | Az alkalmazás típusát, ahogyan az az alkalmazásjegyzék verziója. |
| --async-parameter | A jelzőt, amely megadja e unprovision aszinkron módon történjen. Ha értéke igaz, a unprovision művelet értéket ad eredményül, ha a rendszer elfogadta a kérést, és a unprovision művelet folyamatosan bármely időtúllépési korlát nélkül. Az alapértelmezett értéke FALSE (hamis). Azt javasoljuk azonban, a nagy alkalmazáscsomagok kiépített true értékre. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-upgrade"></a>sfctl-alkalmazás frissítése
Elindul az alkalmazás a Service Fabric-fürt frissítéséhez.

A megadott alkalmazásfrissítési paraméterek érvényesíti, és elindítja az alkalmazás frissítését, ha a paraméterek érvényesek. Vegye figyelembe, hogy a frissítés leírása lecseréli a meglévő alkalmazás leírása. Ez azt jelenti, hogy ha a paraméter nincs megadva, meglévő paraméterei az alkalmazásokat a rendszer felülírja az üres paraméterek listája. Ez az alkalmazás használatával az alapértelmezett érték a paraméterek alkalmazásjegyzékből való eredményez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. <br><br> Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", lesz az identitása "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – [kötelező] alkalmazás verziója | Cél alkalmazás verziója. |
| – [kötelező] Paraméterek | Parametr aplikace JSON-kódolású listáját felülbírálja a alkalmazni, ha az alkalmazás frissítését. |
| --default-service-health-policy | JSON kódolású alapértelmezés szerint egy bizonyos szolgáltatástípusként állapotának értékeléséhez használt állapotházirend meghatározását. |
| --failure-action | A művelet végrehajtására, amikor egy figyelt frissítés figyelési házirend vagy egészségügyi szabálymegsértéseknek észlel. |
| --kényszerített újraindítás | Kényszerítve indítsa újra a folyamatok frissítéskor még akkor is, amikor a kód verziója nem változott. |
| --health-check-retry-timeout | Idő az egészségügyi értékelések újra, amikor az alkalmazás vagy a fürt állapota nem kifogástalan, mielőtt a sikertelen műveletet hajtja végre. Milliszekundumban mérik.  Alapértelmezett\: PT0H10M0S. |
| --health-check-stable-duration | Mennyi ideig, hogy az alkalmazás vagy a fürt kell megfelelő állapotú marad a következő frissítési tartományra abból a frissítés előtt. Milliszekundumban mérik.  Alapértelmezett\: PT0H2M0S. |
| --health-check-wait-duration | A házirendek alkalmazása előtt a frissítési tartomány befejezése után várakozási idő mennyisége. Milliszekundumban mérik.  Alapértelmezett\: 0. |
| --max-unhealthy-apps | A maximálisan megengedett sérült telepített alkalmazások aránya. Egy 0 és 100 közötti számot jelöli. |
| --mód | Az állapotmonitorozás a működés közbeni frissítés során használt mód.  Alapértelmezett\: UnmonitoredAuto. |
| --replica-set-check-timeout | A maximális mennyisége, és letiltja a frissítési tartomány feldolgozása és váratlan problémák esetén a rendelkezésre állás az adatvesztés elkerülése érdekében. Idejének mérése másodpercben. |
| – a házirend Állapotfigyelő szolgáltatás | JSON kódolású térkép rajzolása szolgáltatás típus állapotházirend / szolgáltatás neve. A térkép az üres lehet alapértelmezett. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| --frissítés-tartomány-időkorlátja | Mennyi ideig mindegyik frissítési tartományon van befejezését, mielőtt FailureAction hajtja végre. Milliszekundumban mérik.  Alapértelmezett\: P10675199DT02H48M05.4775807S. |
| --frissítés – időtúllépés | Mennyi ideig a teljes frissítés rendelkezik befejezését, mielőtt FailureAction hajtja végre. Milliszekundumban mérik.  Alapértelmezett\: P10675199DT02H48M05.4775807S. |
| --warning-as-error | Egészségügyi értékelési figyelmeztetést az azonos súlyossági hibaként. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl alkalmazás frissítés-folytatása
Folytatja a Service Fabric-fürt egy alkalmazás frissítéséhez.

Folytatja egy nem figyelt manuális Service Fabric-alkalmazás frissítése. A Service Fabric egyszerre több frissítési tartományt frissít. Nem figyelt manuális frissítéseket a frissítési tartomány, a Service Fabric végeztével arra vár, hogy az API-t, mielőtt folytatná a következő frissítési tartományra irányuló hívás.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| – [kötelező] frissítés-domain-name | A frissítési tartomány, amelyben a frissítés folytatásához neve. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl alkalmazás frissítés-visszaállítás
Elindítja a visszaállítása a kérelmet jelenleg a folyamatban lévő frissítése a Service Fabric-fürtben.

Elindul az adott alkalmazás visszaállítása a korábbi verzióra frissíteni. Az API-t csak a jelenlegi folyamatban lévő frissítés, amely új verzióra előre közbeni visszavonásához használható. Ha az alkalmazás nem jelenleg frissítés alatt áll a StartApplicationUpgrade API használatával frissít a kívánt verziót, beleértve a korábbi verzióinak visszaállítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-upgrade-status"></a>sfctl-alkalmazás frissítés-állapota
Részletek beolvasása végrehajtani ezt az alkalmazást a legújabb frissítést.

A legfrissebb alkalmazás frissítését is tartalmaz, ezzel elősegítve a hibakeresési alkalmazás állapotbeli problémák állapotával kapcsolatos információkat ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-application-upload"></a>sfctl-alkalmazás feltöltése
Másolja a lemezképtároló egy Service Fabric-alkalmazáscsomagot.

Igény szerint megjelenítése a csomag feltöltése folyamatban van az összes fájl esetében. Feltöltés folyamatban van a érkezik `stderr`.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] elérési útja | Helyi alkalmazáscsomag elérési útja. |
| --imagestore-karakterlánc | Cél lemezképet tárolja az alkalmazáscsomag feltöltéséhez.  Alapértelmezett\: fabric\:ImageStore. |
| --show-folyamatban | Fájl feltöltése folyamatban van a nagy méretű csomagok megjelenítése. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).
