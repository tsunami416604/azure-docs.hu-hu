---
title: Azure Service Fabric CLI- sfctl csomópont
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A fürtcsomópontok kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905874"
---
# <a name="sfctl-node"></a>sfctl-csomópont
A fürtöt alkotó csomópontok kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| add-configuration-parameter-overrides | Hozzáadja a konfigurációfelülírások listáját a megadott csomóponton. |
| Megbénít | A service fabric fürtcsomópont inaktiválása a megadott inaktiválási szándékkal. |
| Engedélyezi | Olyan Service Fabric-fürtcsomópont aktiválása, amely jelenleg inaktív. |
| get-configuration-felülbírálások | Leveszi a konfigurációfelülírások listáját a megadott csomóponton. |
| Egészségügyi | Egy Service Fabric-csomópont állapotát kapja. |
| Info | A Service Fabric-fürt egy adott csomópontjára vonatkozó információkat kap. |
| lista | Letöltőa Service Fabric-fürt csomópontjainak listája. |
| betöltés | Egy Service Fabric-csomópont terhelési adatait. |
| eltávolítás-konfiguráció-felülbírálások | Eltávolítja a konfigurációfelülírásokat a megadott csomóponton. |
| eltávolítás-állapot | Értesíti a Service Fabric, hogy a megőrzött állapot egy csomóponton véglegesen eltávolították vagy elvesztették. |
| jelentés-egészségügy | Állapotjelentést küld a Service Fabric-csomóponton. |
| restart | Újraindítja a Service Fabric fürtcsomópontját. |
| Átmenet | Egy fürtcsomópont indítása vagy leállítása. |
| átmeneti állapot | A StartNodeTransition használatával megkezdett művelet folyamatának leszámítása. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl csomópont-add-configuration-parameter-overrides
Hozzáadja a konfigurációfelülírások listáját a megadott csomóponton.

Ez az api lehetővé teszi az összes meglévő konfigurációfelülbírálás hozzáadását a megadott csomóponton.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --config-parameter-override-list [Kötelező] | A konfigurációs felülbírálások listájának hozzáadása. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --erő | Konfigurációfelülbíráláskényszerítás kényszerítése a megadott csomópontokon. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-disable"></a>sfctl csomópont letiltása
A service fabric fürtcsomópont inaktiválása a megadott inaktiválási szándékkal.

A service fabric fürtcsomópont inaktiválása a megadott inaktiválási szándékkal. Miután az inaktiválás folyamatban van, az inaktiválási szándék növelhető, de nem csökkenthető (például a szüneteltetési szándékkal inaktivált csomópont az újraindítással tovább inaktiválható, de fordítva nem. A csomópontok az inaktiválásuk után bármikor újraaktiválhatók a csomópont-művelet aktiválása kor. Ha az inaktiválás nem fejeződött be, az megszakítja az inaktiválást. Egy csomópont, amely leáll, és jön vissza, miközben inaktivált továbbra is újra kell aktiválni, mielőtt a szolgáltatások kerülnek az adott csomópontra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --inaktiválás-szándék | A csomópont inaktiválásának szándékát vagy okát ismerteti. A lehetséges értékek a következők. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-enable"></a>sfctl csomópont engedélyezése
Olyan Service Fabric-fürtcsomópont aktiválása, amely jelenleg inaktív.

Aktiválja a Service Fabric fürtcsomópontot, amely jelenleg inaktivált. Az aktiválást követően a csomópont ismét életképes céllá válik az új replikák elhelyezésére, és a csomóponton maradt inaktivált replikák újra aktiválódnak.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl csomópont get-configuration-overrides
Leveszi a konfigurációfelülírások listáját a megadott csomóponton.

Ez az api lehetővé teszi az összes meglévő konfigurációfelülbírálások a megadott csomóponton.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-health"></a>sfctl csomópont állapota
Egy Service Fabric-csomópont állapotát kapja.

Egy Service Fabric-csomópont állapotát kapja. Az EventsHealthStateFilter használatával szűrheti a csomóponton jelentett állapotesemények gyűjteményét az állapot alapján. Ha a név szerint megadott csomópont nem létezik a health store-ban, ez hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
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

## <a name="sfctl-node-info"></a>sfctl csomópont infó
A Service Fabric-fürt egy adott csomópontjára vonatkozó információkat kap.

A válasz tartalmazza a nevét, állapotát, azonosítóját, állapotát, az uptime-ot és a csomópont egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-list"></a>sfctl csomópontlista
Letöltőa Service Fabric-fürt csomópontjainak listája.

A válasz tartalmazza a nevét, állapotát, azonosítóját, állapotát, az uptime-ot és a csomópontok egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --max-eredmények | A lapozható lekérdezések részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg a visszaadott eredmények számának felső határát. A visszaadott eredmények kisebbek lehetnek, mint a megadott maximális eredmények, ha nem férnek el az üzenetben a konfigurációban megadott maximális üzenetméret-korlátozások nak megfelelően. Ha ez a paraméter nulla vagy nincs megadva, a lapozható lekérdezés a lehető legtöbb eredményt tartalmazza, amely elfér a visszatérési üzenetben. |
| --csomópont-állapotszűrő | Lehetővé teszi a csomópontok szűrését a NodeStatus alapján. Csak a megadott szűrőértéknek megfelelő csomópontok at adja vissza a rendszer. A szűrő értéke a következők egyike lehet.  Alapértelmezett\: érték. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-load"></a>sfctl csomópont terhelése
Egy Service Fabric-csomópont terhelési adatait.

Lekéri a Service Fabric-csomópont terhelési adatait az összes olyan metrika, amely a terhelés vagy a kapacitás definiálva van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl csomópont eltávolítás-konfiguráció-felülbírálások
Eltávolítja a konfigurációfelülírásokat a megadott csomóponton.

Ez az api lehetővé teszi az összes meglévő konfigurációfelülbírálás eltávolítását a megadott csomóponton.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-remove-state"></a>sfctl csomópont eltávolítási állapota
Értesíti a Service Fabric, hogy a megőrzött állapot egy csomóponton véglegesen eltávolították vagy elvesztették.

Ez azt jelenti, hogy nem lehet helyreállítani az adott csomópont megőrzött állapotát. Ez általában akkor fordul elő, ha a merevlemezt tisztára törölték, vagy ha a merevlemez összeomlik. A művelet sikeres működéséhez a csomópontnak nem működik. Ez a művelet lehetővé teszi, hogy a Service Fabric tudja, hogy a replikák, hogy a csomópont már nem létezik, és hogy a Service Fabric ne várja meg a replikákat, hogy jöjjön vissza. Ne futtassa ezt a parancsmamot, ha a csomóponton lévő állapot nem lett eltávolítva, és a csomópont sértetlen állapottal újra előjöhet. A Service Fabric 6.5-ös részéről kiindulva, az API-t a magcsomópontok hoz való használatához módosítsa a magcsomópontokat normál (nem mag) csomópontokra, majd hívja meg ezt az API-t a csomópont állapotának eltávolításához. Ha a fürt fut az Azure-ban, miután a magcsomópont leáll, service fabric megpróbálja automatikusan módosítani egy nem magos csomópont. Ehhez győződjön meg arról, hogy az elsődleges csomóponttípus nem magozott csomópontjainak száma nem kevesebb, mint a Lefelé kezdő csomópontok száma. Ha szükséges, adjon hozzá további csomópontokat az elsődleges csomóponttípushoz ennek eléréséhez. Önálló fürt esetén, ha a Down magcsomópont várhatóan nem jön vissza az állapota ép, távolítsa\:el a csomópontot a fürtből, lásd: https //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-report-health"></a>sfctl csomópont jelentés-egészségügy
Állapotjelentést küld a Service Fabric-csomóponton.

A megadott Service Fabric-csomópont állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentés forrására és a jelentett tulajdonságra vonatkozó információkat. A jelentés egy Service Fabric átjáró csomópont, amely továbbítja a health Store. A jelentést az átjáró elfogadhatja, de az egészségügyi tároló további ellenőrzés után elutasítja. Például a health store elutasíthatja a jelentést, mert egy érvénytelen paraméter, mint például egy elavult sorszám. Ha meg szeretné tudni, hogy a jelentés alkalmazva lett-e a health store-ban, ellenőrizze, hogy a jelentés megjelenik-e a HealthEvents szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --health-property [Kötelező] | Az egészségügyi információk tulajdonsága. <br><br> Egy entitás különböző tulajdonságokállapot-jelentésekkel rendelkezhet. A tulajdonság egy karakterlánc, és nem egy rögzített felsorolás, amely lehetővé teszi az előjelentés rugalmasságát a jelentést kiváltó állapot kategorizálására. Például egy "LocalWatchdog" SourceId azonosítóval rendelkező jelentéskészítő figyelheti a csomóponton rendelkezésre álló lemez állapotát, így jelentheti az "AvailableDisk" tulajdonságot az adott csomóponton. Ugyanaz a jelentéskészítő figyelheti a csomópont-kapcsolatot, így jelentheti a "Kapcsolat" tulajdonságot ugyanazon a csomóponton. Az állapottárolóban ezeket a jelentéseket külön állapoteseményekként kezeli a megadott csomópont. A SourceId-dal együtt a tulajdonság egyedileg azonosítja az állapotinformációkat. |
| --állapot[Kötelező] | A lehetséges\: értékek a következők: "Érvénytelen", "Ok", "Figyelmeztetés", "Hiba", "Ismeretlen". |
| --csomópontneve [Kötelező] | A csomópont neve. |
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

## <a name="sfctl-node-restart"></a>sfctl csomópont újraindítása
Újraindítja a Service Fabric fürtcsomópontját.

Újraindítja a Service Fabric fürtcsomópontját, amely már elindult.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --create-fabric-dump | Adja meg a True értéket a szövetcsomópont folyamatának kiírásához. Ez a kis- és nagybetűk et is figyelembe nem egyezik.  Alapértelmezett\: hamis. |
| --csomópont-példány-azonosító | A célcsomópont példányazonosítója. Ha példányazonosító van megadva, a csomópont csak akkor indul újra, ha megegyezik a csomópont aktuális példányával. Az alapértelmezett "0" érték bármely példányazonosítónak megfelel. A példányazonosító beszerezhető a lekérdezi a csomópont lekérdezését.  Alapértelmezett\: 0. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-transition"></a>sfctl csomópont átmenet
Egy fürtcsomópont indítása vagy leállítása.

Egy fürtcsomópont indítása vagy leállítása.  A fürtcsomópont folyamat, nem maga az operációs rendszer példánya.  Egy csomópont indításához adja át a "Start" parancsot a NodeTransitionType paraméterhez. Egy csomópont leállításához adja át a "Stop" paramétert a NodeTransitionType paraméterhez. Ez az API elindítja a műveletet - amikor az API visszaadja a csomópont még nem fejeződött be az átmenet. Hívja meg a GetNodeTransitionProgress-t ugyanazzal a OperationId azonosítóval a művelet előrehaladásának elérése érdekében.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópont-példány-azonosító [Kötelező] | A célcsomópont csomópont-példányazonosítója. Ez a GetNodeInfo API-n keresztül határozható meg. |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --csomópont-átmeneti típus [Kötelező] | A végrehajtandó átmenet típusát jelzi.  A NodeTransitionType.Start egy leállított csomópontot indít el. A NodeTransitionType.Stop leállítja a feladott csomópontot. |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --stop-duration-in-seconds [Kötelező] | A csomópont leállításának időtartama másodpercben.  A minimális érték 600, a maximális értéke 14400.  Ezen idő lejárta után a csomópont automatikusan biztonsági másolatot kap. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-transition-status"></a>sfctl csomópont átmeneti állapota
A StartNodeTransition használatával megkezdett művelet folyamatának leszámítása.

Leveszi a StartNodeTransition-del a megadott OperationId használatával elindított művelet előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

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