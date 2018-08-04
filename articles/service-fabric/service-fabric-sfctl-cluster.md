---
title: Az Azure Service Fabric parancssori felület - sfctl-fürt |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-fürt parancsokat.
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
ms.openlocfilehash: 4b0491d59e4ac495750a338ad743aab69ff47a4e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494243"
---
# <a name="sfctl-cluster"></a>sfctl-fürt
Válassza ki, kezelése és üzemeltetése a Service Fabric-fürtök.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| kód-verziók | Service Fabric-fürtben üzembe helyezett kód verziók fabric listáját kéri le. |
| config-versions | Service Fabric-fürtben üzembe helyezett konfigurációs verziók fabric listáját kéri le. |
| állapot | Service Fabric-fürt állapotának beolvasása. |
| Manifest | A Service Fabric fürtjegyzék beolvasása. |
| művelet – Mégse | Egy felhasználó által előidézett hibák művelet megszakítása. |
| művelet – lista | A megadott adatok alapján szűrt felhasználó által előidézett hibák műveletek listájának beolvasása. |
| üzembe helyezése | A Service Fabric-fürt kódírásra vagy konfigurálásra csomagok üzembe helyezése. |
| helyreállítás-rendszer | Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia jelenleg ragadnak kvórumveszteségben rendszerszolgáltatások helyreállítani. |
| report-health | A Service Fabric-fürt állapotának jelentést küld. |
| kiválasztás | A Service Fabric-fürt végpontja csatlakozik. |
| show-connection | Bemutatják, milyen Service Fabric-fürtön az sfctl-példányhoz csatlakozik. |
| unprovision | A Service Fabric-fürt kódírásra vagy konfigurálásra csomagok telepítésének visszavonása. |
| frissítés | Indítsa el a Service Fabric-fürt kódírásra vagy konfigurálásra verzió frissítése. |
| frissítés-folytatása | Győződjön meg a fürt frissítésének léphet tovább a következő frissítési tartományra. |
| frissítés-visszaállítás | Állítsa vissza a Service Fabric-fürt frissítését. |
| frissítés – állapot | Lekérdezi az aktuális fürt frissítésének előrehaladása. |
| frissítés – frissítés | Frissítés a Service Fabric-fürt frissítése frissítési paramétereit. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-verziók
Service Fabric-fürtben üzembe helyezett kód verziók fabric listáját kéri le.

Információ a háló listáját kéri le a fürtben felhasznált kód verziója. A paraméter CodeVersion segítségével igény szerint szűrheti a kimenetet a csak adott verzióra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --code-version | A Service Fabric a termék verzióját. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-verziók
Service Fabric-fürtben üzembe helyezett konfigurációs verziók fabric listáját kéri le.

Információ a háló listáját kéri le a fürtben üzembe helyezett konfigurációs verziók. A ConfigVersion paraméter segítségével igény szerint szűrheti a kimenetet a csak adott verzióra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --config-version | A Service fabric config verziója. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-health"></a>sfctl-fürt állapota
Service Fabric-fürt állapotának beolvasása.

EventsHealthStateFilter használatával szűrhetők a fürtön, az állapota alapján jelentett hálózatállapot-események gyűjtésére. Ehhez hasonlóan NodesHealthStateFilter és ApplicationsHealthStateFilter szűréséhez használja a gyűjtemény csomópontot és azok összesített állapota alapján visszaadott alkalmazásokat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --applications-health-state-filter | Az alkalmazás állapotának állapot objektumok azok állapotát a fürt állapotának lekérdezéshez eredményét adja vissza szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a kapott tagok, vagy bitenkénti műveletek HealthStateFilter enumerálás egy tagja az egész szám. Csak olyan alkalmazásokat, amelyek a szűrőnek megfelelő adja vissza. Minden alkalmazás segítségével kiértékelése összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd OK (2), és figyelmeztetés (4) HealthState értékét az alkalmazások állapotát vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --események-állapot – állapot-szűrő | A gyűjtemény állapotesemény – a visszaadott objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény segítségével kiértékelése összesített állapotát. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket, a bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd az OK (2), és figyelmeztetés (4) HealthState értékét az események vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi-e az egészségügyi statisztikák a rendszer visszalépteti a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a gyermekek száma entitások állapota Ok, figyelmeztetés és hiba. |
| --tartalmazza-rendszer-alkalmazás-egészségügyi-statisztikák | Azt jelzi, hogy az egészségügyi statisztikák tartalmazzák-e a háló\:vagy alkalmazásstatisztika-állapotát. Alapértelmezés szerint FALSE. Ha IncludeSystemApplicationHealthStatistics értéke igaz, az egészségügyi statisztika a hálóhoz tartozó entitásokat tartalmazza\:vagy alkalmazás. Ellenkező esetben a lekérdezés eredménye csak a felhasználói alkalmazások állapotának statisztikai magában foglalja. Az egészségügyi statisztikák szerepelnie kell a lekérdezés eredménye a alkalmazni ezt a paramétert. |
| --nodes-health-state-filter | A csomópont állapota állapot objektumok azok állapotát a fürt állapotának lekérdezéshez eredményét adja vissza szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotokat egy egész szám. Csak azok a csomópontok a szűrőnek megfelelő adja vissza. Minden csomópont összesített állapota kiértékelése szolgálnak. Ha nincs megadva, a rendszer minden bejegyzést adja vissza. Állapot értékei jelző-alapú számbavétel, így az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor használatával beszerzett kombinációját. Például ha a megadott értéke 6 majd csomópont állapota OK (2), és figyelmeztetés (4) HealthState értékét a rendszer adja vissza.  <br> -Alapértelmezett – alapértelmezett érték. Bármely HealthState illeszkedik. A tulajdonság értéke nulla.  <br> – Nincs – szűrő, amely nem felel meg a HealthState értéket. Annak érdekében, hogy ne adjon vissza eredményt egy adott gyűjteményen állapotok használja. Az érték az 1.  <br> -Ok - szűrheti, hogy egyezések bemeneti HealthState értékét az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyezések bemenet a HealthState értékét figyelmeztetés. Az érték a 4.  <br> -Hiba - szűrő, amely megfelel a bemeneti hiba HealthState értékkel. Értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti HealthState értéket. Az érték 65535. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-manifest"></a>sfctl-fürt manifest
A Service Fabric fürtjegyzék beolvasása.

A Service Fabric fürtjegyzék beolvasása. A fürtjegyzék tartalmazza a fürt tulajdonságait, amely tartalmazza a különböző típusok fürtöt, biztonsági konfigurációk, hiba, és a frissítési tartomány topológiák, stb. Ezek a tulajdonságok vannak megadva a ClusterConfig.JSON fájl különálló fürt üzembe helyezése során. Azonban a legtöbb információ a fürtjegyzékben belsőleg által létrehozott service fabric központi telepítési forgatókönyvek esetén a fürt üzembe helyezése során (például ha az Azure portal használatával). A fürtjegyzék tartalma csak tájékoztatási célokat szolgálnak, és a felhasználók nem várható a fájl tartalmát, vagy hogy hogyan értelmezte a függőséget.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl fürt művelet megszakítás
Egy felhasználó által előidézett hibák művelet megszakítása.

A következő API-kat, indítsa el CancelOperation használatával törlődhet tartalék műveletek: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Ha kényszerített false (hamis), majd a megadott felhasználó által kiváltott művelet szabályosan leállítja, majd törlődik.  Kényszerített értéke igaz, ha a rendszer megszakítja a parancs, és egyes belső állapot maradhatnak.  Adja meg a force TRUE körültekintően kell használni. Kényszerített igaz értékű-e API meghívása nem engedélyezett, amíg az API már meghívta a kényszerített értéke FALSE (hamis) első ugyanazt a teszt parancsot, vagy, ha a vizsgálat parancs már van egy OperationState.RollingBack OperationState. 

 Pontosítás\: OperationState.RollingBack azt jelenti, hogy a rendszer lesz/törli az belső állapot parancs végrehajtása okozta. Ez lesz nem esetén az adatok helyreállítását a vizsgálat parancs lett az adatvesztést okozhat.  Például ha StartDataLoss hívja meg az API meghívásához, a rendszer fogja csak a törlés belső állapot a következő parancs futtatásával. Ez lesz nem a célpartíción esetén az adatok helyreállítását, a parancs elegendő haladt előre a adatvesztést okozhat. 

> [!NOTE]
> Ha ez az API meghívása kényszerített == igaz, belső állapot maradhatnak.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [kötelező] | Egy GUID Azonosítót, amely azonosítja az API-hívás.  Ez a megfelelő GetProgress API átad. |
| --kényszerítése | Azt jelzi, hogy szabályosan állítja vissza, és törli az módosította a felhasználó által kiváltott művelet végrehajtása belső rendszerállapot-e. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-operation-list"></a>sfctl-fürt-műveletlista
A megadott adatok alapján szűrt felhasználó által előidézett hibák műveletek listájának beolvasása.

A megadott adatok alapján szűrt felhasználói okozta hibák műveletek listájának beolvasása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – Állapot-szűrő | Használt OperationState meg a felhasználó által kiváltott műveletekhez. <br> 65535 - az összes kijelölése <br> 1 – Válassza ki a futó <br> 2 – RollingBack kiválasztása <br>8 – válassza ki a Befejezve <br>16 – Faulted kiválasztása <br>32 - kiválasztása közben történt megszakítás <br>64 - ForceCancelled válassza ki.  <br>Alapértelmezett\: 65535. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| ---szűrő | Használt OperationType a felhasználó által kiváltott műveletekhez. <br> 65535 - az összes kijelölése <br> 1 – Válassza ki a PartitionDataLoss. <br> 2 – válassza ki a PartitionQuorumLoss. <br> 4 – válassza ki a PartitionRestart. <br> 8 – válassza ki a NodeTransition.  <br> Alapértelmezett\: 65535. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-provision"></a>sfctl-fürt üzembe helyezése
A Service Fabric-fürt kódírásra vagy konfigurálásra csomagok üzembe helyezése.

Ellenőrizze, és üzembe helyezése a Service Fabric-fürt kódírásra vagy konfigurálásra csomagokat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --cluster-manifest-file-path | A fürt Alkalmazásjegyzék-fájl elérési útja. |
| --code-file-path | A fürt kód csomag elérési útja. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-recover-system"></a>sfctl fürt helyreállítása-rendszer
Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia jelenleg ragadnak kvórumveszteségben rendszerszolgáltatások helyreállítani.

Azt jelzi, hogy a Service Fabric-fürthöz, hogy kell próbálnia jelenleg ragadnak kvórumveszteségben rendszerszolgáltatások helyreállítani. Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-report-health"></a>sfctl fürt állapotjelentés
A Service Fabric-fürt állapotának jelentést küld.

A jelentésnek tartalmaznia kell a forrás az egészségügyi jelentés és, amelyen jelentett tulajdonság vonatkozó információk. A jelentést küld egy Service Fabric átjárócsomópontnak, amely továbbítja a health Store adatbázisban. A jelentés előfordulhat, hogy fogadja el az átjárót, azonban elutasította a health Store adatbázisban a további ellenőrzést. A health Store adatbázisban például elutasíthatják a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Látható-e a jelentés alkalmazta-e a a health Store adatbázisban, ellenőrizze, hogy a jelentés megjelenik a fürt a HealthEvents.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [kötelező] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterláncot és a nem rögzített enumerálása, hogy az Eszközállapot-feltételt, amely elindítja a jelentés kategorizálása riporter rugalmasan tulajdonság. Például egy riporter a SourceId "LocalWatchdog" figyelheti az állapotot, a rendelkezésre álló lemez egy csomóponton, ezen a csomóponton, jelentést "AvailableDisk" tulajdonság. A ugyanolyan jelentéskészítői figyelheti a csomópont-kapcsolatban –, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonságot. A health Store adatbázisban ezek a jelentések az adott csomópont számára külön állapotesemények kell kezelni. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [kötelező] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Figyelmeztetés", "Error", "Ismeretlen". |
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

## <a name="sfctl-cluster-select"></a>sfctl-fürt kiválasztása
A Service Fabric-fürt végpontja csatlakozik.

Ha a biztonságos fürthöz csatlakozik, adja meg egy tanúsítványt (.crt) és a kulcs fájlját (.key) vagy egy fájl abszolút elérési útnak mindkét (.pem). Adja meg mindkettőt. Ha a biztonságos fürthöz csatlakozik, megadhat is egy hitelesítésszolgáltató csomag fájl vagy könyvtár megbízható CA-tanúsítványok az abszolút elérési útját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] végpont | A fürt végpont URL-címe, beleértve a port és a HTTP vagy HTTPS előtaggal. |
| – aad-ben | Azure Active Directory használata a hitelesítéshez. |
| --ca | Hitelesítésszolgáltatói tanúsítványok címtár kezelésére is érvényes, vagy a CA nyalábfájl abszolút elérési útja. |
| – tanúsítvány | Egy ügyféltanúsítvány-fájl abszolút elérési útját. |
| --key | Ügyféltanúsítvány-kulcs fájl abszolút elérési útját. |
| --no-verify | HTTPS használata esetén tiltsa le a hitelesítési tanúsítványok, vegye figyelembe\: Ez egy nem biztonságos megoldás, és nem használható éles környezetben. |
| --pem | Ügyféltanúsítványt, a .pem fájl abszolút elérési útját. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Bemutatják, milyen Service Fabric-fürtön az sfctl-példányhoz csatlakozik.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-unprovision"></a>sfctl-fürt unprovision
A Service Fabric-fürt kódírásra vagy konfigurálásra csomagok telepítésének visszavonása.

Külön-külön leépítése kód és a konfiguráció támogatott.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --code-version | A csomag verzió. |
| --config-version | A fürtjegyzék verziója. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-upgrade"></a>sfctl-fürt frissítése
Indítsa el a Service Fabric-fürt kódírásra vagy konfigurálásra verzió frissítése.

Ellenőrizze a megadott frissítési paraméterek, és indítsa el a Service Fabric-fürt kódírásra vagy konfigurálásra verzió frissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-health-map | JSON-kódolású szótár párok alkalmazás nevét és a nem megfelelő, hiba kiváltása előtt maximális százalékos aránya. |
| – alkalmazás-típus-állapotfigyelő-térkép | JSON-kódolású szótár párok alkalmazástípus neve és a nem megfelelő, hiba kiváltása előtt maximális százalékos aránya. |
| --code-version | A verzió. |
| --config-version | A fürt konfiguráció verziója. |
| --különbözeti-állapot-értékelést | Lehetővé teszi, hogy mindegyik frissítési tartományon befejezése után abszolút állapotának kiértékelését helyett a különbözeti állapotának kiértékelését. |
| – a különbözeti sérült csomópontok | A maximálisan engedélyezett csomópontok százaléka egészségügyi teljesítménycsökkenés fürt frissítések során engedélyezett.  Alapértelmezett\: 10. <br><br> A különbözeti mérik a frissítés kezdetén csomópontok állapotát, és a health kiértékelés idején a csomópontok állapotát. Az ellenőrzés minden frissítési tartomány frissítési művelet befejezését követően ellenőrizze, hogy a fürt a globális állapot eltűrt korlátokon belül történik. |
| --failure-action | Lehetséges értékek a következők\: "Érvénytelen", "Visszaállítás" Manual (manuális). |
| --kényszerített újraindítás | Kényszerített újraindítás. |
| --health-check-retry | Állapotellenőrzéssel való újrapróbálkozás időkorlátja ezredmásodpercben. |
| – állapot-ellenőrzés – stabil | Állapotának ellenőrzése stabilitásának időtartama ezredmásodpercben. |
| – állapot-ellenőrzés-wait | Állapotellenőrzés várakozási időtartama ezredmásodpercben. |
| --replica-set-check-timeout | Replikakészlet frissítési ellenőrzés időkorlátja, másodpercben. |
| – működés közbeni frissítés-mód | Lehetséges értékek a következők\: "Érvénytelen", 'UnmonitoredAuto', 'UnmonitoredManual', "Figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| – nem megfelelő alkalmazások | A maximálisan engedélyezett sérült alkalmazások százaléka előtt egy hibát jelez. <br><br> Ahhoz, hogy 10 %-a nem megfelelő állapotú, hogy az alkalmazások, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő alkalmazások jelöli. Ha a százalékos tiszteletben tartják, de legalább egy sérült alkalmazás, az egészségügyi figyelmeztetés minősül. Ez kiszámítása a nem megfelelő állapotú alkalmazások száma teljes száma a fürtben, kivéve az alkalmazásokat, amelyek szerepelnek a ApplicationTypeHealthPolicyMap alkalmazástípusok alkalmazáspéldányok keresztül. A számítási kerekít egy sikertelen kérelmek kis számú működését. |
| – sérült csomópontok | A maximálisan engedélyezett százalékos arányát sérült csomópontok előtt egy hibát jelez. <br><br> Ahhoz, hogy sérült csomópontok 10 %-át, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő állapotú csomópontokat jelöli. Ha a százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, az egészségügyi figyelmeztetés minősül. Százalékos kiszámítása a nem megfelelő állapotú csomópontok számát a fürtben található csomópontok száma keresztül. Felfelé kerekít a számítási csomópontok kis számú egy hiba elviselni. Nagy fürtjein található egyes csomópontok minden esetben le- illetve felskálázni a javításához, ezért ezt a százalékos arányt, amely ugyan úgy kell konfigurálni. |
| --upgrade-domain-delta-unhealthy-nodes | A maximálisan engedélyezett frissítési tartományi csomópontok százaléka egészségügyi teljesítménycsökkenés fürt frissítések során engedélyezett.  Alapértelmezett\: 15. <br><br> A különbözeti mérik a frissítés kezdetén frissítési tartományi csomópontok állapotát és a health kiértékelés idején a frissítési tartományi csomópontok állapotát. Minden frissítési tartomány összes frissítési befejezése után, hogy a frissítési tartományok állapota megengedett korláton belüli frissítési tartományok a-ellenőrzést hajtanak végre. |
| --frissítés-tartomány-időkorlátja | Frissítési tartomány időkorlátja ezredmásodpercben. |
| --frissítés – időtúllépés | Frissítési időkorlát ezredmásodpercben. |
| --warning-as-error | Figyelmeztetések az azonos súlyossági hibákként kell kezelni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl fürt frissítése – folytatása
Győződjön meg a fürt frissítésének léphet tovább a következő frissítési tartományra.

Áttérni fürt kódírásra vagy konfigurálásra frissítési meg az a következő frissítési tartományra Ha szükséges.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] tartományon kívüli frissítés | A fürt frissítése a következő frissítési tartományra. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl fürt frissítés-visszaállítás
Állítsa vissza a Service Fabric-fürt frissítését.

Visszaállítás a Service Fabric-fürt kódírásra vagy konfigurálásra frissítését.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl-fürt frissítése-állapota
Lekérdezi az aktuális fürt frissítésének előrehaladása.

Lekérdezi a folyamatban lévő fürtfelügyeleti frissítés aktuális állapotát. Nincs frissítés folyamatban van, ha az előző Fürtfrissítés utolsó állapotának beolvasására.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl fürt frissítés – frissítés
Frissítés a Service Fabric-fürt frissítése frissítési paramétereit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-health-map | JSON-kódolású szótár párok alkalmazás nevét és a nem megfelelő, hiba kiváltása előtt maximális százalékos aránya. |
| – alkalmazás-típus-állapotfigyelő-térkép | JSON-kódolású szótár párok alkalmazástípus neve és a nem megfelelő, hiba kiváltása előtt maximális százalékos aránya. |
| --különbözeti-állapot-értékelést | Lehetővé teszi, hogy mindegyik frissítési tartományon befejezése után abszolút állapotának kiértékelését helyett a különbözeti állapotának kiértékelését. |
| – a különbözeti sérült csomópontok | A maximálisan engedélyezett csomópontok százaléka egészségügyi teljesítménycsökkenés fürt frissítések során engedélyezett.  Alapértelmezett\: 10. <br><br> A különbözeti mérik a frissítés kezdetén csomópontok állapotát, és a health kiértékelés idején a csomópontok állapotát. Az ellenőrzés minden frissítési tartomány frissítési művelet befejezését követően ellenőrizze, hogy a fürt a globális állapot eltűrt korlátokon belül történik. |
| --failure-action | Lehetséges értékek a következők\: "Érvénytelen", "Visszaállítás" Manual (manuális). |
| --kényszerített újraindítás | Kényszerített újraindítás. |
| --health-check-retry | Állapotellenőrzéssel való újrapróbálkozás időkorlátja ezredmásodpercben. |
| – állapot-ellenőrzés – stabil | Állapotának ellenőrzése stabilitásának időtartama ezredmásodpercben. |
| – állapot-ellenőrzés-wait | Állapotellenőrzés várakozási időtartama ezredmásodpercben. |
| --replica-set-check-timeout | Replikakészlet frissítési ellenőrzés időkorlátja, másodpercben. |
| – működés közbeni frissítés-mód | Lehetséges értékek a következők\: "Érvénytelen", 'UnmonitoredAuto', 'UnmonitoredManual', "Figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| – nem megfelelő alkalmazások | A maximálisan engedélyezett sérült alkalmazások százaléka előtt egy hibát jelez. <br><br> Ahhoz, hogy 10 %-a nem megfelelő állapotú, hogy az alkalmazások, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő alkalmazások jelöli. Ha a százalékos tiszteletben tartják, de legalább egy sérült alkalmazás, az egészségügyi figyelmeztetés minősül. Ez kiszámítása a nem megfelelő állapotú alkalmazások száma teljes száma a fürtben, kivéve az alkalmazásokat, amelyek szerepelnek a ApplicationTypeHealthPolicyMap alkalmazástípusok alkalmazáspéldányok keresztül. A számítási kerekít egy sikertelen kérelmek kis számú működését. |
| – sérült csomópontok | A maximálisan engedélyezett százalékos arányát sérült csomópontok előtt egy hibát jelez. <br><br> Ahhoz, hogy sérült csomópontok 10 %-át, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő állapotú csomópontokat jelöli. Ha a százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, az egészségügyi figyelmeztetés minősül. Százalékos kiszámítása a nem megfelelő állapotú csomópontok számát a fürtben található csomópontok száma keresztül. Felfelé kerekít a számítási csomópontok kis számú egy hiba elviselni. Nagy fürtjein található egyes csomópontok minden esetben le- illetve felskálázni a javításához, ezért ezt a százalékos arányt, amely ugyan úgy kell konfigurálni. |
| --upgrade-domain-delta-unhealthy-nodes | A maximálisan engedélyezett frissítési tartományi csomópontok százaléka egészségügyi teljesítménycsökkenés fürt frissítések során engedélyezett.  Alapértelmezett\: 15. <br><br> A különbözeti mérik a frissítés kezdetén frissítési tartományi csomópontok állapotát és a health kiértékelés idején a frissítési tartományi csomópontok állapotát. Minden frissítési tartomány összes frissítési befejezése után, hogy a frissítési tartományok állapota megengedett korláton belüli frissítési tartományok a-ellenőrzést hajtanak végre. |
| --frissítés-tartomány-időkorlátja | Frissítési tartomány időkorlátja ezredmásodpercben. |
| --egyedülálló frissítése | Lehetséges értékek a következők\: "Érvénytelen', 'Működés közbeni',"Rolling_ForceRestart".  Alapértelmezett\: működés közbeni. |
| --frissítés – időtúllépés | Frissítési időkorlát ezredmásodpercben. |
| --warning-as-error | Figyelmeztetések az azonos súlyossági hibákként kell kezelni. |

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