---
title: Az Azure Service Fabric CLI - sfctl replika |} Microsoft Docs
description: A Service Fabric CLI sfctl replika parancsok ismerteti.
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
ms.openlocfilehash: cd09fe906f77bb06f0ac7afaa6c6cce326dbfa5c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763578"
---
# <a name="sfctl-replica"></a>sfctl-replika
A szolgáltatáspartíciók tartozó replikák kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Telepített | Lekérdezi a Service Fabric-csomópont telepített replika részleteit. |
| telepített listája | Lekérdezi a Service Fabric-csomópont telepítve replikák listáját. |
| állapot | A Service Fabric állapotalapú szolgáltatási replika- vagy állapotmentes szolgáltatások állapotának beolvasása. |
| információ | A Service Fabric-partíción replika információ lekérése. |
| lista | A Service Fabric szolgáltatás partíció replikák információ lekérése. |
| eltávolítás | Eltávolítja a csomóponton futó szolgáltatás replika. |
| report-health | A Service Fabric-replika health jelentést küld. |
| Indítsa újra a | A szolgáltatás a replika egy csomóponton futó megőrzött szolgáltatás újraindul. |

## <a name="sfctl-replica-deployed"></a>telepített sfctl replika
Lekérdezi a Service Fabric-csomópont telepített replika részleteit.

Lekérdezi a telepített Service Fabric-csomópont replika részleteit. Jelenlegi szolgáltatásműveletet elindítása ideje, partícióazonosító:, replika/példány azonosítója, jelentett terhelés és egyéb információkat, az információk közé tartozik a szolgáltatás típusa, a szolgáltatás nevét, a jelenlegi szolgáltatásműveletet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --másodpéldány-azonosító [szükséges] | A replika azonosítója. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-replica-deployed-list"></a>telepített sfctl replika-lista
Lekérdezi a Service Fabric-csomópont telepítve replikák listáját.

A Service Fabric-csomópont telepítve replikák kapcsolatos információt tartalmazó listájának beolvasása. Partícióazonosító, másodpéldány-azonosító, a replika, a szolgáltatás, a szolgáltatás típusa, és olyan információkat neve nevét állapotát tartalmazza. PartitionId vagy ServiceManifestName lekérdezési paraméterek segítségével a paraméterek megadott értékek megfelelő telepített replikák vonatkozó adatokat ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] alkalmazás-azonosító | Az alkalmazás identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric\:" URI-séma. 6.0-s verzió-től kezdődő, hierarchikus nevek vannak tagolva a "\~" karakter. Például, ha az alkalmazás neve nem "fabric\:/myapp/app1", az Alkalmazásidentitás lenne "myapp\~app1" 6.0 + és "myapp/app1" korábbi verzióiban. |
| --csomópontnév [szükséges] | A csomópont neve. |
| --partíció-azonosító | A partíció identitását. |
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

## <a name="sfctl-replica-health"></a>sfctl a replika állapota
A Service Fabric állapotalapú szolgáltatási replika- vagy állapotmentes szolgáltatások állapotának beolvasása.

A Service Fabric replika állapotának beolvasása. Az EventsHealthStateFilter használatával szűrhetők a replika állapota alapján jelentett állapotával kapcsolatos események gyűjtése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --másodpéldány-azonosító [szükséges] | A replika azonosítója. |
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

## <a name="sfctl-replica-info"></a>sfctl replika adatai
A Service Fabric-partíción replika információ lekérése.

A válasz tartalmazza az azonosítója, szerepkör, állapot, állapotát, csomópont nevét, hasznos Üzemidő, és más a replika adatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --másodpéldány-azonosító [szükséges] | A replika azonosítója. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-replica-list"></a>sfctl listáján
A Service Fabric szolgáltatás partíció replikák információ lekérése.

A GetReplicas végpont a megadott partíció a replikák vonatkozó adatokat ad vissza. A válasz tartalmazza az azonosítója, szerepkör, állapot, állapotát, csomópont nevét, hasznos Üzemidő, és más a replika adatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --partícióazonosító [szükséges] | A partíció identitását. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-replica-remove"></a>sfctl replika eltávolítása
Eltávolítja a csomóponton futó szolgáltatás replika.

Ez az API a Service Fabric replika hiba replika távolítsa el a Service Fabric-fürt szimulálja. Az Eltávolítás bezárása után a replika, a replika a szerepkör átkerül a None, majd eltávolítja az összes a fürtből a replika állapota információt. Ez az API teszteli a replika állapota eltávolítás elérési útját, és a jelentés tartalék állandó elérési útja ügyfél API-k használatával szimulálja. Figyelmeztetés - ott nem ez az API használata esetén végrehajtott biztonsági ellenőrzések. Ez az API nem megfelelő használata állapotalapú szolgáltatások adatvesztést eredményezhet. Emellett a forceRemove jelző hatással van az egy folyamatban futó összes többi replika.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --másodpéldány-azonosító [szükséges] | A replika azonosítója. |
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

## <a name="sfctl-replica-report-health"></a>a jelentés-egészségügyi sfctl replika
A Service Fabric-replika health jelentést küld.

A jelentés a megadott Service Fabric másodpéldány állapota. A jelentés az állapotjelentést és amelyen jelentett tulajdonság információt kell tartalmaznia. A jelentést küld a Service Fabric átjáró replika, amely továbbítja a health Store adatbázisban. A jelentés lehet, hogy fogadja el az átjárót, de elutasította a health Store adatbázisban a felesleges érvényesítése után. A health Store adatbázisban például utasíthatja a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Tekintse meg, hogy a jelentés a health Store adatbázisban alkalmazta, ellenőrizze, hogy a jelentés az események szakaszában jelenik meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [szükséges] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterlánc, és nem a jelentéskészítő rugalmas a jelentés állapot feltétel kategorizálásának rögzített enumerálási tulajdonság. Olyan jelentéskészítői SourceId "LocalWatchdog" a figyelheti például a csomóponton a rendelkezésre álló lemez állapotának, jelentést ezen a csomóponton "AvailableDisk" tulajdonság. Az azonos jelentéskészítői figyelheti a csomópont csatlakozási, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események a megadott csomópont tekintendők. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [szükséges] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Warning", "Error", "Ismeretlen". |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --másodpéldány-azonosító [szükséges] | A partíció identitását. |
| – [szükséges] adatforrás-azonosítója | A forrás nevét, amely azonosítja az ügyfél, a figyelő vagy a system összetevő, az egészségügyi adatokat generált. |
| – Leírás | Az állapotadatok leírása. <br><br> Adja hozzá a jelentés emberi olvasható információk segítségével szabad szöveg jelzi. A leírás maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor a program automatikusan levágja. Csonkolva lesz, ha az utolsó karakter, a leírás tartalmazza-e a mutatóhoz "[Truncated]", és teljes karaktersorozat 4096 karakternél. A jelölő jelzi a felhasználóknak, hogy csonkolása történt. Vegye figyelembe, hogy csonkolja, amikor a leírás legfeljebb 4096 karakternél az eredeti karakterláncból. |
| – azonnali | A jelzőt, amely azt jelzi, hogy a jelentés azonnal kell küldeni. <br><br> Health jelentést küld a Service Fabric átjáró alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz értéke esetén a jelentés azonnal gépről küldött a health Store adatbázisban, függetlenül a fabric ügyfélbeállításaihoz a http-átjáró alkalmazások által használt HTTP-átjárót. Ez akkor hasznos, amelyek a lehető leghamarabb küldjön kritikus jelentésekhez. Attól függően, hogy időzítési és egyéb feltételek a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Immediate értéke HAMIS, ha a jelentés megfelelően küldi el az egészségügyi ügyfélbeállításokat a HTTP-átjáróról. Ezért azt fogja lehet kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az ajánlott beállítása, mivel így az egészségügyi ügyfél állapotát a health Store adatbázisban, valamint a rendszerállapot jelentésfeldolgozás üzenetek reporting optimalizálása érdekében. Alapértelmezés szerint jelentések nem küldése azonnal. |
| --eltávolítása Ha lejárt | Érték, amely azt jelzi, hogy a jelentés eltávolítsa őket a health Store adatbázisban, ha a lejár. <br><br> Ha true értéke esetén a jelentés távolítja el a health Store adatbázisban lejárata után. Ha FALSE értéke esetén a jelentés hibát adott vissza, ha lejárt a rendszer kezeli. Ez a tulajdonság értéke HAMIS, alapértelmezés szerint. Ha az ügyfelek rendszeresen jelentést, azok RemoveWhenExpired hamis (alapértelmezés) kell beállítani. Ezzel a módszerrel a jelentéskészítő problémák (pl. holtpont), és nem tud jelenteni, az entitás értékelik ki: Hiba történt, amikor az állapotjelentést lejár. Tartalomként jelöli meg, hogy hiba állapotban lévő entitást. |
| --sorszám | Az állapotjelentés numerikus karakterláncként sorszáma. <br><br> A jelentés sorszám használják a health Store adatbázisban elavult jelentések észleléséhez. Ha nincs megadva, egy megfelelő sorszám jön létre automatikusan a rendszerállapot-ügyfél által a jelentés hozzáadásakor. |
| --szolgáltatás-típusa | Milyen típusú szolgáltatás replika (állapot nélküli és állapotalapú), amelynek állapotát jelentette. A lehetséges értékek a következők\: "Állapot nélküli", "Állapotfüggő".  Alapértelmezett\: állapotalapú alkalmazások és szolgáltatások. |
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

## <a name="sfctl-replica-restart"></a>sfctl replika újraindítása
A szolgáltatás a replika egy csomóponton futó megőrzött szolgáltatás újraindul.

A szolgáltatás a replika egy csomóponton futó megőrzött szolgáltatás újraindul. Figyelmeztetés - ott nem ez az API használata esetén végrehajtott biztonsági ellenőrzések. Ez az API nem megfelelő használata állapotalapú szolgáltatások rendelkezésre állásának elvesztéséhez vezethet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| --partícióazonosító [szükséges] | A partíció identitását. |
| --másodpéldány-azonosító [szükséges] | A replika azonosítója. |
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
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).
