---
title: Az Azure Service Fabric parancssori felület - sfctl-csomópont |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-csomópont parancsokat.
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
ms.openlocfilehash: e68a258c8e323b62f85219648c011ce1e661ee0d
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494538"
---
# <a name="sfctl-node"></a>sfctl-csomópont
A csomópontok, amely egy fürt kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| letiltása | Egy Service Fabric-fürt csomópontja megadott inaktiválási készítésében inaktiválása. |
| engedélyezése | Aktiválja egy Service Fabric-fürt csomópontja, amely jelenleg inaktivált állapotban van. |
| állapot | Lekérdezi egy Service Fabric-csomópont állapotát. |
| információ | Egy adott csomópont a adatainak beolvasása a Service Fabric-fürtben. |
| lista | A Service Fabric-fürtben található csomópontok listájának beolvasása. |
| betöltés | A Service Fabric-csomópont a terhelés információkat kér le. |
| állapot eltávolítása | Értesítést küld a Service Fabric, hogy a csomópont a megőrzött állapot véglegesen eltávolították vagy elveszett. |
| report-health | A Service Fabric-csomópont állapota jelentést küld. |
| restart | A Service Fabric-fürtcsomópont újraindítása. |
| Váltás | Elindítja vagy leállítja egy fürt csomópontja. |
| Váltás – állapot | Lekérdezi egy StartNodeTransition első lépéseiben műveletnek az előrehaladását. |

## <a name="sfctl-node-disable"></a>sfctl-csomópont letiltása
Egy Service Fabric-fürt csomópontja megadott inaktiválási készítésében inaktiválása.

Egy Service Fabric-fürt csomópontja megadott inaktiválási készítésében inaktiválása. Után az Inaktiválás folyamatban van, az inaktiválást szándékot is lehet növelni, de nem csökkent (például egy csomópont szüneteltetése készítésében inaktivált lehet további inaktív, újraindítás, de nem fordítva. Csomópontok használatával az aktiválás egy csomópont művelet azokat az inaktiválása után bármikor előfordulhat, hogy újraaktiválását. Ha az inaktiválást nem fejeződött be, Ez megszakítja az inaktiválást. Egy csomópont leáll, és ismét közben inaktiválva továbbra is kell aktiválni kell, mielőtt services ezen a csomóponton kerül.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --inaktiválási-leképezés | Ismerteti a leképezés vagy a csomópont inaktiválása okát. A lehetséges értékek követi. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-enable"></a>sfctl-csomópont engedélyezése
Aktiválja egy Service Fabric-fürt csomópontja, amely jelenleg inaktivált állapotban van.

Aktiválja a egy Service Fabric-fürt csomópontja, amely jelenleg inaktivált állapotban van. Miután aktiválta, a csomópont újra lesz működőképes céljának új replikáit helyezi el, és a csomóponton fennmaradó inaktív replikákat fog újraaktiválását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-health"></a>sfctl-csomópont állapota
Lekérdezi egy Service Fabric-csomópont állapotát.

Lekérdezi egy Service Fabric-csomópont állapotát. EventsHealthStateFilter a gyűjteménye, a csomópont állapota alapján jelentett hálózatállapot-események szűréséhez használja. Ha a csomópontot, név szerint kell megadni a a health Store adatbázisban nem létezik, ez hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --események-állapot – állapot-szűrő | A gyűjtemény állapotesemény – a visszaadott objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény segítségével kiértékelése összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, a bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd az OK (2), és figyelmeztetés (4) HealthState értékét az események vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-info"></a>sfctl-csomópont adatai
Egy adott csomópont a adatainak beolvasása a Service Fabric-fürtben.

A válasz tartalmazza a neve, állapota, azonosítója, egészségügyi, hasznos üzemidőt és más a csomópont részletei.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-list"></a>sfctl-csomópont listája
A Service Fabric-fürtben található csomópontok listájának beolvasása.

A válasz tartalmazza a neve, állapota, azonosítója, egészségügyi, hasznos üzemidőt és egyéb részleteit a csomópontok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --node-status-filter | A csomópontok a NodeStatus szűrését teszi lehetővé. Csak azoknak a csomópontoknak, hogy a megadott szűrőérték megfelelő lesz visszaadva. A szűrő értéke a következők egyike lehet.  Alapértelmezett\: alapértelmezett. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-load"></a>sfctl-csomópont betöltése
A Service Fabric-csomópont a terhelés információkat kér le.

A Service Fabric csomópont esetében a betöltés vagy meghatározott kapacitásának rendelkező mérőszámok a terhelés adatait kérdezi le.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-remove-state"></a>sfctl-csomópont remove-állapota
Értesítést küld a Service Fabric, hogy a csomópont a megőrzött állapot véglegesen eltávolították vagy elveszett.

Ez azt jelenti, hogy azt ne legyen az adott csomópont a megőrzött állapot helyreállításához. Ez általában akkor történik, a merevlemez már megtörténhetett tiszta, vagy ha összeomlik, egy merevlemezt. A csomópont rendelkezik, a művelet sikeres legyen. Ez a művelet lehetővé teszi, hogy a Service Fabric tudja, hogy a replikákat a csomóponton már nem létezik, és a Service Fabric le kell állnia, e replikák térjen vissza vár. Ne futtassa ezt a parancsmagot, ha a csomópont állapotát nem el lett távolítva, és a csomópont is kapja vissza meg állapotában nem sérültek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-report-health"></a>sfctl csomópont állapotjelentés
A Service Fabric-csomópont állapota jelentést küld.

A jelentés a megadott Service Fabric-csomópont állapotát. A jelentésnek tartalmaznia kell a forrás az egészségügyi jelentés és, amelyen jelentett tulajdonság vonatkozó információk. A jelentést küld egy Service Fabric átjárócsomópontnak, amely továbbítja a health Store adatbázisban. A jelentés előfordulhat, hogy fogadja el az átjárót, azonban elutasította a health Store adatbázisban a további ellenőrzést. A health Store adatbázisban például elutasíthatják a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Szeretné-e a jelentés a a health Store adatbázisban alkalmazta-e, ellenőrizze a, hogy a jelentés a HealthEvents szakasz jelenik meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [kötelező] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterláncot és a nem rögzített enumerálása, hogy az Eszközállapot-feltételt, amely elindítja a jelentés kategorizálása riporter rugalmasan tulajdonság. Például egy riporter a SourceId "LocalWatchdog" figyelheti az állapotot, a rendelkezésre álló lemez egy csomóponton, ezen a csomóponton, jelentést "AvailableDisk" tulajdonság. A ugyanolyan jelentéskészítői figyelheti a csomópont-kapcsolatban –, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonságot. A health Store adatbázisban ezek a jelentések az adott csomópont számára külön állapotesemények kell kezelni. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [kötelező] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Figyelmeztetés", "Error", "Ismeretlen". |
| --csomópontnév [kötelező] | Csomópont nevét jelenti. |
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

## <a name="sfctl-node-restart"></a>sfctl-csomópont újraindítása
A Service Fabric-fürtcsomópont újraindítása.

Újraindítja a egy Service Fabric-fürt csomópontja, amely már el van indítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --create-fabric-dump | Adja meg az igaz értékre a fabric-csomópont folyamat egy memóriakép létrehozása. Ez a kis-és nagybetűket.  Alapértelmezett\: false (hamis). |
| --node-instance-id | A célcsomópont példány azonosítója. Ha példány azonosítója van megadva, a csomópontok újraindításakor csak akkor, ha a kulcs megegyezik-e az aktuális instance uzlu. Alapértelmezett érték "0" megfelel minden szolgáltatópéldány-azonosítót. A Példányazonosító get csomópont lekérdezés használatával szerezhető be.  Alapértelmezett\: 0. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-transition"></a>sfctl-csomópont Váltás
Elindítja vagy leállítja egy fürt csomópontja.

Elindítja vagy leállítja egy fürt csomópontja.  Egy fürt csomópontja egy olyan folyamat, nem pedig maga az operációs rendszer példány.  Indítja el a csomópontot, adja át a "Start" NodeTransitionType paraméterhez. Csomópont leállítása, át kell adnia a "Stop" NodeTransitionType paraméterhez. Ez az API az API-t adja vissza, a csomópont lehet, hogy nem végzett transitioning még a művelet - kezdődik. A műveletnek az előrehaladását beolvasni az azonos Műveletazonosítóval rendelkező GetNodeTransitionProgress hívjuk.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – csomópont-példány-id [kötelező] | A célcsomópont csomópont példány azonosítója. Ez lehet meghatározni GetNodeInfo API-n keresztül. |
| --csomópontnév [kötelező] | A csomópont neve. |
| – csomópont-Váltás – típusa [kötelező] | Hajtsa végre az áttérés típusát jelzi.  NodeTransitionType.Start indul el egy leállított csomópontot. NodeTransitionType.Stop működik, egy csomópont leáll. |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --stop-időtartam-az-másodperc [kötelező] | Az időtartam másodpercben, hogy a csomópont leállt.  A minimális értéke 600, a maximális 14400.  Ez az idő lejárta után a csomópont automatikusan visszatér. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-node-transition-status"></a>sfctl csomópont váltás – állapot
Lekérdezi egy StartNodeTransition első lépéseiben műveletnek az előrehaladását.

Lekérdezi a megadott műveletazonosító használata StartNodeTransition lépések egy műveletnek az előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

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