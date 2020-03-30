---
title: Azure Service Fabric CLI- sfctl alkalmazás
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Az alkalmazások kezeléséhez szükséges parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b4e1066bba1db387c9dc0600bc55522f0b5fe897
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906196"
---
# <a name="sfctl-application"></a>sfctl-alkalmazás
Alkalmazások és alkalmazástípusok létrehozása, törlése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Létrehoz egy Service Fabric-alkalmazást a megadott leírás használatával. |
| delete | Egy meglévő Service Fabric-alkalmazás törlése. |
| Telepített | A Service Fabric-csomóponton telepített alkalmazás információinak bekerülése. |
| telepített-egészségügyi | A Service Fabric-csomóponton telepített alkalmazás állapotának információkat kapja. |
| telepített lista | A Service Fabric-csomóponton telepített alkalmazások listájának letöltője. |
| Egészségügyi | A szolgáltatásháló-alkalmazás állapotát kapja. |
| Info | A Service Fabric-alkalmazáshoz való információ leése. |
| lista | Letöltőa Service Fabric-fürtben létrehozott, a megadott szűrőknek megfelelő alkalmazások listáját. |
| betöltés | Betöltődik a Service Fabric-alkalmazás betöltési információi. |
| manifest | Lekéri az alkalmazástípust leíró jegyzékfájlt. |
| Rendelkezés | A Service Fabric-alkalmazástípust a fürthöz a külső tárolóban lévő ".sfpkg" csomag vagy a rendszerképtárban lévő alkalmazáscsomag használatával regisztrálja. |
| jelentés-egészségügy | Állapotjelentést küld a Service Fabric-alkalmazásról. |
| type | A Service Fabric-fürtben lévő alkalmazástípusok listáját, amely pontosan a megadott névnek felel meg. |
| típuslista | Letöltőa Service Fabric-fürt alkalmazástípusainak listája. |
| kiépítés nélküli | Eltávolítja vagy törli a Service Fabric-alkalmazástípust a fürtből. |
| Frissítés | Elindítja egy alkalmazás frissítését a Service Fabric-fürtben. |
| frissítés-folytatás | Folytatja egy alkalmazás frissítését a Service Fabric-fürtben. |
| frissítés-visszaállítás | Elindítja a Service Fabric-fürtben lévő alkalmazás folyamatban lévő frissítésének visszaállítása. |
| frissítési állapot | Az alkalmazáson végrehajtott legújabb frissítés részleteinek bekerülése. |
| feltöltés | A Service Fabric-alkalmazáscsomag másolása a lemezképtárolóba. |

## <a name="sfctl-application-create"></a>sfctl alkalmazás létrehozása
Létrehoz egy Service Fabric-alkalmazást a megadott leírás használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazásneve [Kötelező] | Az alkalmazás neve, beleértve a\:"fabric" URI sémát. |
| --alkalmazás-típus [Kötelező] | Az alkalmazásjegyzékben található alkalmazástípus neve. |
| --app-verzió [Kötelező] | Az alkalmazástípus nak az alkalmazásjegyzékben meghatározott verziója. |
| --max-csomópont-számláló | A szolgáltatások, amelyekhez a Service Fabric kapacitást foglal le, a csomópontok maximális száma. Vegye figyelembe, hogy ez nem jelenti azt, hogy az alkalmazás szolgáltatásai az összes ilyen csomóponton lesznek elhelyezve. |
| --mutatók | A JSON kódolt lista alkalmazás kapacitás metrika leírások. A metrika névként van definiálva, amely minden olyan csomópont kapacitáskészletéhez van társítva, amelyen az alkalmazás létezik. |
| --min-csomópont-szám | A minimális számú csomópontok, ahol a Service Fabric lefoglalja a kapacitást az alkalmazáshoz. Vegye figyelembe, hogy ez nem jelenti azt, hogy az alkalmazás szolgáltatásai az összes ilyen csomóponton lesznek elhelyezve. |
| --paraméterek | Az alkalmazás létrehozásakor alkalmazandó alkalmazásparaméter-felülbírálások JSON-kódolású listája. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-delete"></a>sfctl alkalmazás törlése
Egy meglévő Service Fabric-alkalmazás törlése.

Az alkalmazást a törlés előtt létre kell hozni. Az alkalmazás törlése törli az alkalmazás részét vevő összes szolgáltatást. Alapértelmezés szerint a Service Fabric megpróbálja bezárni a szolgáltatás replikák egy kecses módon, majd törölje a szolgáltatást. Ha azonban egy szolgáltatás nak problémái vannak a kópia szabályos bezárásával, a törlési művelet hosszú időt vehet igénybe, vagy elakadhat. A választható ForceRemove jelzővel kihagyhatja a kecses zárási sorrendet, és erőszakkal törölheti az alkalmazást és annak összes szolgáltatását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
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

## <a name="sfctl-application-deployed"></a>sfctl alkalmazás telepítve
A Service Fabric-csomóponton telepített alkalmazás információinak bekerülése.

Ez a lekérdezés rendszeralkalmazás-adatokat ad vissza, ha a megadott alkalmazásazonosító rendszeralkalmazáshoz szolgál. Az eredmények magukban foglalják az aktív, aktiváló és letöltési állapotokban telepített alkalmazásokat. Ez a lekérdezés megköveteli, hogy a csomópont neve a fürt egyik csomópontjának feleljen meg. A lekérdezés sikertelen lesz, ha a megadott csomópontnév nem mutat a fürt egyik aktív Service Fabric-csomópontjára sem.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --include-health-state | Egy entitás állapotának belefoglalása. Ha ez a paraméter hamis vagy nincs megadva, akkor a visszaadott állapot "Ismeretlen". Ha értéke igaz, a lekérdezés megy párhuzamosan a csomópont és az állapotrendszer szolgáltatás, mielőtt az eredmények egyesítése. Ennek eredményeképpen a lekérdezés drágább, és hosszabb időt vehet igénybe. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-deployed-health"></a>sfctl alkalmazás üzembe helyezett-állapot
A Service Fabric-csomóponton telepített alkalmazás állapotának információkat kapja.

A Service Fabric-csomóponton telepített alkalmazás állapotának információkat kapja. Az EventsHealthStateFilter használatával tetszés szerint szűrheti az üzembe helyezett alkalmazáson az állapot alapján jelentett HealthEvent objektumok gyűjteményét. A DeployedServicePackagesHealthFilter használatával tetszés szerint szűrheti a DeployedServicePackageHealth gyermekek állapotát.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --deployed-service-packages-state-filter | Lehetővé teszi az üzembe helyezett szolgáltatáscsomag állapotállapot-objektumok szűrését az üzembe helyezett alkalmazás állapotlekérdezésének eredményeként az állapotuk alapján. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. Csak a szűrőnek megfelelő üzembe helyezett szolgáltatáscsomagokat adja vissza a rendszer. Az összes üzembe helyezett szolgáltatáscsomag az üzembe helyezett alkalmazás összesített állapotának kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, majd a Service healthState-értékkel rendelkező HealthState-értékkel rendelkező állapot (OK) és (4) értéket adja vissza.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --események-állapot-állapot-szűrő | Lehetővé teszi az állapot alapján visszaadott HealthEvent objektumok gyűjteményének szűrését. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az összes eseményt, amelynek HealthState értéke OK (2) és Figyelmeztetés (4) visszaadja.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --kizárás-egészségügyi statisztikák | Azt jelzi, hogy az állapotstatisztikát vissza kell-e adni a lekérdezés eredményének részeként. Alapértelmezés szerint hamis. A statisztikák az Ok, Figyelmeztetés és Hiba állapotban lévő gyermekentitások számát mutatják. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-deployed-list"></a>sfctl alkalmazás telepített listája
A Service Fabric-csomóponton telepített alkalmazások listájának letöltője.

A Service Fabric-csomóponton telepített alkalmazások listájának letöltője. Az eredmények nem tartalmazzák az üzembe helyezett rendszeralkalmazásokra vonatkozó információkat, kivéve, ha az azonosító kifejezetten lekérdezi őket. Az eredmények magukban foglalják az aktív, aktiváló és letöltési állapotokban telepített alkalmazásokat. Ez a lekérdezés megköveteli, hogy a csomópont neve a fürt egyik csomópontjának feleljen meg. A lekérdezés sikertelen lesz, ha a megadott csomópontnév nem mutat a fürt egyik aktív Service Fabric-csomópontjára sem.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --include-health-state | Egy entitás állapotának belefoglalása. Ha ez a paraméter hamis vagy nincs megadva, akkor a visszaadott állapot "Ismeretlen". Ha értéke igaz, a lekérdezés megy párhuzamosan a csomópont és az állapotrendszer szolgáltatás, mielőtt az eredmények egyesítése. Ennek eredményeképpen a lekérdezés drágább, és hosszabb időt vehet igénybe. |
| --max-eredmények | A lapozható lekérdezések részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg a visszaadott eredmények számának felső határát. A visszaadott eredmények kisebbek lehetnek, mint a megadott maximális eredmények, ha nem férnek el az üzenetben a konfigurációban megadott maximális üzenetméret-korlátozások nak megfelelően. Ha ez a paraméter nulla vagy nincs megadva, a lapozható lekérdezés a lehető legtöbb eredményt tartalmazza, amely elfér a visszatérési üzenetben. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-health"></a>sfctl alkalmazás állapota
A szolgáltatásháló-alkalmazás állapotát kapja.

A szolgáltatásháló-alkalmazás heath állapotát adja vissza. A válasz ok, hiba vagy figyelmeztetés állapotát jelenti. Ha az entitás nem található a health store, akkor visszaadja hiba.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --deployed-applications-állapot-szűrő | Lehetővé teszi az üzembe helyezett alkalmazások állapotállapot-objektumok szűrését az alkalmazás állapotuk alapján az alkalmazás állapotának eredményeként visszaadott állapotban. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. Csak a szűrőnek megfelelő telepített alkalmazások kerülnek vissza. Az összes telepített alkalmazás az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja is lehet, amelyet bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, majd az OK (2) és a Figyelmeztetés (4) healthstate értékű telepített alkalmazások állapotának állapotát adja vissza.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --események-állapot-állapot-szűrő | Lehetővé teszi az állapot alapján visszaadott HealthEvent objektumok gyűjteményének szűrését. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az összes eseményt, amelynek HealthState értéke OK (2) és Figyelmeztetés (4) visszaadja.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --kizárás-egészségügyi statisztikák | Azt jelzi, hogy az állapotstatisztikát vissza kell-e adni a lekérdezés eredményének részeként. Alapértelmezés szerint hamis. A statisztikák az Ok, Figyelmeztetés és Hiba állapotban lévő gyermekentitások számát mutatják. |
| --szolgáltatások-állapot-állapot-szűrő | Lehetővé teszi a szolgáltatások állapotobjektumok szűrését a szolgáltatások állapotállapotuk alapján visszaadott állapoteredményeként. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő szolgáltatásokat adja vissza. Az összes szolgáltatás az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja is lehet, amelyet bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, majd a HealthState (OK) és a Figyelmeztetés (4) állapotú szolgáltatások állapota lesz visszaadva.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-info"></a>sfctl alkalmazás adatai
A Service Fabric-alkalmazáshoz való információ leése.

A létrehozott vagy a Service Fabric-fürtben létrehozott alkalmazás adatait adja vissza, amelynek neve megegyezik a paraméterként megadott alkatával. A válasz tartalmazza az alkalmazás nevét, típusát, állapotát, paramétereit és egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --kizárás-alkalmazás-paraméterek | Az a jelző, amely meghatározza, hogy az alkalmazás paraméterei ki lesznek-e zárva az eredményből. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-list"></a>sfctl alkalmazáslista
Letöltőa Service Fabric-fürtben létrehozott, a megadott szűrőknek megfelelő alkalmazások listáját.

A létrehozott vagy a Service Fabric-fürtben létrehozott alkalmazásokra vonatkozó információkat beszerzi, és megfelel nek a megadott szűrőknek. A válasz tartalmazza az alkalmazás nevét, típusát, állapotát, paramétereit és egyéb részleteit. Ha az alkalmazások nem férnek el egy oldalon, egy oldal eredmények vissza, valamint a folytatási token, amely segítségével kap a következő oldalon. Az ApplicationTypeName és az ApplicationDefinitionKindFilter szűrők nem adhatók meg egyszerre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-meghatározás-fajta-szűrő | Az ApplicationDefinitionKind szűrésére szolgál, amely a Service Fabric-alkalmazások definiálásához használt mechanizmus.  <br> - Alapértelmezett - Alapértelmezett érték, amely ugyanazt a funkciót látja el, mint az "Összes" kiválasztása. Az érték 0.  <br> - All - Szűrő, amely egyezteti a bemeneti bármely ApplicationDefinitionKind értékkel. Az érték 65535.  <br> - ServiceFabricApplicationDescription - Az ApplicationDefinitionKind érték ServiceFabricApplicationDescription értékkel rendelkező bemenetnek megfelelő szűrő. Az érték 1.  <br> - Írás - Szűrő, amely megfelel a bemeneti ApplicationDefinitionKind érték Compose. Az érték 2. |
| --alkalmazás-típus-név | Az alkalmazástípus neve, amely a lekérdezendő alkalmazások szűréséhez használható. Ez az érték nem tartalmazhatja az alkalmazástípus verzióját. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --kizárás-alkalmazás-paraméterek | Az a jelző, amely meghatározza, hogy az alkalmazás paraméterei ki lesznek-e zárva az eredményből. |
| --max-eredmények | A lapozható lekérdezések részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg a visszaadott eredmények számának felső határát. A visszaadott eredmények kisebbek lehetnek, mint a megadott maximális eredmények, ha nem férnek el az üzenetben a konfigurációban megadott maximális üzenetméret-korlátozások nak megfelelően. Ha ez a paraméter nulla vagy nincs megadva, a lapozható lekérdezés a lehető legtöbb eredményt tartalmazza, amely elfér a visszatérési üzenetben. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-load"></a>sfctl alkalmazás terhelése
Betöltődik a Service Fabric-alkalmazás betöltési információi.

A létrehozott vagy a Service Fabric-fürtben létrehozott alkalmazás betöltési adatait adja vissza, amelynek neve megegyezik a paraméterként megadott alkatával. A válasz tartalmazza a nevet, a minimális csomópontokat, a maximális csomópontokat, az alkalmazás által jelenleg elfoglaló csomópontok számát és az alkalmazás terhelési metrika adatait az alkalmazásról.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-manifest"></a>sfctl alkalmazásjegyzék
Lekéri az alkalmazástípust leíró jegyzékfájlt.

A válasz az alkalmazás jegyzékfájl XML-fájlját karakterláncként tartalmazza.

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

## <a name="sfctl-application-provision"></a>sfctl alkalmazás nyújtása
A Service Fabric-alkalmazástípust a fürthöz a külső tárolóban lévő ".sfpkg" csomag vagy a rendszerképtárban lévő alkalmazáscsomag használatával regisztrálja.

A Service Fabric alkalmazástípusát a fürthöz kell lebiztosítani. A rendelkezésre az új alkalmazások példányos létrehozása előtt van szükség. A létesítési művelet a relativePathInImageStore által megadott alkalmazáscsomagon vagy a külső ".sfpkg" URI használatával hajtható végre. Kivéve, ha --külső-provision van beállítva, ez a parancs várható képtároló kiépítése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-csomag-download-uri | A ".sfpkg" alkalmazáscsomag elérési útja, ahonnan az alkalmazáscsomag HTTP vagy HTTPS protokollok használatával letölthető. <br><br> Csak a kiépítéshez csak külső raktárban. Az alkalmazáscsomag egy külső tárolóban tárolható, amely GET-műveletet biztosít a fájl letöltéséhez. A támogatott protokollok HTTP és HTTPS, és az elérési útnak engedélyeznie kell az OLVASÁSi hozzáférést. |
| --alkalmazás-típus-build-path | A rendelkezés fajta kép tárolására csak. Az alkalmazáscsomag relatív elérési útja a lemezképtárolóban az előző feltöltési művelet során megadott. |
| --alkalmazás-típus-név | Csak a kiépítéshez csak külső raktárban. Az alkalmazástípus neve az alkalmazásjegyzékben található alkalmazástípus nevét jelöli. |
| --alkalmazás-típus-verzió | Csak a kiépítéshez csak külső raktárban. Az alkalmazástípus verziója az alkalmazástípus nak az alkalmazásjegyzékben található verzióját jelöli. |
| --külső ellátás | Az a hely, ahonnan az alkalmazáscsomag regisztrálható vagy kiépíthető. Azt jelzi, hogy a rendelkezésre egy olyan alkalmazáscsomag, amelyet korábban feltöltött egy külső tárolóba. Az alkalmazáscsomag a *.sfpkg kiterjesztéssel végződik. |
| --nem-várjon | Azt jelzi, hogy a kiépítés aszinkron módon történjen-e. <br><br> Ha a beállítás igaz, a létesítési művelet visszaadja, ha a rendszer elfogadja a kérelmet, és a létesítési művelet időkorlát nélkül folytatódik. Az alapértelmezett érték a hamis. Nagy alkalmazáscsomagok esetén azt javasoljuk, hogy az értéket igaz értékre kell alapozni. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-report-health"></a>sfctl alkalmazás jelentés-egészségügy
Állapotjelentést küld a Service Fabric-alkalmazásról.

A megadott Service Fabric-alkalmazás állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentés forrására és a jelentett tulajdonságra vonatkozó információkat. A jelentés egy Service Fabric átjáró alkalmazás, amely továbbítja a health Store. A jelentést az átjáró elfogadhatja, de az egészségügyi tároló további ellenőrzés után elutasítja. Például a health store elutasíthatja a jelentést, mert egy érvénytelen paraméter, mint például egy elavult sorszám. Ha meg szeretné tudni, hogy a jelentés alkalmazva lett-e az állapottárolóban, ellenőrizze az alkalmazás állapotát, és ellenőrizze, hogy a jelentés megjelenik-e.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveka ' '\~karakterrel van elválasztva. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a "myapp app1" lesz a 6.0+ és a "myapp/app1" a korábbi verziókban. |
| --health-property [Kötelező] | Az egészségügyi információk tulajdonsága. <br><br> Egy entitás különböző tulajdonságokállapot-jelentésekkel rendelkezhet. A tulajdonság egy karakterlánc, és nem egy rögzített felsorolás, amely lehetővé teszi az előjelentés rugalmasságát a jelentést kiváltó állapot kategorizálására. Például egy "LocalWatchdog" SourceId azonosítóval rendelkező jelentéskészítő figyelheti a csomóponton rendelkezésre álló lemez állapotát, így jelentheti az "AvailableDisk" tulajdonságot az adott csomóponton. Ugyanaz a jelentéskészítő figyelheti a csomópont-kapcsolatot, így jelentheti a "Kapcsolat" tulajdonságot ugyanazon a csomóponton. Az állapottárolóban ezeket a jelentéseket külön állapoteseményekként kezeli a megadott csomópont. A SourceId-dal együtt a tulajdonság egyedileg azonosítja az állapotinformációkat. |
| --állapot[Kötelező] | A lehetséges\: értékek a következők: "Érvénytelen", "Ok", "Figyelmeztetés", "Hiba", "Ismeretlen". |
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

## <a name="sfctl-application-type"></a>sfctl alkalmazás típusa
A Service Fabric-fürtben lévő alkalmazástípusok listáját, amely pontosan a megadott névnek felel meg.

A service fabric-fürtben kiépített vagy kiépített alkalmazástípusokra vonatkozó információkat adja vissza. Ezek az eredmények olyan alkalmazástípusok, amelyek neve pontosan megegyezik a paraméterként megadott altípussal, és amelyek megfelelnek a megadott lekérdezési paramétereknek. Az alkalmazástípus minden, az alkalmazástípus nevének megfelelő verziót ad vissza a rendszer, és minden verzió egy alkalmazástípusként kerül visszaadásra. A válasz tartalmazza az alkalmazástípus nevét, verzióját, állapotát és egyéb részleteit. Ez egy lapozható lekérdezés, ami azt jelenti, hogy ha nem az összes alkalmazástípus fér el egy oldalon, egy oldal eredmények ad vissza, valamint egy folytatási jogkivonatot, amely a következő oldal lekéréséhez használható. Ha például 10 alkalmazástípus van, de egy oldal csak az első három alkalmazástípushoz illik, vagy ha a maximális eredmény 3, akkor három lesz visszaadva. A többi eredmény eléréséhez a következő lapok lekérése a visszaadott folytatási jogkivonat használatával a következő lekérdezésben. Ha nincsenek további oldalak, a rendszer üres folytatási jogkivonatot ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-típus-név [Kötelező] | Az alkalmazástípus neve. |
| --alkalmazás-típus-verzió | Az alkalmazástípus verziója. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --kizárás-alkalmazás-paraméterek | Az a jelző, amely meghatározza, hogy az alkalmazás paraméterei ki lesznek-e zárva az eredményből. |
| --max-eredmények | A lapozható lekérdezések részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg a visszaadott eredmények számának felső határát. A visszaadott eredmények kisebbek lehetnek, mint a megadott maximális eredmények, ha nem férnek el az üzenetben a konfigurációban megadott maximális üzenetméret-korlátozások nak megfelelően. Ha ez a paraméter nulla vagy nincs megadva, a lapozható lekérdezés a lehető legtöbb eredményt tartalmazza, amely elfér a visszatérési üzenetben. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-type-list"></a>sfctl alkalmazástípus-lista
Letöltőa Service Fabric-fürt alkalmazástípusainak listája.

A service fabric-fürtben kiépített vagy kiépített alkalmazástípusokra vonatkozó információkat adja vissza. Az alkalmazástípus minden verziója egy alkalmazástípusként kerül visszaadásra. A válasz tartalmazza az alkalmazástípus nevét, verzióját, állapotát és egyéb részleteit. Ez egy lapozható lekérdezés, ami azt jelenti, hogy ha nem az összes alkalmazástípus fér el egy oldalon, egy oldal eredmények ad vissza, valamint egy folytatási jogkivonatot, amely a következő oldal lekéréséhez használható. Ha például 10 alkalmazástípus van, de egy oldal csak az első három alkalmazástípushoz illik, vagy ha a maximális eredmény 3, akkor három lesz visszaadva. A többi eredmény eléréséhez a következő lapok lekérése a visszaadott folytatási jogkivonat használatával a következő lekérdezésben. Ha nincsenek további oldalak, a rendszer üres folytatási jogkivonatot ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-típus-meghatározás-fajta-szűrő | Az ApplicationTypeDefinitionKind szűrésére szolgál, amely a Service Fabric alkalmazástípus definiálására használt mechanizmus.  <br> - Alapértelmezett - Alapértelmezett érték, amely ugyanazt a funkciót látja el, mint az "Összes" kiválasztása. Az érték 0.  <br> - All - Szűrő, amely egyezteti a bemeneti bármely ApplicationTypeDefinitionKind értékkel. Az érték 65535.  <br> - ServiceFabricApplicationPackage - Szűrő, amely megfelel a bemeneti ApplicationTypeTypeKind value ServiceFabricApplicationPackage. Az érték 1.  <br> - Compose - Szűrő, amely megfelel a bemeneti ApplicationTypeDefinitionKind érték Compose. Az érték 2. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --kizárás-alkalmazás-paraméterek | Az a jelző, amely meghatározza, hogy az alkalmazás paraméterei ki lesznek-e zárva az eredményből. |
| --max-eredmények | A lapozható lekérdezések részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg a visszaadott eredmények számának felső határát. A visszaadott eredmények kisebbek lehetnek, mint a megadott maximális eredmények, ha nem férnek el az üzenetben a konfigurációban megadott maximális üzenetméret-korlátozások nak megfelelően. Ha ez a paraméter nulla vagy nincs megadva, a lapozható lekérdezés a lehető legtöbb eredményt tartalmazza, amely elfér a visszatérési üzenetben. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-unprovision"></a>sfctl alkalmazás kiépítés
Eltávolítja vagy törli a Service Fabric-alkalmazástípust a fürtből.

Ez a művelet csak akkor hajtható végre, ha az alkalmazástípus összes alkalmazáspéldányát törölték. Ha az alkalmazástípus nincs regisztrálva, nem hozhatók létre új alkalmazáspéldányok ehhez az alkalmazástípushoz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazás-típus-név [Kötelező] | Az alkalmazástípus neve. |
| --alkalmazás-típus-verzió [Kötelező] | Az alkalmazástípus nak az alkalmazásjegyzékben meghatározott verziója. |
| --async-paraméter | A megvilágító jelző, amely azt jelzi, hogy a nem kell-e aszinkron módon bekövetkeznie. Ha a készlet igaz, a nem létesítési művelet visszaadja, ha a rendszer elfogadja a kérelmet, és a kiépítetlen művelet időkorlát nélkül folytatódik. Az alapértelmezett érték a hamis. Azonban azt javasoljuk, hogy a kiépített nagy alkalmazáscsomagok true értékre állítása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade"></a>sfctl alkalmazás frissítése
Elindítja egy alkalmazás frissítését a Service Fabric-fürtben.

Ellenőrzi a megadott alkalmazásfrissítési paramétereket, és elindítja az alkalmazás frissítését, ha a paraméterek érvényesek. Vegye figyelembe, hogy a frissítés leírása felülírja a meglévő alkalmazásleírást. Ez azt jelenti, hogy ha a paraméterek nincsenek megadva, az alkalmazások meglévő paraméterei felülíródnak az üres paraméterek listájával. Ez azt eredményezné, hogy az alkalmazás az alkalmazásjegyzék paramétereinek alapértelmezett értékét használja.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --alkalmazás-verzió [Kötelező] | A célalkalmazás típusverziója (az alkalmazásjegyzékben található) az alkalmazás frissítéséhez. |
| --paraméterek [Kötelező] | Az alkalmazás frissítésekor alkalmazandó alkalmazásparaméter-felülbírálások JSON-kódolású listája. |
| --default-service-health-policy | JSON kódolt specifikációja az állapotházirend alapértelmezés szerint egy szolgáltatástípus állapotának kiértékeléséhez használt. |
| --hiba-művelet | A figyelt frissítés figyelési szabályzatvagy az állapotházirend-sértések esetén végrehajtandó művelet. |
| --erő-újraindítás | A frissítés során a folyamatokat akkor is erőteljesen újrakell indítani, ha a kódverzió nem változott. |
| --health-check-retry-idő-időállás | Az állapot-ellenőrzések végrehajtása között az állapotellenőrzések végrehajtása között, ha az alkalmazás vagy a fürt nem kifogástalan.  Alapértelmezett\: PT0H10m0s. |
| --health-check-stable-időtartam | Az az idő, amerre az alkalmazásnak vagy fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományra lépne.  Alapértelmezett\: PT0H2M0s. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --health-check-wait-időtartam | A frissítési tartomány befejezése után a várakozási idő időtartama az állapotellenőrzési folyamat megkezdése előtt.  Alapértelmezett\: 0. |
| --max-egészségtelen-apps | A nem megfelelő állapotú telepített alkalmazások maximálisan engedélyezett százaléka. 0 és 100 közötti számként jelölve. |
| --mód | A működés közbeni frissítés során az állapot figyelésére használt mód.  Alapértelmezett\: UnmonitoredAuto. |
| --replika-set-check-időout | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megakadályozására rendelkezésre álló idő letiltására rendelkezésre álló idő, ha nem várt problémák merülnek fel. Másodpercben mérve. |
| --szolgáltatás-egészségügyi politika | JSON kódolású leképezés szolgáltatástípus-állapotházirenddel szolgáltatástípus neve szerint. A térkép üres, legyen az alapértelmezett. |
| --idő-out -t | Alapértelmezett\: 60. |
| --upgrade-domain-időout | Az egyes frissítési tartományoknak a FailureAction végrehajtása előtt be kell fejezniük az időt.  Alapértelmezett\: P10675199DT02H48M05.4775807s. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --upgrade-időeltetés | A teljes frissítés befejezéséhez szükséges idő a FailureAction végrehajtása előtt.  Alapértelmezett\: P10675199DT02H48M05.4775807s. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --figyelmeztetés hibaként | Azt jelzi, hogy a figyelmeztetések kezelése ugyanolyan súlyos, mint a hibák. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl alkalmazás frissítés-folytatás
Folytatja egy alkalmazás frissítését a Service Fabric-fürtben.

Folytatja a nem figyelt manuális Service Fabric alkalmazás frissítése. A Service Fabric egyszerre csak egy frissítési tartományt frissít. Nem figyelt manuális frissítések esetén, miután a Service Fabric befejezi a frissítési tartományt, megvárja, hogy hívja meg ezt az API-t, mielőtt továbblépne a következő frissítési tartományba.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --upgrade-domain-name [Kötelező] | Annak a frissítési tartománynak a neve, amelyben a frissítés folytatásához. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl alkalmazás frissítés-visszaállítás
Elindítja a Service Fabric-fürtben lévő alkalmazás folyamatban lévő frissítésének visszaállítása.

Elindítja az aktuális alkalmazásfrissítés visszaállítását az előző verzióra. Ez az API csak az aktuális folyamatban lévő frissítés visszaállítására használható, amely az új verzióra van előregörgetve. Ha az alkalmazás frissítése jelenleg nem történik meg, használja a StartApplicationUpgrade API-t a kívánt verzióra való frissítéshez, beleértve a korábbi verzióra való visszaállítást is.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade-status"></a>sfctl alkalmazás frissítési állapota
Az alkalmazáson végrehajtott legújabb frissítés részleteinek bekerülése.

A legutóbbi alkalmazásfrissítés állapotára vonatkozó információkat, valamint az alkalmazás állapotával kapcsolatos problémák hibakeresését segítő részleteket ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upload"></a>sfctl alkalmazás feltöltése
A Service Fabric-alkalmazáscsomag másolása a lemezképtárolóba.

Tetszés szerint megjelenítheti a feltöltési folyamatot a csomag minden egyes fájlja esetében. A feltöltési `stderr`folyamat a rendszer nek kerül elküldésre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --elérési út [Kötelező] | A helyi alkalmazáscsomag elérési útja. |
| --tömörítés | Csak a Service Fabric alkalmazáscsomagok alkalmazható. Hozzon létre egy új mappát, amely tartalmazza a tömörített alkalmazáscsomagot az alapértelmezett helyre vagy a tömörített hely paraméter által megadott helyre, majd töltse fel az újonnan létrehozott mappát. <br><br> Ha már van tömörített fájl, amelyet az sfctl generál, a jelző beállítása esetén a program felülírja azt. Hiba jelenik meg, ha a könyvtár nem alkalmazáscsomag. Ha már tömörített alkalmazáscsomagról van szó, a mappa a lap nak másolódik. Alapértelmezés szerint az újonnan létrehozott tömörített alkalmazáscsomag sikeres feltöltés után törlődik. Ha a feltöltés nem sikerül, kérjük, szükség szerint manuálisan tisztítsa meg a tömörített csomagot. A törlés nem távolítja el azokat az üres könyvtárakat, amelyek akkor hozhatók létre, ha a tömörített hely paraméter nem létező könyvtárakra hivatkozik. |
| --tömörített hely | A tömörített alkalmazáscsomag elhelyezésének helye. <br><br> Ha nincs megadva hely, a tömörített csomag egy újonnan létrehozott mappa alá kerül, amelynek neve sfctl_compressed_temp az elérési út argumentumban megadott szülőkönyvtár alatt. Ha például az elérési út\:argumentum értéke C /FolderA/AppPkg, akkor\:a tömörített csomag hozzáadódik a C /FolderA/sfctl_compressed_temp/AppPkg-hez. |
| --imagestore-karakterlánc | Az alkalmazáscsomag feltöltéséhez célképtároló.  Alapértelmezett\: \:háló ImageStore. <br><br> A fájl helyre való feltöltéséhez indítsa\:el ezt a paramétert a "file" (fájl) segítségével. Ellenkező esetben az értéknek a lemezképtároló kapcsolati karakterláncának kell lennie, például az alapértelmezett értéknek. |
| --keep-tömörített | Azt jelzi, hogy a generált tömörített csomagot meg kell-e tartani a sikeres feltöltés befejezésekor. <br><br> Ha nincs beállítva, akkor a sikeres befejezéskor a tömörített alkalmazáscsomagok törlődnek. Ha a feltöltés nem sikerült, akkor az alkalmazáscsomag mindig a kimeneti könyvtárban marad újra feltöltésre. |
| --show-haladás | Nagy csomagok fájlfeltöltési folyamatának megjelenítése. |
| --idő-out -t | A teljes időkitöltés másodpercben. A feltöltés sikertelen lesz, és a feltöltési időmegadási idő lejárta után hibaüzenetet ad vissza. Ez az időmeghosszabbítás a teljes alkalmazáscsomagra vonatkozik, és az egyes fájlidő-megmaradások megegyeznek a hátralévő időmegmaradási időtartammal. Az időtúlfoglalás nem tartalmazza az alkalmazáscsomag tömörítéséhez szükséges időt.  Alapértelmezett\: 300. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.
