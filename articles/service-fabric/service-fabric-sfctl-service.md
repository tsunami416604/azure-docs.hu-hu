---
title: Az Azure Service Fabric CLI- sfctl szolgáltatás
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A szolgáltatások, a szolgáltatástípusok és a szolgáltatáscsomagok kezelésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906235"
---
# <a name="sfctl-service"></a>sfctl service
Szolgáltatások, szolgáltatástípusok és szolgáltatáscsomagok létrehozása, törlése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| alkalmazásneve | A Service Fabric-alkalmazás egy szolgáltatás nevét. |
| kódcsomag-lista | A Service Fabric-csomóponton telepített kódcsomagok listájának letöltője. |
| létrehozás | Létrehozza a megadott Service Fabric szolgáltatást. |
| delete | Egy meglévő Service Fabric-szolgáltatás törlése. |
| telepített típusú | A Service Fabric-fürt csomópontján üzembe helyezett alkalmazás adott szolgáltatástípusára vonatkozó információk beírása. |
| telepített típuslista | Letöltőa szolgáltatástípusok a Service Fabric-fürt csomóponton telepített alkalmazások szolgáltatástípusok információkat tartalmazó lista leszármaztatása. |
| leírás | Egy meglévő Service Fabric-szolgáltatás leírása. |
| get-konténer-naplók | Lekéri a tároló naplók egy Service Fabric-csomóponton üzembe helyezett tároló. |
| Egészségügyi | A megadott Service Fabric-szolgáltatás állapotát leadja. |
| Info | A Service Fabric-alkalmazáshoz tartozó adott szolgáltatás információi. |
| lista | Az alkalmazásazonosító által megadott alkalmazáshoz tartozó összes szolgáltatás adatait beszerzi. |
| manifest | Leveszi a szolgáltatástípust leíró jegyzékfájlt. |
| csomag telepítése | A megadott szolgáltatásjegyzékhez társított csomagok letöltése a megadott csomópontlemezgyorsítótárába. |
| csomag-egészségügy | A Service Fabric-csomóponthoz és -alkalmazáshoz telepített adott alkalmazás szolgáltatáscsomagállapotára vonatkozó információkat kapja. |
| csomag-info | A Service Fabric-csomóponton telepített szolgáltatáscsomagok listáját, amely pontosan megfelel a megadott névnek. |
| csomaglista | A Service Fabric-csomóponton telepített szolgáltatáscsomagok listájának letöltője. |
| Visszaszerez | Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani a megadott szolgáltatást, amely jelenleg a kvórumveszteségben ragadt. |
| jelentés-egészségügy | Állapotjelentést küld a Service Fabric szolgáltatásról. |
| Megoldani | Egy Service Fabric-partíció feloldása. |
| típuslista | Leképezi a lista, amely a Service Fabric-fürtben egy kiépített alkalmazástípus által támogatott szolgáltatástípusok adatait tartalmazza. |
| update | Frissíti a megadott szolgáltatást a megadott frissítési leírás sal. |

## <a name="sfctl-service-app-name"></a>sfctl szolgáltatás alkalmazásneve
A Service Fabric-alkalmazás egy szolgáltatás nevét.

Lekéri az alkalmazás nevét a megadott szolgáltatáshoz. 404-es FABRIC_E_SERVICE_DOES_NOT_EXIST hibát ad vissza, ha a szolgáltatás a megadott szolgáltatásazonosítóval nem létezik.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-code-package-list"></a>sfctl szolgáltatás kód-csomag-lista
A Service Fabric-csomóponton telepített kódcsomagok listájának letöltője.

Lekéri a service Fabric-csomóponton az adott alkalmazáshoz telepített kódcsomagok listáját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --kód-csomag-név | A service manifest-ban megadott kódcsomag neve egy Service Fabric-fürtben egy alkalmazástípus részeként regisztrálva. |
| --szolgáltatás-jegyzéknév | A Service Fabric-fürtben egy alkalmazástípus részeként regisztrált szolgáltatásjegyzék neve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-create"></a>sfctl szolgáltatás létrehozása
Létrehozza a megadott Service Fabric szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveka ' '\~karakterrel van elválasztva. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a "myapp app1" lesz a 6.0+ és a "myapp/app1" a korábbi verziókban. |
| --név [Kötelező] | A szolgáltatás neve. Ennek az alkalmazásazonosító gyermekének kell lennie. Ez a teljes név, beleértve az `fabric\:` URI-t is. Például `fabric\:/A/B` a szolgáltatás az `fabric\:/A`alkalmazás gyermeke . |
| --szolgáltatás típusa [Kötelező] | A szolgáltatástípus neve. |
| --aktiválási mód | A szolgáltatáscsomag aktiválási módja. |
| --kényszerek | Az elhelyezési megkötések karakterláncként. Az elhelyezési megkötések logikai kifejezések a csomópont tulajdonságain, és lehetővé teszik, hogy egy szolgáltatást a szolgáltatáskövetelmények alapján adott csomópontokra korlátozzanak. Ha például egy szolgáltatást olyan csomópontokon szeretne elhelyezni, ahol\:a NodeType kék, adja meg a következő "NodeColor == kék" értéket. |
| --korrelált szolgáltatás | A megakadni a célszolgáltatás neve. |
| --korreláció | A szolgáltatás korrelálegy meglévő szolgáltatással egy nyomvonal-affinitás használatával. |
| --dns-név | A létrehozandó szolgáltatás DNS-neve. Ehhez a beállításhoz engedélyezni kell a Service Fabric DNS-rendszerszolgáltatását. |
| --példány-szám | A példányok száma. Ez csak az állapotmentes szolgáltatásokra vonatkozik. |
| --int-rendszer | Azt jelzi, hogy a szolgáltatást egységesen kell particionálni az aláíratlan egész számok között. |
| --int-scheme-count | A létrehozandó egész kulcstartományon belüli partíciók száma, ha egységes egész partíciósémát használ. |
| --int-scheme-magas | A kulcs egész tartományának vége, ha egységes egész partíciósémát használ. |
| --int-scheme-alacsony | A kulcs egész tartományának kezdete, ha egységes egész partíciósémát használ. |
| --terhelés-mérőszámok | JSON kódolt metrikák listáját a terheléselosztási szolgáltatások csomópontok közötti használt metrikák. |
| --min-replika-set-méret | A minimális replikakészlet mérete számként. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --mozgás-költség | A szolgáltatás áthelyezési költségét adja meg. Lehetséges értékek:\: "Zero", "Low", "Medium", "High", "VeryHigh". |
| --megnevezett séma | Azt jelzi, hogy a szolgáltatásnak több elnevezett partícióval kell rendelkeznie. |
| --megnevezett séma-lista | JSON kódolt névlistát a szolgáltatás particionálása között, ha a megnevezett partícióséma használatával. |
| --nincs tartós állapot | Ha ez igaz, ez azt jelzi, hogy a szolgáltatás nem rendelkezik a helyi lemezen tárolt állandó állapottal, vagy csak a memóriában tárolja az állapotot. |
| --elhelyezés-politika-lista | JSON kódolt listája elhelyezési szabályzatok a szolgáltatás, és a kapcsolódó tartományneveket. A házirendek lehetnek\: `NonPartiallyPlaceService` `PreferPrimaryDomain`egy `RequireDomain` `RequireDomainDistribution`vagy több , , , . |
| --kvórum-veszteség-várakozás | Az a maximális időtartam másodpercben, amelynél a partíció kvórumveszteség állapotában lehet. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --replika-újraindítás-várakozás | A kópia lekapcsolása és az új kópia létrehozása között eltelt időtartam másodpercben. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --méretezési házirendek | JSON kódolású skálázási szabályzatok a szolgáltatáshoz. |
| --szolgáltatás-elhelyezés-idő | Az időtartam, amely replikák maradhat InBuild jelentéskészítés előtt, hogy a build beragadt. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --singleton-rendszer | Azt jelzi, hogy a szolgáltatásnak egyetlen partícióval kell rendelkeznie, vagy nem particionált szolgáltatásnak kell lennie. |
| --stand-by-replika-keep | Az a maximális időtartam másodpercben, amelynél a StandBy replikák karbantartásra kerülnek az eltávolítás előtt. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --stateful | Azt jelzi, hogy a szolgáltatás állapotalapú szolgáltatás. |
| --állapot nélküli | Azt jelzi, hogy a szolgáltatás állapotnélküli szolgáltatás. |
| --cél-replika-set-méret | A célkópia készlet mérete számként. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-delete"></a>sfctl szolgáltatás törlése
Egy meglévő Service Fabric-szolgáltatás törlése.

A szolgáltatás törléséhez létre kell hozni egy szolgáltatást. Alapértelmezés szerint a Service Fabric megpróbálja bezárni a szolgáltatás replikák egy kecses módon, majd törölje a szolgáltatást. Ha azonban a szolgáltatás nak problémái vannak a kópia szabályos bezárásával, a törlési művelet hosszú időt vehet igénybe, vagy elakadhat. Az opcionális ForceRemove jelzővel kihagyhatja a kecses zárási sorrendet, és erélyesen törölheti a szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --erő-eltávolítás | Távolítson el egy Service Fabric-alkalmazást vagy -szolgáltatást erélyesen anélkül, hogy a kecses leállítási folyamaton menne keresztül. Ez a paraméter arra használható, hogy erőszakkal törölje az okat az alkalmazásokat vagy szolgáltatást, amelyek törlése időtúllépés a szolgáltatáskódban lévő problémák miatt, amelyek megakadályozzák a replikák kecses bezárását. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-deployed-type"></a>sfctl szolgáltatás telepített típusú
A Service Fabric-fürt csomópontján üzembe helyezett alkalmazás adott szolgáltatástípusára vonatkozó információk beírása.

Letöltőa lista, amely egy adott szolgáltatástípus adatait tartalmazza a Service Fabric-fürt csomópontján telepített alkalmazásokból. A válasz tartalmazza a szolgáltatástípus nevét, regisztrációs állapotát, a regisztrációt regisztráló kódcsomagot és a szolgáltatáscsomag aktiválási azonosítóját. Minden bejegyzés egy szolgáltatástípus egy aktiválását jelöli, amelyet az aktiválási azonosító különböztet meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-típus-név [Kötelező] | A Service Fabric szolgáltatástípus nevét adja meg. |
| --szolgáltatás-jegyzéknév | A szolgáltatásjegyzék neve az üzembe helyezett szolgáltatástípus-információk listájának szűréséhez. Ha meg van adva, a válasz csak a szolgáltatásjegyzékben definiált szolgáltatástípusokra vonatkozó információkat tartalmazza. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl szolgáltatás telepített típuslistája
Letöltőa szolgáltatástípusok a Service Fabric-fürt csomóponton telepített alkalmazások szolgáltatástípusok információkat tartalmazó lista leszármaztatása.

Letöltőa szolgáltatástípusok a Service Fabric-fürt csomóponton telepített alkalmazások szolgáltatástípusok információkat tartalmazó lista leszármaztatása. A válasz tartalmazza a szolgáltatástípus nevét, regisztrációs állapotát, a regisztrációt regisztráló kódcsomagot és a szolgáltatáscsomag aktiválási azonosítóját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-jegyzéknév | A szolgáltatásjegyzék neve az üzembe helyezett szolgáltatástípus-információk listájának szűréséhez. Ha meg van adva, a válasz csak a szolgáltatásjegyzékben definiált szolgáltatástípusokra vonatkozó információkat tartalmazza. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-description"></a>sfctl szolgáltatás leírása
Egy meglévő Service Fabric-szolgáltatás leírása.

Egy meglévő Service Fabric-szolgáltatás leírása. A szolgáltatás nak létre kell hoznia a leírást, mielőtt beszerezhető lenne.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-get-container-logs"></a>sfctl szolgáltatás get-container-logs
Lekéri a tároló naplók egy Service Fabric-csomóponton üzembe helyezett tároló.

Lekéri a tároló naplók egy Service Fabric-csomóponton az adott kódcsomag üzembe helyezett tároló.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --code-package-name [Kötelező] | A service manifest-ban megadott kódcsomag neve egy Service Fabric-fürtben egy alkalmazástípus részeként regisztrálva. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-jegyzéknév [Kötelező] | A Service Fabric-fürtben egy alkalmazástípus részeként regisztrált szolgáltatásjegyzék neve. |
| --előző | Itt adható meg, hogy a rendszer beszeretné-e szerezni a kódcsomagpéldány kilépett/halott tárolóiból származó tárolónaplókat. |
| --farok | A naplók végétől megjelenítandó sorok száma. Az alapértelmezett érték 100. "all", hogy megmutassa a teljes naplókat. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-health"></a>sfctl szolgáltatás állapota
A megadott Service Fabric-szolgáltatás állapotát leadja.

A megadott szolgáltatás állapotinformációinak beszerzése. Az EventsHealthStateFilter használatával szűrheti a szolgáltatáson jelentett állapotesemények gyűjteményét az állapot alapján. A PartitionsHealthStateFilter segítségével szűrje a visszaadott partíciók gyűjteményét. Ha olyan szolgáltatást ad meg, amely nem létezik a health Store-ban, a kérés hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --események-állapot-állapot-szűrő | Lehetővé teszi az állapot alapján visszaadott HealthEvent objektumok gyűjteményének szűrését. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az összes eseményt, amelynek HealthState értéke OK (2) és Figyelmeztetés (4) visszaadja.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --kizárás-egészségügyi statisztikák | Azt jelzi, hogy az állapotstatisztikát vissza kell-e adni a lekérdezés eredményének részeként. Alapértelmezés szerint hamis. A statisztikák az Ok, Figyelmeztetés és Hiba állapotban lévő gyermekentitások számát mutatják. |
| --partíciók-állapot-szűrő | Lehetővé teszi a partíciók állapotát tartalmazó objektumok szűrését a szolgáltatás állapotának állapota alapján visszaadott állapotban. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő partíciókat adja vissza. Az összes partíció az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték lehet ezeknek az értéknek a kombinációja, amelyet bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, majd az OK (2) és a Figyelmeztetés (4) healthState értékkel rendelkező partíciók állapota lesz visszaadva.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-info"></a>sfctl szolgáltatás információ
A Service Fabric-alkalmazáshoz tartozó adott szolgáltatás információi.

A megadott Service Fabric-alkalmazáshoz tartozó megadott szolgáltatás adatait adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-list"></a>sfctl szolgáltatáslista
Az alkalmazásazonosító által megadott alkalmazáshoz tartozó összes szolgáltatás adatait beszerzi.

Az alkalmazásazonosító által megadott alkalmazáshoz tartozó összes szolgáltatás adatait adja eredményül.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --szolgáltatás-típus-név | A szolgáltatástípus neve, amely a lekérdezéshez használt szolgáltatások szűréséhez szolgál. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-manifest"></a>sfctl szolgáltatásjegyzék
Leveszi a szolgáltatástípust leíró jegyzékfájlt.

Leveszi a szolgáltatástípust leíró jegyzékfájlt. A válasz a szolgáltatásjegyzék XML-fájlját tartalmazza karakterláncként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-típus-név [Kötelező] | Az alkalmazástípus neve. |
| --alkalmazás-típus-verzió [Kötelező] | Az alkalmazástípus verziója. |
| --szolgáltatás-jegyzéknév [Kötelező] | A Service Fabric-fürtben egy alkalmazástípus részeként regisztrált szolgáltatásjegyzék neve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-deploy"></a>sfctl szolgáltatáscsomag telepítése
A megadott szolgáltatásjegyzékhez társított csomagok letöltése a megadott csomópontlemezgyorsítótárába.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-típus-név [Kötelező] | A megfelelő kért szolgáltatásjegyzékhez tartozó alkalmazásjegyzék neve. |
| --app-type-version [Kötelező] | A megfelelő kért szolgáltatásjegyzékhez tartozó alkalmazásjegyzék verziója. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-jegyzéknév [Kötelező] | A letöltendő csomagokhoz társított szolgáltatásjegyzék neve. |
| --részvény-politika | JSON kódolt lista megosztási szabályzatok. Minden megosztási irányelv elem egy "névből" és "hatókörből" áll. A név a megosztandó kód, konfiguráció vagy adatcsomag nevének felel meg. A hatály lehet "Nincs", "Mind", "Kód", "Config" vagy "Data". |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-health"></a>sfctl szolgáltatás csomag-egészségügy
A Service Fabric-csomóponthoz és -alkalmazáshoz telepített adott alkalmazás szolgáltatáscsomagállapotára vonatkozó információkat kapja.

A Service Fabric-csomóponton telepített adott alkalmazás szolgáltatáscsomagállapotára vonatkozó információkat kapja. Az EventsHealthStateFilter használatával tetszés szerint szűrheti az állapot alapján telepített szolgáltatáscsomagon jelentett HealthEvent objektumok gyűjteményét.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-csomag-név [Kötelező] | A szolgáltatáscsomag neve. |
| --események-állapot-állapot-szűrő | Lehetővé teszi az állapot alapján visszaadott HealthEvent objektumok gyűjteményének szűrését. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az összes eseményt, amelynek HealthState értéke OK (2) és Figyelmeztetés (4) visszaadja.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-info"></a>sfctl szolgáltatás csomag-info
A Service Fabric-csomóponton telepített szolgáltatáscsomagok listáját, amely pontosan megfelel a megadott névnek.

A Service Fabric-csomóponton az adott alkalmazáshoz telepített szolgáltatáscsomagokra vonatkozó információkat adja vissza. Ezek az eredmények olyan szolgáltatáscsomagok, amelyek neve pontosan megegyezik a paraméterként megadott szolgáltatáscsomag nevével.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --szolgáltatás-csomag-név [Kötelező] | A szolgáltatáscsomag neve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-list"></a>sfctl szolgáltatáscsomag-lista
A Service Fabric-csomóponton telepített szolgáltatáscsomagok listájának letöltője.

A Service Fabric-csomóponton az adott alkalmazáshoz telepített szolgáltatáscsomagokra vonatkozó információkat adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-recover"></a>sfctl szolgáltatás helyreállítása
Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani a megadott szolgáltatást, amely jelenleg a kvórumveszteségben ragadt.

Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani a megadott szolgáltatást, amely jelenleg a kvórumveszteségben ragadt. Ezt a műveletet csak akkor kell végrehajtani, ha ismeretes, hogy a nem működő replikák nem állíthatók helyre. Az API helytelen használata adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-report-health"></a>sfctl szolgáltatás jelentés-egészségügy
Állapotjelentést küld a Service Fabric szolgáltatásról.

A megadott Service Fabric szolgáltatás állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentés forrására és a jelentett tulajdonságra vonatkozó információkat. A jelentés egy Service Fabric átjáró szolgáltatás, amely továbbítja a health Store. A jelentést az átjáró elfogadhatja, de az egészségügyi tároló további ellenőrzés után elutasítja. Például a health store elutasíthatja a jelentést, mert egy érvénytelen paraméter, mint például egy elavult sorszám. Ha meg szeretné tudni, hogy a jelentés lett-e alkalmazva az állapottárolóban, ellenőrizze, hogy a jelentés megjelenik-e a szolgáltatás állapoteseményeiben.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --health-property [Kötelező] | Az egészségügyi információk tulajdonsága. <br><br> Egy entitás különböző tulajdonságokállapot-jelentésekkel rendelkezhet. A tulajdonság egy karakterlánc, és nem egy rögzített felsorolás, amely lehetővé teszi az előjelentés rugalmasságát a jelentést kiváltó állapot kategorizálására. Például egy "LocalWatchdog" SourceId azonosítóval rendelkező jelentéskészítő figyelheti a csomóponton rendelkezésre álló lemez állapotát, így jelentheti az "AvailableDisk" tulajdonságot az adott csomóponton. Ugyanaz a jelentéskészítő figyelheti a csomópont-kapcsolatot, így jelentheti a "Kapcsolat" tulajdonságot ugyanazon a csomóponton. Az állapottárolóban ezeket a jelentéseket külön állapoteseményekként kezeli a megadott csomópont. A SourceId-dal együtt a tulajdonság egyedileg azonosítja az állapotinformációkat. |
| --állapot[Kötelező] | A lehetséges\: értékek a következők: "Érvénytelen", "Ok", "Figyelmeztetés", "Hiba", "Ismeretlen". |
| --service-id [Kötelező] | A szolgáltatás identitása. <br><br> Ez általában a teljes nevét a szolgáltatás\:nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveka ' '\~karakterrel van elválasztva. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~a "myapp app1 svc1" lesz a 6.0+ és a "myapp/app1/svc1" a korábbi verziókban. |
| --source-id [Kötelező] | A forrásnév, amely azonosítja az ügyfél/figyelő/rendszerösszetevő, amely létrehozta az állapotadatokat. |
| --leírás | Az egészségügyi információk leírása. <br><br> Szabad szöveget jelöl, amely a jelentéssel kapcsolatos emberi olvasható információk hozzáadására szolgál. A leírás maximális karakterlánchossza 4096 karakter. Ha a megadott karakterlánc hosszabb, a program automatikusan csonkolja. Csonkoláskor a leírás utolsó karakterei a "[Csonka]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi a felhasználóknak, hogy csonkolás történt. Ne feledje, hogy csonkoláskor a leírás kevesebb mint 4096 karaktert tartalmaz az eredeti karakterláncból. |
| --azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e küldeni. <br><br> Egy állapotjelentés egy Service Fabric átjáró alkalmazás, amely továbbítja a health Store. Ha az Immediate értéke igaz, a rendszer azonnal elküldi a jelentést a HTTP-átjáróból a health Store-ba, függetlenül attól, hogy a HTTP átjáróalkalmazás által használt háló-ügyfél-beállításokat használja.If Immediate is set to true, the report is sent immediately from HTTP Gateway to the health store, regardless of the fabric client settings that the HTTP Gateway Application is using. Ez akkor hasznos, ha a kritikus jelentéseket a lehető leghamarabb el kell küldeni. Az időzítéstől és az egyéb feltételektől függően a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró be van zárva, vagy ha az üzenet nem éri el az átjárót. Ha az Immediate értéke hamis, a rendszer a http-átjáró ból származó állapotügyfél-beállítások alapján küldi el a jelentést. Ezért a rendszer a HealthReportSendInterval konfigurációnak megfelelően lesz kötegelve. Ez az ajánlott beállítás, mert lehetővé teszi, hogy az állapotjelző ügyfél optimalizálja az állapotjelentési üzeneteket az állapottárolónak, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a jelentések nem kerülnek azonnal elküldésre. |
| --remove-when-expired | Érték, amely azt jelzi, hogy a jelentés törlődik-e az állapottárolóból, amikor lejár. <br><br> Ha értéke igaz, a jelentés törlődik a health Store lejárta után. Ha hamis ra van állítva, a jelentés lejártakor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeres időközönként jelentést, meg kell állítani a RemoveWhenExpired false (alapértelmezett). Ily módon a jelentéskészítő problémák (pl. holtpont), és nem tud jelentést, az entitás kiértékelése hiba, amikor az állapotjelentés lejár. Ez az entitást hibaállapotként jelzi. |
| --szekvenciaszám | Az állapotjelentés sorozatszáma numerikus karakterláncként. <br><br> A jelentéssorszámot az állapottároló az elavult jelentések észlelésére használja. Ha nincs megadva, a jelentés hozzáadásakor az állapotügyfél automatikusan létrehoz egy sorszámot. |
| --idő-out -t | Alapértelmezett\: 60. |
| --ttl | Az az időtartam, amelyre ez az állapotjelentés érvényes. Ez a mező Az Időtartam megadásához Az ISO8601 formátumot használja. <br><br> Amikor az ügyfelek rendszeres időközönként jelentést küldenek, az életidejéneknél nagyobb gyakorisággal kell jelentéseket küldeniük. Ha az ügyfelek az átmenetről számolnak be, beállíthatják, hogy az élet ideje végtelen. Amikor lejár az életidő, az állapotadatokat tartalmazó állapotesemény törlődik az állapottárolóból, ha az RemoveWhenExpired igaz, vagy hiba esetén kiértékelve, ha az RemoveWhenExpired hamis. Ha nincs megadva, az élethez való idő nem éri el a végtelen értéket. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-resolve"></a>sfctl szolgáltatás feloldása
Egy Service Fabric-partíció feloldása.

A Service Fabric szolgáltatáspartíció feloldása a szolgáltatás replikák végpontjainak leése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez az azonosító általában a teljes neve a\:szolgáltatás nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~"myapp app1 svc1" lesz 6.0+ és "myapp/app1/svc1" a korábbi verziókban. |
| --partíció-kulcs-típusú | A partíció kulcstípusa. Erre a paraméterre akkor van szükség, ha a szolgáltatás partícióséma Int64Range vagy Named. A lehetséges értékek a következők. - Nincs (1) - Azt jelzi, hogy a PartitionKeyValue paraméter nincs megadva. Ez a singleton particionálási sémával rendelkező partíciókra érvényes. Ez az alapértelmezett érték. Az érték 1. - Int64Range (2) - Azt jelzi, hogy a PartitionKeyValue paraméter egy int64 partíciókulcs. Ez az Int64Range particionálási sémával rendelkező partíciókra érvényes. Az érték 2. - Named (3) - Azt jelzi, hogy a PartitionKeyValue paraméter a partíció neve. Ez a particionálási sémával rendelkező partíciók neve. Az érték 3. |
| --partíció-kulcs-érték | Partíciókulcs. Erre akkor van szükség, ha a szolgáltatás partícióséma Int64Range vagy Named. Ez nem a partíció azonosítója, hanem az egész kulcs értéke vagy a partícióazonosító neve. Ha például a szolgáltatás 0 és 10 közötti tartományú partíciókat használ, akkor a PartitionKeyValue egész szám lenne ebben a tartományban. A tartomány vagy név megtekintéséhez kérdezze le a szolgáltatás leírását. |
| --előző-rsp-verzió | A korábban kapott válasz Verzió mezőjének értéke. Erre akkor van szükség, ha a felhasználó tudja, hogy a korábban leütött eredmény elavult. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-type-list"></a>sfctl szolgáltatás típuslistája
Leképezi a lista, amely a Service Fabric-fürtben egy kiépített alkalmazástípus által támogatott szolgáltatástípusok adatait tartalmazza.

Leképezi a lista, amely a Service Fabric-fürtben egy kiépített alkalmazástípus által támogatott szolgáltatástípusok adatait tartalmazza. A megadott alkalmazástípusnak léteznie kell. Ellenkező esetben a rendszer 404-es állapotot ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-típus-név [Kötelező] | Az alkalmazástípus neve. |
| --alkalmazás-típus-verzió [Kötelező] | Az alkalmazástípus verziója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-update"></a>sfctl szolgáltatás frissítése
Frissíti a megadott szolgáltatást a megadott frissítési leírás sal.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --service-id [Kötelező] | A szolgáltatás identitása. Ez általában a teljes nevét a szolgáltatás\:nélkül a "fabric" URI-séma. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például a szolgáltatás neve\:"fabric /myapp/app1/svc1", a szolgáltatás\~identitása\~a "myapp app1 svc1" lesz a 6.0+ és a "myapp/app1/svc1" a korábbi verziókban. |
| --kényszerek | Az elhelyezési megkötések karakterláncként. Az elhelyezési megkötések logikai kifejezések a csomópont tulajdonságain, és lehetővé teszik, hogy egy szolgáltatást a szolgáltatáskövetelmények alapján adott csomópontokra korlátozzanak. Ha például egy szolgáltatást olyan csomópontokon szeretne elhelyezni, ahol\: a NodeType kék, adja meg a következő "NodeColor == kék" értéket. |
| --korrelált szolgáltatás | A megakadni a célszolgáltatás neve. |
| --korreláció | A szolgáltatás korrelálegy meglévő szolgáltatással egy nyomvonal-affinitás használatával. |
| --példány-szám | A példányok száma. Ez csak az állapotmentes szolgáltatásokra vonatkozik. |
| --terhelés-mérőszámok | JSON kódolt metrikák listáját a csomópontok közötti terheléselosztás használt metrikák. |
| --min-replika-set-méret | A minimális replikakészlet mérete számként. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --mozgás-költség | A szolgáltatás áthelyezési költségét adja meg. Lehetséges értékek:\: "Zero", "Low", "Medium", "High", "VeryHigh". |
| --elhelyezés-politika-lista | JSON kódolt listája elhelyezési szabályzatok a szolgáltatás, és a kapcsolódó tartományneveket. A házirendek lehetnek\: `NonPartiallyPlaceService` `PreferPrimaryDomain`egy `RequireDomain` `RequireDomainDistribution`vagy több , , , . |
| --kvórum-veszteség-várakozás | Az a maximális időtartam másodpercben, amelynél a partíció kvórumveszteség állapotában lehet. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --replika-újraindítás-várakozás | A kópia lekapcsolása és az új kópia létrehozása között eltelt időtartam másodpercben. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --méretezési házirendek | JSON kódolású skálázási szabályzatok a szolgáltatáshoz. |
| --szolgáltatás-elhelyezés-idő | Az időtartam, amely replikák maradhat InBuild jelentéskészítés előtt, hogy a build beragadt. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --stand-by-replika-keep | Az a maximális időtartam másodpercben, amelynél a StandBy replikák karbantartásra kerülnek az eltávolítás előtt. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --stateful | Azt jelzi, hogy a célszolgáltatás állapotalapú szolgáltatás. |
| --állapot nélküli | Azt jelzi, hogy a célszolgáltatás állapot nélküli szolgáltatás. |
| --cél-replika-set-méret | A célkópia készlet mérete számként. Ez csak az állapotalapú szolgáltatásokra vonatkozik. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.
