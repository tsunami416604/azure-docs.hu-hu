---
title: Az Azure Service Fabric CLI - sfctl csomópont |} Microsoft Docs
description: A Service Fabric CLI sfctl csomópont parancsok ismerteti.
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
ms.openlocfilehash: fb8a310a131938e95f3d21b3962dbbd1944a57ed
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763425"
---
# <a name="sfctl-node"></a>sfctl-csomópont
A csomópontok, amely egy fürt része legyen kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| letiltása | A Service Fabric fürt csomópontokat a megadott inaktiválási szándék inaktiválása. |
| engedélyezése | Egy Service Fabric-fürt csomópontja, inaktív aktiválása. |
| állapot | Lekérdezi a Service Fabric-csomópont állapotát. |
| információ | Egy adott csomópont a információ lekérése a Service Fabric-fürt. |
| lista | A Service Fabric-fürt a csomópontok listájának beolvasása. |
| betöltés | Lekérdezi a terhelés adatokat a Service Fabric-csomópont. |
| Remove-állapot | A Service Fabric értesíti az, hogy a csomópont a megőrzött állapot véglegesen eltávolították vagy elveszett. |
| report-health | A Service Fabric-csomópont állapotát jelentést küld. |
| Indítsa újra a | A Service Fabric fürt csomópont újraindul. |
| átmenet | Elindítja vagy leállítja egy fürt csomópontja. |
| átmenet-állapot | Lekérdezi egy StartNodeTransition használatának műveletnek az előrehaladását. |

## <a name="sfctl-node-disable"></a>sfctl csomópont letiltása
A Service Fabric fürt csomópontokat a megadott inaktiválási szándék inaktiválása.

A Service Fabric fürt csomópontokat a megadott inaktiválási szándék inaktiválása. Miután a inaktiválása folyamatban van, az inaktiválási szándék lehet növelni, de nem csökkenthető (például egy csomópont szünet biztonsági mentés inaktivált inaktiválhatók további újraindítást, de nem fordítva. Csomópontok előfordulhat, hogy újra kell aktiválni az aktiválás egy csomópont műveletet bármikor után azok inaktiválása használatával. Ha el az inaktiválást nem teljes, megszakítása el az inaktiválást. Egy csomópont leáll, és ismét elérhető lesz közben inaktiválása továbbra is előtt szolgáltatások helyez el a csomópontot újra kell aktiválni kell.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| --az inaktiválást-leképezés | Ismerteti a leképezés vagy a csomópont inaktiválása okát. A lehetséges értékek követi. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-node-enable"></a>sfctl csomópont engedélyezése
Egy Service Fabric-fürt csomópontja, inaktív aktiválása.

Aktiválja az inaktív Service Fabric fürt csomópontot. Miután aktiváltak, a csomópont újra lesz új replikák elhelyezéséhez életképes célja, és a csomópont fennmaradó inaktív replikákat újra kell aktiválni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-health"></a>sfctl csomópont állapota
Lekérdezi a Service Fabric-csomópont állapotát.

Lekérdezi a Service Fabric-csomópont állapotát. Az EventsHealthStateFilter használatával szűrhetők a csomóponton a állapota alapján jelentett állapotával kapcsolatos események gyűjtése. Ha a csomópont neve szerint a health Store adatbázisban nem létezik, akkor ez hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
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

## <a name="sfctl-node-info"></a>sfctl csomópont adatai
Egy adott csomópont a információ lekérése a Service Fabric-fürt.

Egy adott csomópont a információ lekérése a Service Fabric-fürt. A válasz tartalmazza a neve, állapota, azonosítója, rendszerállapot, hasznos üzemidő és más a csomópont részletei.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-list"></a>sfctl csomópontlista
A Service Fabric-fürt a csomópontok listájának beolvasása.

A Service Fabric-fürt a csomópontok listájának beolvasása. A válasz tartalmazza a neve, állapota, azonosítója, rendszerállapot, hasznos üzemidő és más a csomópont részletei.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --node-status-filter | A csomópontok a NodeStatus alapján szűrését teszi lehetővé. Az eredmény csak a megadott szűrő értéke megfelelő csomópontot. A szűrő értéke a következők egyike lehet.  Alapértelmezett\: alapértelmezett. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-node-load"></a>sfctl csomópont betöltése
Lekérdezi a terhelés adatokat a Service Fabric-csomópont.

A Service Fabric csomópont a load vagy a meghatározott kapacitás rendelkező metrikák terhelés adatainak beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-remove-state"></a>sfctl remove-állapot
A Service Fabric értesíti az, hogy a csomópont a megőrzött állapot véglegesen eltávolították vagy elveszett.

A Service Fabric értesíti az, hogy a csomópont a megőrzött állapot véglegesen eltávolították vagy elveszett.  Ez azt jelenti, hogy nincs lehetőség az adott csomópont a megőrzött állapot helyreállításához. Ez általában akkor fordul elő, ha a merevlemez tiszta törölték, vagy ha a merevlemez összeomlik. A csomópont rendelkezik a művelet sikeres tekinti. Ez a művelet lehetővé teszi, hogy a Service Fabric arról, hogy a replikákat a csomóponton már nem létezik, és a Service Fabric álljon le, ezeket replikák helyreállnak vár. Ez a parancsmag nem futnak, ha a csomópont állapotát nem távolították el, és a csomópont is visszatérhet a változatlan állapotú.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
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

## <a name="sfctl-node-report-health"></a>a jelentés-egészségügyi sfctl csomópont
A Service Fabric-csomópont állapotát jelentést küld.

A jelentés a megadott Service Fabric-csomópont állapotát. A jelentés az állapotjelentést és amelyen jelentett tulajdonság információt kell tartalmaznia. A jelentést küld a Service Fabric átjárócsomópontnak, amely továbbítja a health Store adatbázisban. A jelentés lehet, hogy fogadja el az átjárót, de elutasította a health Store adatbázisban a felesleges érvényesítése után. A health Store adatbázisban például utasíthatja a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Tekintse meg, hogy a jelentés a health Store adatbázisban alkalmazta, ellenőrizze, hogy megjelenik-e a jelentés a HealthEvents szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [szükséges] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterlánc, és nem a jelentéskészítő rugalmas a jelentés állapot feltétel kategorizálásának rögzített enumerálási tulajdonság. Olyan jelentéskészítői SourceId "LocalWatchdog" a figyelheti például a csomóponton a rendelkezésre álló lemez állapotának, jelentést ezen a csomóponton "AvailableDisk" tulajdonság. Az azonos jelentéskészítői figyelheti a csomópont csatlakozási, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események a megadott csomópont tekintendők. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [szükséges] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Warning", "Error", "Ismeretlen". |
| --csomópontnév [szükséges] | A jelentés a csomópont neve. |
| – [szükséges] adatforrás-azonosítója | A forrás nevét, amely azonosítja az ügyfél, a figyelő vagy a system összetevő, az egészségügyi adatokat generált. |
| – Leírás | Az állapotadatok leírása. <br><br> Adja hozzá a jelentés emberi olvasható információk segítségével szabad szöveg jelzi. A leírás maximális hossza 4096 karakternél. Ha a megadott karakterlánc hosszabb, akkor a program automatikusan levágja. Csonkolva lesz, ha az utolsó karakter, a leírás tartalmazza-e a mutatóhoz "[Truncated]", és teljes karaktersorozat 4096 karakternél. A jelölő jelzi a felhasználóknak, hogy csonkolása történt. Vegye figyelembe, hogy csonkolja, amikor a leírás legfeljebb 4096 karakternél az eredeti karakterláncból. |
| – azonnali | A jelzőt, amely azt jelzi, hogy a jelentés azonnal kell küldeni. <br><br> Health jelentést küld a Service Fabric átjáró alkalmazás, amely továbbítja a health Store adatbázisban. Ha az Immediate értékre van állítva. igaz értéke esetén a jelentés azonnal gépről küldött a health Store adatbázisban, függetlenül a fabric ügyfélbeállításaihoz a http-átjáró alkalmazások által használt HTTP-átjárót. Ez akkor hasznos, amelyek a lehető leghamarabb küldjön kritikus jelentésekhez. Attól függően, hogy időzítési és egyéb feltételek a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró le van zárva, vagy az üzenet az átjáró nem érhető el. Immediate értéke HAMIS, ha a jelentés megfelelően küldi el az egészségügyi ügyfélbeállításokat a HTTP-átjáróról. Ezért azt fogja lehet kötegelni HealthReportSendInterval konfigurációjának megfelelően. Ez az ajánlott beállítása, mivel így az egészségügyi ügyfél állapotát a health Store adatbázisban, valamint a rendszerállapot jelentésfeldolgozás üzenetek reporting optimalizálása érdekében. Alapértelmezés szerint jelentések nem küldése azonnal. |
| --eltávolítása Ha lejárt | Érték, amely azt jelzi, hogy a jelentés eltávolítsa őket a health Store adatbázisban, ha a lejár. <br><br> Ha true értéke esetén a jelentés távolítja el a health Store adatbázisban lejárata után. Ha FALSE értéke esetén a jelentés hibát adott vissza, ha lejárt a rendszer kezeli. Ez a tulajdonság értéke HAMIS, alapértelmezés szerint. Ha az ügyfelek rendszeresen jelentést, azok RemoveWhenExpired hamis (alapértelmezés) kell beállítani. Ezzel a módszerrel a jelentéskészítő problémák (pl. holtpont), és nem tud jelenteni, az entitás értékelik ki: Hiba történt, amikor az állapotjelentést lejár. Tartalomként jelöli meg, hogy hiba állapotban lévő entitást. |
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

## <a name="sfctl-node-restart"></a>sfctl csomópont újraindítása
A Service Fabric fürt csomópont újraindul.

Újraindítja a egy Service Fabric-fürt csomópontja, amely már el van indítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| --create-fabric-dump | Adja meg az igaz a háló munkaterület folyamat egy biztonsági másolat létrehozásához. Ez a kis-és nagybetűket.  Alapértelmezett\: hamis. |
| --node-instance-id | A célcsomóponton Példányazonosítója. Ha Példányazonosító van megadva, a csomópont csak akkor, ha ez megegyezik-e az aktuális példány a csomópont újraindul. Alapértelmezett érték "0" megfelelő minden példány azonosítója. Get-csomópont lekérdezéssel Példányazonosítója lehet lekérdezni.  Alapértelmezett\: 0. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-node-transition"></a>sfctl csomópont átmenet
Elindítja vagy leállítja egy fürt csomópontja.

Elindítja vagy leállítja egy fürt csomópontja.  Egy fürtcsomópont egy folyamatot, nem pedig magát az operációs rendszer példány.  Indítja el a csomópontot, adja át a "Start" NodeTransitionType paramétereként. Csomópont leállítása a NodeTransitionType paraméter át a "Stop". Ez az API - művelet kezdődik, amikor a az API-t adja vissza, a csomópont esetleg nem végzett még tér át. Az azonos OperationID azonosítójú lekérni a műveletnek az előrehaladását a GetNodeTransitionProgress hívja.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópont-példány-azonosító [szükséges] | A csomópont a célcsomóponton Példányazonosítója. Ez GetNodeInfo API-n keresztül kell meghatározni. |
| --csomópontnév [szükséges] | A csomópont neve. |
| – [szükséges] csomópont-átviteli-típusa | Hajtsa végre az áttérés típusát jelöli.  NodeTransitionType.Start indul el egy leállított csomópont. NodeTransitionType.Stop, hogy egy csomópont leáll. |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --stop-időtartam-a-másodperc [] | Az időtartam (másodpercben), a csomópont tartása leállt.  A minimális értéke 600, a maximális hossz 14400.  Az idő lejárta után a csomópont lesz automatikusan helyreállnak. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-node-transition-status"></a>sfctl csomópont átmenet-állapot
Lekérdezi egy StartNodeTransition használatának műveletnek az előrehaladását.

Lekérdezi a megadott OperationID azonosítójú használatával StartNodeTransition használatába egy műveletnek az előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --csomópontnév [szükséges] | A csomópont neve. |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
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