---
title: Azure Service Fabric CLI – sfctl összeállítás | Microsoft Docs
description: A CLI-sfctl összeállítási parancsainak Service Fabric ismerteti.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dab844246d99b0ab80e1e86219c2064c79e74e4f
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035108"
---
# <a name="sfctl-compose"></a>sfctl-összeállítás
Docker-összeállítási alkalmazások létrehozása, törlése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| create | Létrehoz egy Service Fabric összeállítási központi telepítést. |
| list | A Service Fabric-fürtben létrehozott összeállítási központi telepítések listájának beolvasása. |
| eltávolítás | Töröl egy meglévő Service Fabric összeállítási telepítést a fürtből. |
| status | Információ beolvasása egy Service Fabric összeállítási telepítésről. |
| upgrade | Megkezdi az összeállítási központi telepítés frissítését a Service Fabric-fürtön. |
| upgrade-rollback | Elindítja az összeállítási központi telepítés frissítését a Service Fabric-fürtön. |
| upgrade-status | Beolvassa az ezen a Service Fabric-összeállításon végrehajtott legújabb frissítés részleteit. |

## <a name="sfctl-compose-create"></a>sfctl összeállítás létrehozása
Létrehoz egy Service Fabric összeállítási központi telepítést.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – központi telepítés – név [kötelező] | Az üzemelő példány neve. |
| --fájl elérési útja [kötelező] | A cél Docker-összeállítási fájl elérési útja. |
| --encrypted-pass | A tároló beállításjegyzékbeli jelszavának megkérdezése helyett használjon egy már titkosított pass-kifejezést. |
| --a-pass | A jelszó megadását kéri a tároló beállításjegyzékében. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |
| --user | A tároló-beállításjegyzékhez való kapcsolódáshoz használandó Felhasználónév. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-list"></a>sfctl-összeállítási lista
A Service Fabric-fürtben létrehozott összeállítási központi telepítések listájának beolvasása.

Lekérdezi a létrehozott összeállítási központi telepítések állapotát vagy a Service Fabric-fürtben létrehozandó folyamat során. A válasz tartalmazza az összeállítási központi telepítések nevét, állapotát és egyéb részleteit. Ha a központi telepítések listája nem fér el egy oldalon, a rendszer az eredmények egyik oldalát adja vissza, valamint egy folytatási tokent, amely a következő oldal beolvasására használható.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-remove"></a>sfctl-összeállítás eltávolítása
Töröl egy meglévő Service Fabric összeállítási telepítést a fürtből.

Töröl egy meglévő Service Fabric összeállítást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – központi telepítés – név [kötelező] | Az üzemelő példány identitása. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-status"></a>sfctl-összeállítás állapota
Információ beolvasása egy Service Fabric összeállítási telepítésről.

A létrehozott, vagy a Service Fabric-fürtben létrehozott összeállítási telepítés állapotát adja vissza, amelynek a neve megegyezik a paraméterrel megadott névvel. A válasz tartalmazza az üzemelő példány nevét, állapotát és egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – központi telepítés – név [kötelező] | Az üzemelő példány identitása. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-upgrade"></a>sfctl-összeállítás frissítése
Megkezdi az összeállítási központi telepítés frissítését a Service Fabric-fürtön.

Ellenőrzi a megadott frissítési paramétereket, és megkezdi az üzemelő példány frissítését, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – központi telepítés – név [kötelező] | Az üzemelő példány neve. |
| --fájl elérési útja [kötelező] | A cél Docker-összeállítási fájl elérési útja. |
| --default-svc-type-health-map | JSON-kódolású szótár, amely leírja a szolgáltatások állapotának kiértékeléséhez használt állapotfigyelő házirendet. |
| --encrypted-pass | A tároló beállításjegyzékbeli jelszavának megkérdezése helyett használjon egy már titkosított pass-kifejezést. |
| --failure-action | A lehetséges értékek\: a következők lehetnek: "Érvénytelen", "visszaállítás", "Manual". |
| --kényszerített újraindítás | A folyamatokat a rendszer a frissítés során kényszeríti újra, még akkor is, ha a kód verziószáma nem változott. <br><br> A frissítés csak a konfigurációt vagy az adatértéket módosítja. |
| --a-pass | A jelszó megadását kéri a tároló beállításjegyzékében. |
| --health-check-retry | Az állapot-ellenőrzések elvégzésére irányuló kísérletek közötti időtartam, ha az alkalmazás vagy a fürt állapota nem kifogástalan. |
| --állapot-passzolás – stabil | Az az időtartam, ameddig az alkalmazásnak vagy a fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományba kerül. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --health-check-wait | Az a várakozási idő, ameddig a frissítési tartomány befejezése után meg kell várni az állapot-ellenőrzési folyamat megkezdése előtt. |
| --replica-set-check | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megelőzésére szolgáló maximális időtartam, ha váratlan problémák merülnek fel. <br><br> Ha ez az időkorlát lejár, a frissítési tartomány feldolgozása a rendelkezésre állás elvesztésével kapcsolatos problémáktól függetlenül folytatódni fog. Az időtúllépés az egyes frissítési tartományok elején alaphelyzetbe áll. Az érvényes értékek 0 és 42949672925 között vannak. |
| --svc-type-health-map | A különböző típusú szolgáltatások állapotának kiértékeléséhez használt állapotfigyelő házirendeket leíró, JSON-kódolású objektumok listája. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |
| --unhealthy-app | A nem kifogástalan állapotú alkalmazások maximálisan megengedett százaléka a hiba jelentése előtt. <br><br> Ha például engedélyezni szeretné, hogy az alkalmazások 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték azt jelenti, hogy az alkalmazások maximálisan tolerálható hányada sérült, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem kifogástalan állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítjuk ki, hogy a nem kifogástalan állapotú alkalmazások számát a fürtben lévő alkalmazások teljes száma fölé osztja. |
| – frissítés – tartomány – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a FailureAction végrehajtása előtt kell végrehajtani. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --upgrade-Kind | Alapértelmezett\: működés. |
| – frissítési mód | A lehetséges értékek\: a következők lehetnek: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| – frissítés – időtúllépés | Az a időtartam, ameddig a teljes frissítésnek a FailureAction végrehajtása előtt el kell végeznie. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --user | A tároló-beállításjegyzékhez való kapcsolódáshoz használandó Felhasználónév. |
| --warning-as-error | Azt jelzi, hogy a figyelmeztetések a hibákkal azonos súlyossággal vannak-e kezelve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl-összeállítás frissítése – visszaállítás
Elindítja az összeállítási központi telepítés frissítését a Service Fabric-fürtön.

Egy Service Fabric-összeállítási frissítés visszaállítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – központi telepítés – név [kötelező] | Az üzemelő példány identitása. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl-összeállítás frissítése – állapot
Beolvassa az ezen a Service Fabric-összeállításon végrehajtott legújabb frissítés részleteit.

A összeállít telepítési frissítés állapotával kapcsolatos adatokat adja vissza, valamint az alkalmazás állapotával kapcsolatos hibák elhárításának támogatását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – központi telepítés – név [kötelező] | Az üzemelő példány identitása. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.