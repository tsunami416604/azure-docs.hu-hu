---
title: Az Azure Service Fabric CLI - sfctl szolgáltatás |} Microsoft Docs
description: A Service Fabric CLI sfctl parancsok ismerteti.
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
ms.openlocfilehash: e027bb7f61d19fc274f7eddd8f28e9e6dac099ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763544"
---
# <a name="sfctl-service"></a>sfctl service
Létrehozása, törlése, és szolgáltatás, a típusú szolgáltatásokat és a service-csomagok kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| alkalmazás-neve | Lekérdezi a szolgáltatás a Service Fabric-alkalmazás nevét. |
| kód csomaglista | Lekérdezi a Service Fabric-csomópont telepítve kód csomagok listáját. |
| létrehozás | A megadott Service Fabric-szolgáltatás létrehozása. |
| törlés | Törli a meglévő Service Fabric-szolgáltatás. |
| telepített típusa | A Service Fabric-fürt egyik csomópontján központilag telepített alkalmazás egy megadott szolgáltatás típusú információ lekérése. |
| telepített típus-listában | A Service Fabric-fürt egyik csomópontján telepített alkalmazások szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása. |
| leírás | Lekérdezi a meglévő Service Fabric szolgáltatás leírását. |
| get-container-logs | A Service Fabric-csomópont telepítve tároló tároló naplóinak beolvasása. |
| állapot | A megadott Service Fabric-szolgáltatás állapotának beolvasása. |
| információ | Az adott szolgáltatás a Service Fabric-alkalmazás tartozó információ lekérése. |
| lista | Az alkalmazás azonosítóját. a megadott alkalmazáshoz tartozó összes szolgáltatás információ lekérése |
| Manifest | A jegyzékfájl egy szolgáltatástípus leíró lekérése. |
| csomag telepítése | Letölti a megadott szolgáltatás jegyzékben megadott csomópont kép gyorsítótárába társított csomagokat. |
| csomag – állapot | A service-csomag állapota információ lekérése a Service Fabric-csomópont és az alkalmazás telepítve egy adott alkalmazáshoz. |
| csomag-adatai | Lekérdezi a Service Fabric-csomópont pontosan a megadott névnek megfelelő telepített service-csomagok listáját. |
| csomag – lista | Lekérdezi a Service Fabric-csomópont telepített service-csomagok listáját. |
| Helyreállítás | Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani a megadott szolgáltatás, amely a kvórum elvesztése jelenleg Beragadt, hogy. |
| report-health | A Service Fabric-szolgáltatás állapotának jelentést küld. |
| megoldás | Hárítsa el a Service Fabric-partícióhoz. |
| Típus-listában | A Service Fabric-fürtök kiépítése alkalmazástípusok által támogatott szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása. |
| Frissítés | Frissíti az adott szolgáltatást az adott frissítés leírása. |

## <a name="sfctl-service-app-name"></a>sfctl service alkalmazás-neve
Lekérdezi a szolgáltatás a Service Fabric-alkalmazás nevét.

Lekérdezi a megadott szolgáltatás alkalmazás nevét. A 404-es FABRIC_E_SERVICE_DOES_NOT_EXIST hiba jelentkezik, ha egy szolgáltatást a megadott felügyeletiszolgáltatás-azonosító nem létezik.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl szolgáltatás kódot--csomaglista
Lekérdezi a Service Fabric-csomópont telepítve kód csomagok listáját.

Lekérdezi a listát a kód egy Service Fabric-csomóponton, az adott alkalmazás telepítése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --Kódnév-csomag | A kódcsomag alkalmazástípust a Service Fabric-fürt részeként regisztrált szolgáltatás jegyzékben megadott neve. |
| --jegyzékfájl-szolgáltatásnév | A Service Fabric-fürt alkalmazástípust részeként regisztrált szolgáltatás jegyzékfájl neve. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-create"></a>sfctl szolgáltatás létrehozása
A megadott Service Fabric-szolgáltatás létrehozása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-id [szükséges] | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --a név [szükséges] | A szolgáltatás neve. Ezt az azonosítót. gyermeke kell Ez az a teljes név többek között a `fabric\:` URI. Például szolgáltatás `fabric\:/A/B` alkalmazás gyermeke `fabric\:/A`. |
| --szolgáltatástípus [szükséges] | A szolgáltatás típusának neve. |
| --aktiválási mód | Az aktiválás módja a service-csomag. |
| --megkötések | Az egy elhelyezési korlátozás karakterláncként. Egy elhelyezési korlátozás csomópont-tulajdonságok logikai kifejezésen, és lehetővé teszik a szolgáltatás korlátozása adott csomópontok szolgáltatás követelményeinek megfelelően. Például szeretné elhelyezni a csomópontok NodeType esetén kék szolgáltatás adja meg a következőket\:"NodeColor kék ==". |
| --korrelált szolgáltatás | A célszolgáltatás függ a neve. |
| --korrelációs | A szolgáltatás igazítás kapcsolatot használó meglévő szolgáltatással összefüggéseket. |
| --dns-name | A létrehozandó szolgáltatás DNS-nevét. A Service Fabric DNS-rendszer szolgáltatás ezt a beállítást engedélyezni kell. |
| ---példányszám | A példányok száma. Ez csak az állapotmentes szolgáltatások vonatkozik. |
| --int-séma | Azt jelzi, hogy a szolgáltatás egységesen kell particionálható között számos különböző előjel nélküli egész számot. |
| --int-séma-száma | Szeretne létrehozni, ha egységes egész partícióséma használatával egész kulcs tartományon belül partíciók száma. |
| rendszer-magas – int | A kulcs egész tartomány, ha egységes egész partícióséma használatával végéig. |
| rendszer-alacsony – int | A kulcs egészek tartományába, ha egységes egész partícióséma használatával elindítása. |
| --betöltési-metrikák | Amikor terheléselosztás szolgáltatási csomópontjai között használt mérőszámok JSON-kódolású listája. |
| --min-replica-set-size | A replikakészlet minimális méretének beállítása egy számot. Ez csak állapotalapú szolgáltatások vonatkozik. |
| --move-cost | A szolgáltatás áthelyezés költséget határozza meg. A lehetséges értékek:\: "Nulla", "Alacsony", "Közepes", "Magas". |
| --nevű séma | Azt jelzi, hogy a szolgáltatás több elnevezett partíciót kell rendelkeznie. |
| --nevű séma-listában | JSON-kódolású listája particionálásához a szolgáltatás között, ha a nevesített partícióséma használatával. |
| --nem megőrzött állapot | Igaz értéke esetén ez azt jelzi a szolgáltatás nem a helyi lemezen tárolt állandó állapotban vannak, vagy csak állapot tárolja a memóriában. |
| --elhelyezési-házirend-listájában | JSON-kódolású elhelyezési házirendeket a szolgáltatás listája, és esetleg hozzá tartozó tartomány nevét. Házirendek lehetnek egy vagy több\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kvórum-adatveszteség-wait | A maximális időtartamot (másodpercben), amelynek partíció számára engedélyezett a kvórum elvesztése alkalmas állapotban. Ez csak állapotalapú szolgáltatások vonatkozik. |
| – a replika-újraindítás-wait | Az időtartam (másodpercben), ha egy replika leáll, és amikor létrejön egy új replika között. Ez csak állapotalapú szolgáltatások vonatkozik. |
| --skálázás házirendek | JSON kódolású skálázás szolgáltatás házirendek listáját. |
| --Egypéldányos-séma | Azt jelzi, a szolgáltatás kell rendelkezik egy olyan partíciót, vagy egy másik szolgáltatás. |
| --készenléti által a replika megőrzése | A maximális időtartamot (másodpercben), mely készenléti replikák fenntartási eltávolításuk előtt. Ez csak állapotalapú szolgáltatások vonatkozik. |
| --állapotalapú alkalmazások és szolgáltatások | Azt jelzi, hogy a szolgáltatás egy olyan állapotalapú szolgáltatás. |
| --az állapot nélküli | Azt jelzi, hogy a szolgáltatás egy olyan állapot nélküli szolgáltatás. |
| --target-replica-set-size | A replikakészlet méretének beállítása egy számot. Ez csak állapotalapú szolgáltatások vonatkozik. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-delete"></a>sfctl szolgáltatás törlése
Törli a meglévő Service Fabric-szolgáltatás.

Egy szolgáltatás akkor törlése előtt létre kell hozni. A Service Fabric alapértelmezés szerint megpróbálja zárja be a szolgáltatás replikák biztonságos módon, és törölje a szolgáltatást. Azonban ha a szolgáltatás a replika szabályosan bezárása problémákat okoz, a delete művelet hosszú időt vehet igénybe vagy elakadnak. Használja a választható ForceRemove jelzőt a szabályos szekvenciazáró kihagyásához és a kényszerített módon törölni a szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
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

## <a name="sfctl-service-deployed-type"></a>sfctl szolgáltatás telepített típusa
A Service Fabric-fürt egyik csomópontján központilag telepített alkalmazás egy megadott szolgáltatás típusú információ lekérése.

A Service Fabric-fürt egyik csomópontján központilag telepített alkalmazások egy adott szolgáltatás típusának kapcsolatos információt tartalmazó listájának beolvasása. A válasz tartalmazza a nevét, a szolgáltatás típusa, a regisztrációs állapot, a kódcsomag regisztrált, és aktiválási szolgáltatás Csomagazonosítóját. Minden bejegyzésben egy aktiválási szolgáltatás típusú különbözteti meg a aktiválási azonosítóját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --szolgáltatás-típusnév [szükséges] | Adja meg a Service Fabric-szolgáltatás típusának neve. |
| --jegyzékfájl-szolgáltatásnév | A telepített szolgáltatások típusra vonatkozó adat szűréséhez a szolgáltatás jegyzékfájl neve. Ha meg van adva, a válasz csak a szolgáltatás jegyzékben meghatározott szolgáltatástípusok információt fogja tartalmazni. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl telepített-típus-lista
A Service Fabric-fürt egyik csomópontján telepített alkalmazások szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása.

A Service Fabric-fürt egyik csomópontján telepített alkalmazások szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása. A válasz tartalmazza a nevét, a szolgáltatás típusa, a regisztrációs állapot, a kódcsomag regisztrált, és aktiválási szolgáltatás Csomagazonosítóját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --jegyzékfájl-szolgáltatásnév | A telepített szolgáltatások típusra vonatkozó adat szűréséhez a szolgáltatás jegyzékfájl neve. Ha meg van adva, a válasz csak a szolgáltatás jegyzékben meghatározott szolgáltatástípusok információt fogja tartalmazni. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-description"></a>sfctl szolgáltatás leírása
Lekérdezi a meglévő Service Fabric szolgáltatás leírását.

Lekérdezi a meglévő Service Fabric szolgáltatás leírását. A szolgáltatás kell létrehozni, mielőtt annak leírását érhető el.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>get-tároló-naplók sfctl szolgáltatás
A Service Fabric-csomópont telepítve tároló tároló naplóinak beolvasása.

A megadott kód csomag a Service Fabric-csomóponton telepített tároló tároló naplóinak beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --kódot--csomagnév [szükséges] | A kódcsomag alkalmazástípust a Service Fabric-fürt részeként regisztrált szolgáltatás jegyzékben megadott neve. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --jegyzékfájl-szolgáltatásnév [szükséges] | A Service Fabric-fürt alkalmazástípust részeként regisztrált szolgáltatás jegyzékfájl neve. |
| – előző | Megadja, hogy a tároló naplók lekérése az kód csomag példány kilépett/kézbesítetlen tárolók. |
| – kiegészítő | A naplók végén sorok száma. Alapértelmezett érték 100. az "összes" megjelenítése a teljes naplókat. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-health"></a>sfctl szolgáltatásának állapota
A megadott Service Fabric-szolgáltatás állapotának beolvasása.

Lekérdezi a megadott szolgáltatás az egészségügyi adatokat. Az EventsHealthStateFilter küldött, az állapota alapján a szolgáltatás állapotával kapcsolatos események gyűjtése szűréséhez használja. Használja a PartitionsHealthStateFilter szűrése adott vissza partíciók száma a gyűjteményben. Ha megad egy szolgáltatás, amely a health Store adatbázisban nem létezik, a kérelem hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |
| --partíciók-rendszerállapot-állapot-szűrő | Lehetővé teszi, hogy a partíciók egészségügyi állapot objektumok szűrése az eredmény abban a állapota alapján szolgáltatás állapotának lekérdezés eredménye. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak azokat a partíciókat a szűrőnek megfelelő adja vissza. Minden olyan partíciónak összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd állapotát a partíciók OK (2) és a figyelmeztető (4) a HealthState értékét visszatér.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-info"></a>sfctl szolgáltatás adatai
Az adott szolgáltatás a Service Fabric-alkalmazás tartozó információ lekérése.

A megadott szolgáltatás a Service Fabric adott alkalmazáshoz tartozó információt ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
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

## <a name="sfctl-service-list"></a>sfctl szolgáltatás listája
Az alkalmazás azonosítóját. a megadott alkalmazáshoz tartozó összes szolgáltatás információ lekérése

Az alkalmazás azonosítóját. a megadott alkalmazáshoz tartozó összes szolgáltatásokkal kapcsolatos adatokat adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --service-type-name | A szolgáltatás típusának neve lekérdezni a szolgáltatások szűrésére használatos. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-manifest"></a>sfctl szolgáltatás jegyzék
A jegyzékfájl egy szolgáltatástípus leíró lekérése.

A jegyzékfájl egy szolgáltatástípus leíró lekérése. A válasz tartalmazza a szolgáltatás jegyzék XML karakterláncként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-típus-neve | Az alkalmazástípus neve. |
| ---alkalmazástípus-verziója [szükséges] | Az alkalmazástípus verziója. |
| --jegyzékfájl-szolgáltatásnév [szükséges] | A Service Fabric-fürt alkalmazástípust részeként regisztrált szolgáltatás jegyzékfájl neve. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-package-deploy"></a>sfctl szolgáltatás csomag telepítése
Letölti a megadott szolgáltatás jegyzékben megadott csomópont kép gyorsítótárába társított csomagokat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típusnév [szükséges] | A kért szolgáltatás megfelelő jegyzékfájl az alkalmazás jegyzékében neve. |
| – alkalmazás-típus-verziója [szükséges] | A kért szolgáltatás megfelelő jegyzékfájl alkalmazás jegyzékfájljának verzióját. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --jegyzékfájl-szolgáltatásnév [szükséges] | Amely letölti a csomagokhoz társított szolgáltatás jegyzékfájl neve. |
| – megosztás-házirend | Megosztási házirendek JSON-kódolású listája. Minden egyes megosztási házirend-elem egy "name" és "hatókör" tevődik össze. A neve megegyezik a kódot, a konfiguráció, vagy csomagot kell osztani a neve. A hatókör lehet "None", "All", "Code", "Config" vagy "Data". |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-package-health"></a>sfctl szolgáltatás csomag-állapota
A service-csomag állapota információ lekérése a Service Fabric-csomópont és az alkalmazás telepítve egy adott alkalmazáshoz.

A service-csomag állapota információ lekérése a Service Fabric-csomópont telepítve egy adott alkalmazáshoz. Az EventsHealthStateFilter HealthEvent objektumok állapota alapján a telepített szervizcsomag jelentett gyűjtemény opcionálisan szűréséhez használja.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| – [szükséges] service-csomag-neve | A service-csomag neve. |
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-package-info"></a>sfctl szolgáltatás csomag-adatai
Lekérdezi a Service Fabric-csomópont pontosan a megadott névnek megfelelő telepített service-csomagok listáját.

A Service Fabric-csomópont, az adott alkalmazáshoz telepített szervizcsomagok információt ad vissza. Ezekkel az eredményekkel vannak szolgáltatás csomagot, amelynek neve egyezik pontosan a csomag neve meg paraméterként.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| – [szükséges] service-csomag-neve | A service-csomag neve. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-package-list"></a>szolgáltatás-csomaglista sfctl
Lekérdezi a Service Fabric-csomópont telepített service-csomagok listáját.

A Service Fabric-csomópont, az adott alkalmazáshoz telepített szervizcsomagok információt ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-recover"></a>sfctl szolgáltatás helyreállítása
Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani a megadott szolgáltatás, amely a kvórum elvesztése jelenleg Beragadt, hogy.

Azt jelzi, hogy a Service Fabric-fürt, hogy kell-e megpróbálni helyreállítani a megadott szolgáltatás, amely a kvórum elvesztése jelenleg Beragadt, hogy. Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl szolgáltatás jelentés-állapota
A Service Fabric-szolgáltatás állapotának jelentést küld.

A jelentés a megadott Service Fabric-szolgáltatás állapotát. A jelentés az állapotjelentést és amelyen jelentett tulajdonság információt kell tartalmaznia. A jelentést küld a Service Fabric-átjáró szolgáltatás, amely továbbítja a health Store adatbázisban. A jelentés lehet, hogy fogadja el az átjárót, de elutasította a health Store adatbázisban a felesleges érvényesítése után. A health Store adatbázisban például utasíthatja a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Tekintse meg, hogy a jelentés a health Store adatbázisban alkalmazta, ellenőrizze, hogy a jelentés megjelenik-e a szolgáltatás állapotának események.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [szükséges] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterlánc, és nem a jelentéskészítő rugalmas a jelentés állapot feltétel kategorizálásának rögzített enumerálási tulajdonság. Olyan jelentéskészítői SourceId "LocalWatchdog" a figyelheti például a csomóponton a rendelkezésre álló lemez állapotának, jelentést ezen a csomóponton "AvailableDisk" tulajdonság. Az azonos jelentéskészítői figyelheti a csomópont csatlakozási, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események a megadott csomópont tekintendők. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [szükséges] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Warning", "Error", "Ismeretlen". |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. <br><br> Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| – [szükséges] adatforrás-azonosítója | A forrás nevét, amely azonosítja az ügyfél, a figyelő vagy a system összetevő, amely állít elő, az egészségügyi adatokat. |
| – Leírás | Az állapotadatok leírása. <br><br> Adja hozzá a jelentés emberi olvasható információk segítségével szabad szöveg jelzi. A leírás maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor a program automatikusan levágja. Csonkolva lesz, ha az utolsó karakter, a leírás tartalmazza-e a mutatóhoz "[Truncated]", és teljes karaktersorozat 4096 karakternél. A jelölő jelzi a felhasználóknak, hogy csonkolása történt. Vegye figyelembe, hogy csonkolja, amikor a leírás legfeljebb 4096 karakternél az eredeti karakterláncból. |
| – azonnali | A jelzőt, amely azt jelzi, hogy a jelentés azonnal kell küldeni. <br><br> Health jelentést küld a Service Fabric átjáró alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz értéke esetén a jelentés azonnal gépről küldött a health Store adatbázisban, függetlenül a fabric ügyfélbeállításaihoz a http-átjáró alkalmazások által használt HTTP-átjárót. Ez akkor hasznos, amelyek a lehető leghamarabb küldjön kritikus jelentésekhez. Attól függően, hogy időzítési és egyéb feltételek a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Immediate értéke HAMIS, ha a jelentés megfelelően küldi el az egészségügyi ügyfélbeállításokat a HTTP-átjáróról. Ezért azt fogja lehet kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az ajánlott beállítása, mivel így az egészségügyi ügyfél állapotát a health Store adatbázisban, valamint a rendszerállapot jelentésfeldolgozás üzenetek reporting optimalizálása érdekében. Alapértelmezés szerint jelentések nem küldése azonnal. |
| --eltávolítása Ha lejárt | Érték, amely azt jelzi, hogy a jelentés eltávolítsa őket a health Store adatbázisban, ha a lejár. <br><br> Ha true értéke esetén a jelentés távolítja el a health Store adatbázisban lejárata után. Ha FALSE értéke esetén a jelentés hibát adott vissza, ha lejárt a rendszer kezeli. Ez a tulajdonság értéke HAMIS, alapértelmezés szerint. Ha az ügyfelek rendszeresen jelentést, azok RemoveWhenExpired hamis (alapértelmezés) kell beállítani. Ezzel a módszerrel a jelentéskészítő problémákkal (például holtpont) rendelkezik, és nem tud jelenteni, az entitás értékelik ki: Hiba történt, amikor az állapotjelentést lejár. Tartalomként jelöli meg, hogy hiba állapotban lévő entitást. |
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

## <a name="sfctl-service-resolve"></a>sfctl szolgáltatás feloldása
Hárítsa el a Service Fabric-partícióhoz.

Hárítsa el a végpontok a szolgáltatás replikák lekérni a Service Fabric szolgáltatás partíció.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás identitásának. Ez általában az a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --partíció-kulcs-típusa | A partíciós kulcs típusa. E paraméter megadása kötelező, ha a szolgáltatás partícióséma Int64Range vagy név. A lehetséges értékek követi. -Nincs (1) – azt jelzi, hogy a PartitionKeyValue paraméter nincs megadva. Ez a partíciók particionálás egypéldányosként séma érvénytelen. Ez az alapértelmezett érték. Az érték 1. -Int64Range (2) – azt jelzi, hogy a PartitionKeyValue paraméter egy int64 partíciós kulcs. Ez a partíciók particionálás Int64Range séma érvénytelen. A 2 érték. -Nevű (3) – azt jelzi, hogy a PartitionKeyValue paraméter a partíció nevét. Ez a partíciók particionálás nevesített séma érvénytelen. Az érték 3. |
| --partíció-kulcs-érték | Partíciós kulcs. Ez azért szükséges, ha a szolgáltatás partícióséma Int64Range vagy név. |
| --previous-rsp-version | A korábban fogadott válasz a verzió mező értéke. Ez azért szükséges, ha a felhasználó ismer, hogy az eredményre igazoló volt korábban már elavult. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-service-type-list"></a>sfctl típus-lista
A Service Fabric-fürtök kiépítése alkalmazástípusok által támogatott szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása.

A Service Fabric-fürtök kiépítése alkalmazástípusok által támogatott szolgáltatástípusok kapcsolatos információt tartalmazó listájának beolvasása. A megadott alkalmazástípus léteznie kell. Ellenkező esetben a 404-es állapotot adott vissza.

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

## <a name="sfctl-service-update"></a>sfctl szolgáltatás frissítése
Frissíti az adott szolgáltatást az adott frissítés leírása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] szolgáltatás azonosítója | A szolgáltatás azonosítója. Ezt az Azonosítót az általában a teljes nevet, a szolgáltatás nélkül a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha a szolgáltatás neve "háló\:/myapp/app1/svc1", a szolgáltatás identitás lenne "myapp\~app1\~svc1" 6.0 + és "myapp/app1/svc1" korábbi verzióiban. |
| --megkötések | Az egy elhelyezési korlátozás karakterláncként. Egy elhelyezési korlátozás csomópont-tulajdonságok logikai kifejezésen, és lehetővé teszik a szolgáltatás korlátozása adott csomópontok szolgáltatás követelményeinek megfelelően. Például szeretné elhelyezni a csomópontok NodeType esetén kék szolgáltatás adja meg a következőket\: "NodeColor kék ==". |
| --korrelált szolgáltatás | A célszolgáltatás függ a neve. |
| --korrelációs | A szolgáltatás igazítás kapcsolatot használó meglévő szolgáltatással összefüggéseket. |
| ---példányszám | A példányok száma. Ez csak az állapotmentes szolgáltatások vonatkozik. |
| --betöltési-metrikák | JSON-kódolású listája metrikák használni terheléselosztás csomópontjai között. |
| --min-replica-set-size | A replikakészlet minimális méretének beállítása egy számot. A mérete csak állapotalapú szolgáltatások vonatkozik. |
| --move-cost | A szolgáltatás áthelyezés költséget határozza meg. A lehetséges értékek:\: "Nulla", "Alacsony", "Közepes", "Magas". |
| --elhelyezési-házirend-listájában | JSON-kódolású elhelyezési házirendeket a szolgáltatás listája, és esetleg hozzá tartozó tartomány nevét. Házirendek lehetnek egy vagy több\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kvórum-adatveszteség-wait | A maximális időtartamot (másodpercben), amelynek partíció számára engedélyezett a kvórum elvesztése alkalmas állapotban. Ennek az időtartamnak csak állapotalapú szolgáltatások vonatkozik. |
| – a replika-újraindítás-wait | Az időtartam (másodpercben), ha egy replika leáll, és amikor létrejön egy új replika között. Ennek az időtartamnak csak állapotalapú szolgáltatások vonatkozik. |
| --skálázás házirendek | JSON kódolású skálázás szolgáltatás házirendek listáját. |
| --készenléti által a replika megőrzése | A maximális időtartamot (másodpercben), mely készenléti replikák fenntartási eltávolításuk előtt. Ennek az időtartamnak csak állapotalapú szolgáltatások vonatkozik. |
| --állapotalapú alkalmazások és szolgáltatások | Azt jelzi, hogy a célként megadott szolgáltatás egy olyan állapotalapú szolgáltatás. |
| --az állapot nélküli | Azt jelzi, hogy a célként megadott szolgáltatás egy olyan állapot nélküli szolgáltatás. |
| --target-replica-set-size | A replikakészlet méretének beállítása egy számot. A mérete csak állapotalapú szolgáltatások vonatkozik. |
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
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
