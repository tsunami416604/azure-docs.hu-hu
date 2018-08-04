---
title: Az Azure Service Fabric parancssori felület - sfctl-replika |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-replika parancsokat.
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
ms.openlocfilehash: 6c16cd95fce7d3f367f0ded73c3635d8cefea7a0
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493987"
---
# <a name="sfctl-replica"></a>sfctl-replika
A szolgáltatás partícióinak tartozó replikák kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| üzembe helyezett | A Service Fabric csomópont telepítve replika részleteinek beolvasása. |
| üzembe helyezett-list | A Service Fabric-csomópont replikát listájának beolvasása. |
| állapot | A Service Fabric állapotalapú szolgáltatás replika- vagy állapotmentes szolgáltatás állapotának beolvasása. |
| információ | A Service Fabric-partíción replika adatainak beolvasása. |
| lista | A Service Fabric service partíció replika adatainak beolvasása. |
| eltávolítás | Eltávolítja a csomóponton futó service replika. |
| report-health | A Service Fabric-replika health jelentést küld. |
| restart | Megőrzött szolgáltatásként fut egy csomóponton szolgáltatás replikája újraindul. |

## <a name="sfctl-replica-deployed"></a>sfctl-replika üzembe helyezett
A Service Fabric csomópont telepítve replika részleteinek beolvasása.

A replika egy Service Fabric-csomópont telepítve részleteinek beolvasása. Az információk közé tartozik a szolgáltatás típusának, a szolgáltatás nevét, a jelenlegi, jelenlegi indítsa el a dátum idő, partícióazonosító:, replika és példány azonosítója, jelentett terhelés és egyéb információkat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --replikaazonosító [kötelező] | A replika azonosítója. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-replica-deployed-list"></a>üzembe helyezett replika sfctl-list
A Service Fabric-csomópont replikát listájának beolvasása.

A replikát a Service Fabric csomópontjaival kapcsolatos információt tartalmazó lista beolvasása. Partícióazonosító, másodpéldány-azonosító, a replika, a nevét, a szolgáltatást, a nevét, a szolgáltatás típusának és egyéb információkat állapotát tartalmazza. PartitionId vagy ServiceManifestName lekérdezési paraméterek segítségével a megfelelő paraméterek megadott értékek telepített replikák kapcsolatos információkat ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában a nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió kezdve hierarchikus nevek vannak tagolva, az a "\~" karaktert. Például, ha az alkalmazás neve "fabric\:/myapp/app1", az identitása lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [kötelező] | A csomópont neve. |
| --partíció-azonosító | A partíció identitását. |
| --szolgáltatás jegyzékfájl neve | A szolgáltatásjegyzék-alkalmazástípus a Service Fabric-fürt részeként regisztrált neve. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-replica-health"></a>sfctl-replika állapota
A Service Fabric állapotalapú szolgáltatás replika- vagy állapotmentes szolgáltatás állapotának beolvasása.

Service Fabric replika állapotának beolvasása. EventsHealthStateFilter a gyűjteménye, a replika állapota alapján jelentett hálózatállapot-események szűréséhez használja.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --replikaazonosító [kötelező] | A replika azonosítója. |
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

## <a name="sfctl-replica-info"></a>sfctl-replika adatai
A Service Fabric-partíción replika adatainak beolvasása.

A válasz azonosítója, szerepkör, állapot, állapot, csomópont nevét, hasznos üzemidőt és más a replika adatait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --replikaazonosító [kötelező] | A replika azonosítója. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-replica-list"></a>sfctl-replika listája
A Service Fabric service partíció replika adatainak beolvasása.

A GetReplicas végpont a megadott partíció a replikák kapcsolatos információkat ad vissza. A válasz azonosítója, szerepkör, állapot, állapot, csomópont nevét, hasznos üzemidőt és más a replika adatait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [kötelező] | A partíció identitását. |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-replica-remove"></a>sfctl-replika eltávolítása
Eltávolítja a csomóponton futó service replika.

Az API-t egy replika eltávolítása a Service Fabric-fürt által a Service Fabric replika hiba szimulálja. Az Eltávolítás bezárja a replikát, átirányítja a replikát a szerepkörhöz, None, majd eltávolítja az összes az állapotinformációkat a replika a fürtből. Ez az API teszteli a replika állapota eltávolítás elérési útját, és a jelentés tartalék állandó útvonala ügyfél API-k használatával szimulálja. Figyelmeztetés – itt olyan biztonsági ellenőrzést nem végrehajtani, ha az API-t használja. Ez az API nem megfelelő használata az állapotalapú szolgáltatások esetében az adatvesztést eredményezhet. Emellett a forceRemove jelző ugyanabban a folyamatban lévő üzemeltetett összes többi replika hatással van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --replikaazonosító [kötelező] | A replika azonosítója. |
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

## <a name="sfctl-replica-report-health"></a>sfctl replika állapotjelentés
A Service Fabric-replika health jelentést küld.

A jelentés a megadott Service Fabric-replika állapotát. A jelentésnek tartalmaznia kell a forrás az egészségügyi jelentés és, amelyen jelentett tulajdonság vonatkozó információk. A jelentést küld egy Service Fabric gateway replika, amely továbbítja a health Store adatbázisban. A jelentés előfordulhat, hogy fogadja el az átjárót, azonban elutasította a health Store adatbázisban a további ellenőrzést. A health Store adatbázisban például elutasíthatják a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Látható-e a jelentés a a health Store adatbázisban alkalmaztak, ellenőrizze, hogy megjelenik-e a jelentés az események szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [kötelező] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterláncot és a nem rögzített enumerálása, hogy az Eszközállapot-feltételt, amely elindítja a jelentés kategorizálása riporter rugalmasan tulajdonság. Például egy riporter a SourceId "LocalWatchdog" figyelheti az állapotot, a rendelkezésre álló lemez egy csomóponton, ezen a csomóponton, jelentést "AvailableDisk" tulajdonság. A ugyanolyan jelentéskészítői figyelheti a csomópont-kapcsolatban –, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonságot. A health Store adatbázisban ezek a jelentések az adott csomópont számára külön állapotesemények kell kezelni. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [kötelező] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Figyelmeztetés", "Error", "Ismeretlen". |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --replikaazonosító [kötelező] | A partíció identitását. |
| – [kötelező] adatforrás-azonosítója | Az adatforrás neve azonosítja az ügyfél, a figyelő vagy a system összetevője, amely az egészségügyi információk jönnek létre. |
| – Leírás | Az egészségügyi információk leírását. <br><br> Azt jelöli, szabad szöveges adja hozzá a jelentés az emberi olvasható információk segítségével. A leírás karakterlánc maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor automatikusan csonkolva lesz. Csonkolva, amikor az utolsó karakter, a leírás tartalmaz egy "[Truncated]" jelölő, és teljes karaktersorozat 4096 karakternél. Jelenlétét, a jelölő azt jelzi, hogy a felhasználók számára, hogy a csonkolási történt. Vegye figyelembe, hogy csonkolva, a leírásnak legalább 4096 karakternél, az eredeti karakterláncot. |
| – azonnali | Azt a jelzőt, amely azt jelzi, hogy a jelentés azonnal kell küldeni. <br><br> Egy jelentés küld egy Service Fabric gateway alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz, a jelentés azonnal címről érkezik a health Store adatbázisban, függetlenül a fabric-ügyfélbeállításokat a http-átjáró alkalmazások által használt HTTP-átjáró. Ez akkor hasznos, a kritikus fontosságú jelentések, amelyek a lehető leghamarabb kell küldeni. Attól függően, ütemezését és egyéb feltételek a jelentés elküldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Ha Immediate hamis értékre van állítva, a jelentés alapján lesz elküldve az egészségügyi ügyfélbeállításokat a HTTP-átjáró. Ezért azt fogja kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az az ajánlott beállítás, mivel így az egészségügyi ügyfél állapotfigyelő jelentési üzenetek a health Store adatbázisban, valamint az egészségügyi jelentés feldolgozása optimalizálása érdekében. Alapértelmezés szerint a rendszer a jelentések nem küldése azonnal. |
| --eltávolítása akkor, ha lejárt | Érték, amely azt jelzi, hogy a jelentés törlődik a health store adatbázisból, a lejárat után. <br><br> Ha igaz értékű, a jelentés távolítja el a health Store adatbázisban után jár le. Ha az értéke HAMIS, a jelentés egy hibát, ha lejárt számít. Ez a tulajdonság értéke alapértelmezés szerint False (hamis). Amikor az ügyfelek rendszeresen készít jelentést, akkor állítsa be RemoveWhenExpired false (alapértelmezett). Ezzel a módszerrel a riporter veti fel (pl. holtpont), és nem készíthető jelentés, az entitás értékeli ki a hiba, ha lejár az állapotjelentés. Az entitás hibaállapot állapottal megőrzendő tartalomként jelöli. |
| --sorszám | A numerikus karakterláncként egészségügyi jelentés sorszáma. <br><br> A jelentés sorszám használják a health Store adatbázisban elavult jelentések észleléséhez. Ha nincs megadva, egy megfelelő sorszám health-ügyfél által automatikusan létrehozott jelentések hozzáadásakor. |
| --service-típusa | Milyen típusú szolgáltatás replika (állapot nélküli vagy állapot-nyilvántartó), amelynek az állapotát jelenti. A lehetséges értékek a következők\: "Állapot nélküli", "Állapotalapú".  Alapértelmezett\: állapotalapú. |
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

## <a name="sfctl-replica-restart"></a>sfctl-replika újraindítása
Megőrzött szolgáltatásként fut egy csomóponton szolgáltatás replikája újraindul.

Megőrzött szolgáltatásként fut egy csomóponton szolgáltatás replikája újraindul. Figyelmeztetés – itt olyan biztonsági ellenőrzést nem végrehajtani, ha az API-t használja. Az API nem megfelelő használata az állapotalapú szolgáltatások esetében a rendelkezésre állási adatvesztést eredményezhet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [kötelező] | A csomópont neve. |
| --partícióazonosító [kötelező] | A partíció identitását. |
| --replikaazonosító [kötelező] | A replika azonosítója. |
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
