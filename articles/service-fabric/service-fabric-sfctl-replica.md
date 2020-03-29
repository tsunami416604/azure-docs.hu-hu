---
title: Az Azure Service Fabric CLI- sfctl replika
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A replikák kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f6ad0b4c08ac8d710340fe654a068d0a3804e58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905803"
---
# <a name="sfctl-replica"></a>sfctl-replika
A szolgáltatáspartíciókhoz tartozó replikák kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Telepített | A Service Fabric-csomóponton üzembe helyezett replika részleteinek bemásolása. |
| telepített lista | A Service Fabric-csomóponton üzembe helyezett replikák listájának letöltője. |
| Egészségügyi | Egy Service Fabric állapotalapú szolgáltatás replika vagy állapotmentes szolgáltatáspéldány állapotát leáll. |
| Info | A Service Fabric-partíció kópiájának adatait. |
| lista | A Service Fabric szolgáltatáspartíció replikáival kapcsolatos információk bemásolása. |
| Eltávolít | Eltávolítja a csomóponton futó szolgáltatásreplikát. |
| jelentés-egészségügy | Állapotjelentést küld a Service Fabric replika. |
| restart | Újraindítja egy csomóponton futó, megőrzött szolgáltatás szolgáltatásreplikáját. |

## <a name="sfctl-replica-deployed"></a>sfctl replika telepítve
A Service Fabric-csomóponton üzembe helyezett replika részleteinek bemásolása.

A Service Fabric-csomóponton üzembe helyezett replika részleteinek leése. Az információ tartalmazza a szolgáltatás típusa, szolgáltatás neve, aktuális szolgáltatás művelet, aktuális szolgáltatás művelet kezdési dátum időpontja, partíció azonosító, replika/példány azonosító, jelentett terhelés, és egyéb információk.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A replika azonosítója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-deployed-list"></a>sfctl replika telepített lista
A Service Fabric-csomóponton üzembe helyezett replikák listájának letöltője.

Letöltőa Service Fabric-csomóponton üzembe helyezett replikák adatait tartalmazó lista letöltődése. Az adatok közé tartozik a partícióazonosító, a replika azonosítója, a replika állapota, a szolgáltatás neve, a szolgáltatástípus neve és egyéb információk. A PartitionId vagy serviceManifestName lekérdezési paraméterek használatával adja vissza a megadott értékeknek megfelelő telepített replikák adatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --application-id [Kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a\:"fabric" URI-séma nélkül. A 6.0-s verziótól kezdődően a hierarchikus neveket a "\~" karakter nel dekvanálja. Ha például az alkalmazás neve\:"fabric /myapp/app1", az alkalmazás\~identitása a korábbi verziókban "myapp app1" lesz, a korábbi verziókban pedig "myapp/app1". |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --partíció-azonosító | A partíció identitása. |
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

## <a name="sfctl-replica-health"></a>sfctl replika állapota
Egy Service Fabric állapotalapú szolgáltatás replika vagy állapotmentes szolgáltatáspéldány állapotát leáll.

A Service Fabric-replika állapotát lemásolja. Az EventsHealthStateFilter használatával szűrheti a kópián jelentett állapotesemények gyűjteményét az állapot alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A replika azonosítója. |
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

## <a name="sfctl-replica-info"></a>sfctl replika adatai
A Service Fabric-partíció kópiájának adatait.

A válasz tartalmazza az azonosítót, a szerepkört, az állapotot, az állapotot, a csomópont nevét, az uptime-ot és a replika egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A replika azonosítója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-list"></a>sfctl replikalista
A Service Fabric szolgáltatáspartíció replikáival kapcsolatos információk bemásolása.

A GetReplicas végpont a megadott partíció replikáival kapcsolatos információkat ad vissza. A válasz tartalmazza az azonosítót, a szerepkört, az állapotot, az állapotot, a csomópont nevét, az uptime-ot és a replika egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partition-id [Kötelező] | A partíció identitása. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-remove"></a>sfctl replika eltávolítása
Eltávolítja a csomóponton futó szolgáltatásreplikát.

Ez az API szimulálja a Service Fabric replika hiba eltávolításával egy replika egy Service Fabric-fürtből. Az eltávolítás bezárja a replika, átvált a replika a szerepkör Nincs, majd eltávolítja az összes állapotinformáció a replika a fürtből. Ez az API teszteli a replika állapotának eltávolításának elérési útját, és szimulálja a jelentéshiba állandó elérési útját az ügyfél API-kon keresztül. Figyelmeztetés – Az API-használat során nem végeznek biztonsági ellenőrzéseket. Az API helytelen használata adatvesztéshez vezethet az állapotalapú szolgáltatások esetében. Emellett a forceRemove jelző hatással van az ugyanabban a folyamatban tárolt összes többi replikára.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A replika azonosítója. |
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

## <a name="sfctl-replica-report-health"></a>sfctl replika jelentés-állapot
Állapotjelentést küld a Service Fabric replika.

A megadott Service Fabric-replika állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentés forrására és a jelentett tulajdonságra vonatkozó információkat. A jelentés egy Service Fabric átjáró replika, amely továbbítja a health store. A jelentést az átjáró elfogadhatja, de az egészségügyi tároló további ellenőrzés után elutasítja. Például a health store elutasíthatja a jelentést, mert egy érvénytelen paraméter, mint például egy elavult sorszám. Ha meg szeretné tudni, hogy a jelentés alkalmazva lett-e a health store-ban, futtassa a replika állapotának befizetést, és ellenőrizze, hogy a jelentés megjelenik-e a HealthEvents szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --health-property [Kötelező] | Az egészségügyi információk tulajdonsága. <br><br> Egy entitás különböző tulajdonságokállapot-jelentésekkel rendelkezhet. A tulajdonság egy karakterlánc, és nem egy rögzített felsorolás, amely lehetővé teszi az előjelentés rugalmasságát a jelentést kiváltó állapot kategorizálására. Például egy "LocalWatchdog" SourceId azonosítóval rendelkező jelentéskészítő figyelheti a csomóponton rendelkezésre álló lemez állapotát, így jelentheti az "AvailableDisk" tulajdonságot az adott csomóponton. Ugyanaz a jelentéskészítő figyelheti a csomópont-kapcsolatot, így jelentheti a "Kapcsolat" tulajdonságot ugyanazon a csomóponton. Az állapottárolóban ezeket a jelentéseket külön állapoteseményekként kezeli a megadott csomópont. A SourceId-dal együtt a tulajdonság egyedileg azonosítja az állapotinformációkat. |
| --állapot[Kötelező] | A lehetséges\: értékek a következők: "Érvénytelen", "Ok", "Figyelmeztetés", "Hiba", "Ismeretlen". |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A partíció identitása. |
| --source-id [Kötelező] | A forrásnév, amely azonosítja az ügyfél/figyelő/rendszerösszetevő, amely létrehozta az állapotadatokat. |
| --leírás | Az egészségügyi információk leírása. <br><br> Szabad szöveget jelöl, amely a jelentéssel kapcsolatos emberi olvasható információk hozzáadására szolgál. A leírás maximális karakterlánchossza 4096 karakter. Ha a megadott karakterlánc hosszabb, a program automatikusan csonkolja. Csonkoláskor a leírás utolsó karakterei a "[Csonka]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi a felhasználóknak, hogy csonkolás történt. Ne feledje, hogy csonkoláskor a leírás kevesebb mint 4096 karaktert tartalmaz az eredeti karakterláncból. |
| --azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e küldeni. <br><br> Egy állapotjelentés egy Service Fabric átjáró alkalmazás, amely továbbítja a health Store. Ha az Immediate értéke igaz, a rendszer azonnal elküldi a jelentést a HTTP-átjáróból a health Store-ba, függetlenül attól, hogy a HTTP átjáróalkalmazás által használt háló-ügyfél-beállításokat használja.If Immediate is set to true, the report is sent immediately from HTTP Gateway to the health store, regardless of the fabric client settings that the HTTP Gateway Application is using. Ez akkor hasznos, ha a kritikus jelentéseket a lehető leghamarabb el kell küldeni. Az időzítéstől és az egyéb feltételektől függően a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró be van zárva, vagy ha az üzenet nem éri el az átjárót. Ha az Immediate értéke hamis, a rendszer a http-átjáró ból származó állapotügyfél-beállítások alapján küldi el a jelentést. Ezért a rendszer a HealthReportSendInterval konfigurációnak megfelelően lesz kötegelve. Ez az ajánlott beállítás, mert lehetővé teszi, hogy az állapotjelző ügyfél optimalizálja az állapotjelentési üzeneteket az állapottárolónak, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a jelentések nem kerülnek azonnal elküldésre. |
| --remove-when-expired | Érték, amely azt jelzi, hogy a jelentés törlődik-e az állapottárolóból, amikor lejár. <br><br> Ha értéke igaz, a jelentés törlődik a health Store lejárta után. Ha hamis ra van állítva, a jelentés lejártakor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeres időközönként jelentést, meg kell állítani a RemoveWhenExpired false (alapértelmezett). Ily módon a jelentéskészítő problémák (pl. holtpont), és nem tud jelentést, az entitás kiértékelése hiba, amikor az állapotjelentés lejár. Ez az entitást hibaállapotként jelzi. |
| --szekvenciaszám | Az állapotjelentés sorozatszáma numerikus karakterláncként. <br><br> A jelentéssorszámot az állapottároló az elavult jelentések észlelésére használja. Ha nincs megadva, a jelentés hozzáadásakor az állapotügyfél automatikusan létrehoz egy sorszámot. |
| --szolgáltatás-fajta | Az a szolgáltatásreplika (állapot nélküli vagy állapotalapú), amelynek állapotjelzője az állapot. A következőkben\: a "Stateless", "Stateful" lehetséges értékek et taszák.  Alapértelmezett\: állapotalapú. |
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

## <a name="sfctl-replica-restart"></a>sfctl replika újraindítása
Újraindítja egy csomóponton futó, megőrzött szolgáltatás szolgáltatásreplikáját.

Újraindítja egy csomóponton futó, megőrzött szolgáltatás szolgáltatásreplikáját. Figyelmeztetés – Az API-használat során nem végeznek biztonsági ellenőrzéseket. Az API helytelen használata az állapotalapú szolgáltatások rendelkezésre állási veszteségéhez vezethet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontneve [Kötelező] | A csomópont neve. |
| --partition-id [Kötelező] | A partíció identitása. |
| --replika-id [Kötelező] | A replika azonosítója. |
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
