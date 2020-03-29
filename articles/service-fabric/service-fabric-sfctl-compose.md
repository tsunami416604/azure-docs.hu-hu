---
title: Az Azure Service Fabric CLI- sfctl komponálása
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A Docker-compose alkalmazások parancsainak listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906133"
---
# <a name="sfctl-compose"></a>sfctl-összeállítás
Hozzon létre, töröljön és kezelje a Docker Compose alkalmazásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Létrehoz egy Service Fabric-összeállítási központi telepítést. |
| lista | Letöltő a Service Fabric-fürtben létrehozott írási központi telepítések listájának leése. |
| Eltávolít | Egy meglévő Service Fabric-összeállítás központi telepítésének törlése a fürtből. |
| status | A Service Fabric-írás központi telepítésével kapcsolatos információk beszerzése. |
| Frissítés | Elindítja a komponálási központi telepítés frissítését a Service Fabric-fürtben. |
| frissítés-visszaállítás | Elindítja a service fabric-fürt ben a komponálási központi telepítési frissítés visszaállítását. |
| frissítési állapot | A Service Fabric-összeállítás központi telepítésén végrehajtott legújabb frissítés részleteinek beszerzése. |

## <a name="sfctl-compose-create"></a>sfctl komponálás létrehozása
Létrehoz egy Service Fabric-összeállítási központi telepítést.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --telepítési név [Kötelező] | A központi telepítés neve. |
| --fájlelérési út [Kötelező] | A cél Docker Compose fájl elérési útja. |
| --titkosított-át | Ahelyett, hogy egy tároló rendszerleíró jelszavát kérné, használjon már titkosított jelszót. |
| --van-pass | Kérni fogja a jelszót a tároló rendszerleíró adatbázisához. |
| --idő-out -t | Alapértelmezett\: 60. |
| --user | Felhasználónév a tároló beállításjegyzékéhez való csatlakozáshoz. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-list"></a>sfctl összeállítási lista
Letöltő a Service Fabric-fürtben létrehozott írási központi telepítések listájának leése.

Leálja a létrehozott vagy a Service Fabric-fürtben létrehozott központi telepítések állapotát. A válasz tartalmazza a nevet, az állapotot és a komponálási központi telepítések egyéb részleteit. Ha a központi telepítések listája nem fér el egy lapon, egy oldal eredmények ad vissza, valamint egy folytatási jogkivonatot, amely a következő oldal lekéréséhez használható.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
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

## <a name="sfctl-compose-remove"></a>sfctl komponálás eltávolítás
Egy meglévő Service Fabric-összeállítás központi telepítésének törlése a fürtből.

Egy meglévő Service Fabric-összeállításközponti telepítés törlése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --telepítési név [Kötelező] | A központi telepítés identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-status"></a>sfctl összeállítási állapot
A Service Fabric-írás központi telepítésével kapcsolatos információk beszerzése.

A Service Fabric-fürtben létrehozott vagy létrehozás alatt álló, a paraméterként megadott nak megfelelő állapotú központi telepítés állapotát adja eredményül. A válasz tartalmazza a nevét, állapotát és a központi telepítés egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --telepítési név [Kötelező] | A központi telepítés identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-upgrade"></a>sfctl komponálási frissítés
Elindítja a komponálási központi telepítés frissítését a Service Fabric-fürtben.

Ellenőrzi a megadott frissítési paramétereket, és elindítja a központi telepítés frissítését, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --telepítési név [Kötelező] | A központi telepítés neve. |
| --fájlelérési út [Kötelező] | A cél Docker-fájl elérési útja. |
| --default-svc-type-health-map | JSON kódolású szótár, amely leírja a szolgáltatások állapotának értékeléséhez használt állapotházirendet. |
| --titkosított-át | Ahelyett, hogy egy tároló rendszerleíró jelszavát kérné, használjon már titkosított jelszót. |
| --hiba-művelet | A lehetséges\: értékek a következők: "Érvénytelen", "Visszaállítás", "Kézi". |
| --erő-újraindítás | A folyamatok a frissítés során akkor is erőteljesen újraindulnak, ha a kódverzió nem változott. <br><br> A frissítés csak a konfigurációt vagy az adatokat módosítja. |
| --van-pass | Kérni fogja a jelszót a tároló rendszerleíró adatbázisához. |
| --health-check-retry | Az állapot-ellenőrzések végrehajtása között az állapotellenőrzések végrehajtása között, ha az alkalmazás vagy a fürt nem kifogástalan. |
| --health-check-stable | Az az idő, amerre az alkalmazásnak vagy fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományra lépne. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --health-check-wait | A frissítési tartomány befejezése után a várakozási idő időtartama az állapotellenőrzési folyamat megkezdése előtt. |
| --replika-set-check | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megakadályozására rendelkezésre álló idő letiltására rendelkezésre álló idő, ha nem várt problémák merülnek fel. <br><br> Ha ez az időbevétel lejár, a frissítési tartomány feldolgozása a rendelkezésre állási veszteségi problémáktól függetlenül folytatódik. Az időhosszabbítás minden frissítési tartomány elején alaphelyzetbe áll. Az érvényes értékek 0 és 42949672925 között vannak. |
| --svc-típus-egészségügyi térkép | JSON kódolású objektumok listáját, amelyek leírják a különböző szolgáltatástípusok állapotának kiértékeléséhez használt állapotházirendek. |
| --idő-out -t | Alapértelmezett\: 60. |
| --egészségtelen-app | A nem megfelelő állapotú alkalmazások maximálisan engedélyezett százaléka a hiba jelentésének bejelentése előtt. <br><br> Ha például az alkalmazások 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték az alkalmazások maximális tolerálható százalékát jelöli, amelyek nem megfelelő állapotúak lehetnek, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy elosztja a nem megfelelő állapotú alkalmazások számát a fürtben lévő alkalmazáspéldányok teljes számával. |
| --upgrade-domain-időout | Az egyes frissítési tartományoknak a FailureAction végrehajtása előtt be kell fejezniük az időt. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --upgrade-típusú | Alapértelmezett\: működés közben. |
| --frissítés-mód | A lehetséges\: értékek a következők: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Alapértelmezett\: UnmonitoredAuto. |
| --upgrade-időeltetés | A teljes frissítés befejezéséhez szükséges idő a FailureAction végrehajtása előtt. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --user | Felhasználónév a tároló beállításjegyzékéhez való csatlakozáshoz. |
| --figyelmeztetés hibaként | Azt jelzi, hogy a figyelmeztetések kezelése ugyanolyan súlyos, mint a hibák. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl komponupgrade-rollback
Elindítja a service fabric-fürt ben a komponálási központi telepítési frissítés visszaállítását.

Egy szolgáltatásháló központi telepítési frissítésének visszaállítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --telepítési név [Kötelező] | A központi telepítés identitása. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl komponálásfrissítési állapot
A Service Fabric-összeállítás központi telepítésén végrehajtott legújabb frissítés részleteinek beszerzése.

Az összeállításközponti telepítési frissítés állapotára vonatkozó információkat, valamint az alkalmazás állapotával kapcsolatos problémák hibakeresésének elősegítésére vonatkozó információkat adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --telepítési név [Kötelező] | A központi telepítés identitása. |
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
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.