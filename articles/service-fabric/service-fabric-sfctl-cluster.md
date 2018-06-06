---
title: Az Azure Service Fabric CLI - sfctl fürt |} Microsoft Docs
description: A Service Fabric CLI sfctl fürt parancsok ismerteti.
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
ms.openlocfilehash: 60f3f74778f0fb32677c3b87b3140131ccd37bea
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763629"
---
# <a name="sfctl-cluster"></a>sfctl-fürt
Válassza ki, kezelése és a Service Fabric-fürtök működik.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| kód-verziók | A Service Fabric-fürt kiépített kód verziók háló listájának lekérése. |
| config-versions | A Service Fabric-fürt kiépített config verziója háló listájának lekérése. |
| állapot | Lekérdezi a Service Fabric-fürt állapotát. |
| Manifest | A Service Fabric-fürt jegyzékfájl beolvasása. |
| művelet – Mégse | A felhasználó által előidézett tartalék művelet visszavonása. |
| művelet – lista | Lekérdezi a megadott adatok alapján szűrt felhasználói által előidézett tartalék műveletek listáját. |
| Kiépítés | A kódban, illetve konfigurációs csomagokat a Service Fabric-fürt telepítéséhez. |
| helyreállítás a rendszerhez | A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a rendszerszolgáltatások, amely a kvórum elvesztése jelenleg marad. |
| report-health | A Service Fabric-fürt állapotát jelentést küld. |
| kiválasztás | A kapcsolatot a Service Fabric-fürt végpontja. |
| Unprovision | Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt. |
| Frissítés | Indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése. |
| frissítés-folytatása | Ellenőrizze a fürt frissítése, lépjen tovább a következő frissítési tartományra. |
| frissítés-visszaállítás | Állítsa vissza a Service Fabric-fürt frissítését. |
| frissítés-állapot | Lekérdezi az aktuális Fürtfrissítés előrehaladását. |
| frissítés – frissítés | A Service Fabric-fürtök frissítése frissítési paramétereinek frissítése. |

## <a name="sfctl-cluster-code-versions"></a>sfctl fürt kód-verziók
A Service Fabric-fürt kiépített kód verziók háló listájának lekérése.

Információ a háló listájának lekérése a fürtben kiépített kód verziók. A paraméter CodeVersion opcionálisan szűrése a kimenet csak adott verzió használható.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --code-version | A Service Fabric termék verzióját. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-config-versions"></a>sfctl fürt config-verziók
A Service Fabric-fürt kiépített config verziója háló listájának lekérése.

Információ a háló listájának lekérése a fürtben kiépített config verziója. A paraméter ConfigVersion opcionálisan szűrése a kimenet csak adott verzió használható.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --config-version | A Service Fabric config verziója. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-health"></a>sfctl fürt állapotát
Lekérdezi a Service Fabric-fürt állapotát.

Lekérdezi a Service Fabric-fürt állapotát. Az EventsHealthStateFilter használatával szűrhetők a fürt állapota alapján jelentett állapotával kapcsolatos események gyűjtése. Ehhez hasonlóan szűréséhez használja a NodesHealthStateFilter és az ApplicationsHealthStateFilter csomópont, és az összesített állapota alapján visszaadott alkalmazások gyűjteménye.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --applications-health-state-filter | Az alkalmazás állapotát állapota objektumok tért vissza a állapota alapján a fürt állapotának lekérdezés eredménye szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a tagokat, vagy bitenkénti műveletek HealthStateFilter számbavétel tagjainak egész érték. Csak olyan alkalmazások, a szűrőnek megfelelő adja vissza. Összesített állapotát értékeléséhez használt összes alkalmazást. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd OK (2) és a figyelmeztető (4) a HealthState értékét az alkalmazások állapotának is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --események-rendszerállapot-állapot-szűrő | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| --Állapotstatisztika kizárása | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |
| --tartalmazzák-rendszer-alkalmazás-egészségügyi-statisztikák | Azt jelzi, hogy a health statisztika tartalmazzák-e a háló\:vagy alkalmazásstatisztika-állapotát. Alapértelmezés szerint FALSE. Ha IncludeSystemApplicationHealthStatistics értéke igaz, az egészségügyi statisztika közé tartozik a tartozó entitások is szerepelnek a háló\:vagy alkalmazás. Ellenkező esetben a lekérdezés eredménye tartalmaz egészségügyi statisztikák csak felhasználói alkalmazások esetében. A lekérdezés eredményében alkalmazandó paraméter a állapotstatisztika szerepelnie kell. |
| --nodes-health-state-filter | A csomópont állapotát állapot objektumok tért vissza a állapota alapján a fürt állapotának lekérdezés eredménye szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a csomópontok a szűrőnek megfelelő adja vissza. Összesített állapotát értékeléséhez használt összes csomópontján. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd csomópontok állapota az OK (2) és a figyelmeztető (4) a HealthState értékét is megjelennek.  <br> -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla.  <br> -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1.  <br> -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték.  <br> -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4.  <br> -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8.  <br> -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-manifest"></a>a fürtjegyzékben sfctl
A Service Fabric-fürt jegyzékfájl beolvasása.

A Service Fabric-fürt jegyzékfájl beolvasása. A fürtjegyzékben, amelyek tartalmazzák a fürt, biztonsági beállításokkal, hiba, és a frissítési tartomány topológiák stb a különböző csomóponttípusok a fürt tulajdonságait tartalmazza. Ezek a tulajdonságok vannak megadva a művelet fájl egy különálló fürt telepítése közben. Azonban a legtöbb a a fürtjegyzékben információ van által belsőleg generált a service fabric fürt telepítésekor a központi telepítési forgatókönyvek esetén (pl. az Azure portál használata). A fürtjegyzékben tartalma csak tájékoztató jellegű, és a felhasználók nem vehet igénybe a függőség tartalmát, vagy a értelmezés formátumát.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-operation-cancel"></a>művelet – Mégse sfctl fürt
A felhasználó által előidézett tartalék művelet visszavonása.

A következő API-k start CancelOperation használatával vissza lehet vonni tartalék műveletek: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Ha kényszerített hamis, majd a megadott felhasználó által előidézett művelet szabályosan leáll és karbantartása.  Kényszerített értéke true, ha a parancsot meg lesz szakítva, és egyes belső állapot maradhatnak.  Adja meg a force igaz körültekintően kell használni. A kényszerített igaz értékre állítva ez az API hívása nem engedélyezett az API már meghívták a azonos tesztparancs hamis első kényszerített beállítva, vagy, ha a teszt parancs már van egy OperationState.RollingBack OperationState. 

Tisztázása\: OperationState.RollingBack azt jelenti, hogy a rendszer lesz/belső rendszer törli az állapot a következő parancs végrehajtásakor okozza. Az adatokat nem azt fogja vissza, ha a tesztparancs adatvesztést okozhat.  Például ha StartDataLoss hívja, akkor ez az API hívása, a rendszer csak megtisztítja belső állapotát a parancs futtatását. A célpartíciót az adatokat, nem azt fogja vissza, ha a parancs feldolgozásáig jutott el adatvesztést okozhat. 

> [!NOTE]
> Ha ez az API meghívása kényszerített == true, belső állapot maradhatnak.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Műveletazonosító [szükséges] | Ez az API hívása azonosító egy GUID.  Ez a megfelelő GetProgress API be lett átadva. |
| --kényszerítése | Azt jelzi, hogy szabályosan visszaállítása, és módosítja a felhasználó által előidézett művelet végrehajtása belső rendszerállapot tisztítása. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-operation-list"></a>sfctl művelet-fürtlista
Lekérdezi a megadott adatok alapján szűrt felhasználói által előidézett tartalék műveletek listáját.

Lekérdezi a felhasználó által előidézett tartalék műveletek megadott adatok alapján szűrt listája.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Állapot-szűrő | A felhasználó által előidézett műveletek a OperationState tartozó szűrésére használatos. <br> 65535 - összes kijelölése <br> 1 – Válassza ki a fut <br> 2 - RollingBack kiválasztása <br>8 – válassza ki a Befejezve <br>16 - Faulted kiválasztása <br>32 - megszakítás kiválasztása <br>64 - ForceCancelled kiválasztása  <br>Alapértelmezett\: 65535. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| ---szűrő | A felhasználó által előidézett műveletek a művelettípus szűrésére használatos. <br> 65535 - összes kijelölése <br> 1 - PartitionDataLoss kiválasztása <br> 2 - PartitionQuorumLoss kiválasztása <br> 4 - PartitionRestart kiválasztása <br> 8 - NodeTransition kiválasztása  <br> Alapértelmezett\: 65535. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-provision"></a>sfctl fürt létesítése
A kódban, illetve konfigurációs csomagokat a Service Fabric-fürt telepítéséhez.

Ellenőrzi, és jogosultságok kiosztása a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --cluster-manifest-file-path | A fürt Alkalmazásjegyzék-fájl elérési útja. |
| --code-file-path | A fürt kód csomag fájl elérési útja. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-recover-system"></a>sfctl fürt helyreállítása-rendszer
A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a rendszerszolgáltatások, amely a kvórum elvesztése jelenleg marad.

A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a rendszerszolgáltatások, amely a kvórum elvesztése jelenleg marad. Ez a művelet csak akkor hajtható végre, ha ismert, hogy a replikákat, le nem állítható helyre. Ez az API nem megfelelő használata a lehetséges adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-report-health"></a>a jelentés-egészségügyi sfctl fürt
A Service Fabric-fürt állapotát jelentést küld.

A jelentés az állapotjelentést és amelyen jelentett tulajdonság információt kell tartalmaznia. A jelentést küld a Service Fabric átjárócsomópontnak, amely továbbítja a health Store adatbázisban. A jelentés lehet, hogy fogadja el az átjárót, de elutasította a health Store adatbázisban a felesleges érvényesítése után. A health Store adatbázisban például utasíthatja a jelentés érvénytelen paraméter, például egy elavult sorozatszám miatt. Tekintse meg, hogy a jelentés a health Store adatbázisban alkalmazta, ellenőrizze, hogy a jelentés a fürt HealthEvents jelenik meg.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – rendszerállapot-tulajdonság [szükséges] | A tulajdonság az állapotadatokat. <br><br> Egy entitás különböző tulajdonságaihoz állapotjelentések rendelkezhet. Egy karakterlánc, és nem a jelentéskészítő rugalmas a jelentés állapot feltétel kategorizálásának rögzített enumerálási tulajdonság. Olyan jelentéskészítői SourceId "LocalWatchdog" a figyelheti például a csomóponton a rendelkezésre álló lemez állapotának, jelentést ezen a csomóponton "AvailableDisk" tulajdonság. Az azonos jelentéskészítői figyelheti a csomópont csatlakozási, jelentést ugyanazon a csomóponton "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események a megadott csomópont tekintendők. A SourceId együtt a tulajdonság egyedileg azonosítja az egészségügyi adatokat. |
| --állapota [szükséges] | Lehetséges értékek a következők\: "Érvénytelen", "Ok", "Warning", "Error", "Ismeretlen". |
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

## <a name="sfctl-cluster-select"></a>sfctl fürt kiválasztása
A kapcsolatot a Service Fabric-fürt végpontja.

Ha csatlakozni a biztonságos fürthöz, adja meg mindkét (.pem) abszolút elérési útját (.crt) tanúsítványt és kulcsot tartalmazó fájlt (.key) vagy akár egyetlen fájlt. Nem adható meg egyszerre. Ha egy biztonságos fürt szeretne csatlakozni, megadhat is hitelesítésszolgáltató kötegfájl vagy a megbízható CA-tanúsítványok könyvtár abszolút elérési utat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] végpont | A fürt végponti URL-cím, beleértve a port és a HTTP vagy HTTPS-előtagot. |
| – aad-ben | Azure Active Directory használata a hitelesítéshez. |
| --ca | Hitelesítésszolgáltatói tanúsítványok directory szerint érvényes kezelni vagy CA kötegfájl abszolút elérési útja |
| --tanúsítvány | Ügyfél-tanúsítvány fájl abszolút elérési útja. |
| --kulcs | Ügyfél-tanúsítvány kulcsfájl abszolút elérési útja. |
| --no-verify | Tiltsa le a tanúsítványok ellenőrzése a HTTPS használata esetén, vegye figyelembe\: Ez egy nem biztonságos beállítás, és nem használható termelési környezetben. |
| --pem | Az ügyféltanúsítványt, mint a .pem fájl abszolút elérési útja. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-unprovision"></a>sfctl fürt unprovision
Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt.

Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt. Külön-külön leépítése kód és a konfiguráció támogatott.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --code-version | A fürt kód csomag verziója. |
| --config-version | A fürtjegyzék verziója. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-upgrade"></a>sfctl fürtök frissítése
Indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése.

Érvényesítse a megadott frissítési paramétereket, és indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-health-map | JSON-kódolású szótár tartalmazó alkalmazás neve és a maximális százalék hiba kiváltása előtt nem kifogástalan. |
| – alkalmazás-típus-rendszerállapot-leképezés | JSON-kódolású szótár pár alkalmazás nevét, és nem kifogástalan hiba kiváltása előtt maximális százalék. |
| --code-version | A fürt kód verziója. |
| --config-version | A fürt konfigurációs verziója. |
| --delta-állapot-kiértékelésekor | Lehetővé teszi, hogy mindegyik frissítési tartományon befejezése után abszolút állapotának kiértékelését helyett különbözeti állapot kiértékelésekor. |
| --különbözeti nem kifogástalan csomópontokat | Az engedélyezett maximális százalékos aránya a csomópontok fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.  Alapértelmezett\: 10. <br><br> A különbözeti frissítés elején a csomópontok állapotát és a csomópontok az állapot kiértékelésekor időpontjában állapotának mérik. Az ellenőrzés minden frissítési tartomány frissítési művelet befejezését követően ellenőrizze, hogy a fürt globális állapotának megengedett határokon belül történik. |
| --failure-action | Lehetséges értékek a következők\: "Érvénytelen", "Visszaállítási", "Manual". |
| – force-újraindítás | Újraindítását kényszeríti ki. |
| --health-check-retry | Állapotfigyelő ellenőrizze újra időkorlát ezredmásodpercben. |
| – rendszerállapot-ellenőrzés-stabil | Állapotának ellenőrzése stabil időtartam ezredmásodpercben. |
| --állapot-ellenőrzés-várakozási | Állapotfigyelő ellenőrzés várakozási időtartama ezredmásodpercben. |
| --replica-set-check-timeout | Jelölőnégyzet időtúllépés másodpercben mért frissítési replika beállítása |
| – működés közbeni frissítés-mód | Lehetséges értékek a következők\: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "A figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| – a nem megfelelő alkalmazások | Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést. <br><br> Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez kiszámítása a nem megfelelő alkalmazások száma a teljes száma a fürtben, alkalmazások, amelyek szerepelnek az applicationtypehealthpolicymap paraméter hiányzó értékei alkalmazástípus kizárásának alkalmazáspéldányok keresztül. A számítási kerekít alkalmazások kis számú hiba működését. |
| --nem kifogástalan csomópontokat | Az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést. <br><br> Ahhoz, hogy 10 % a csomópontok sérült állapotot jelez, például ez az érték lenne 10. A százalékos arányát a csomópontokra, amelyeket nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. A százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, ha a rendszerállapot – figyelmeztetés értékeli. Az érték kiszámításához igény szerinti felosztásával sérült csomópontok száma a fürtben található csomópontok száma keresztül. A számítási csomópontok kis számú hiba tűrését kerekít. Nagy fürtök egyes csomópontok mindig lesz le, vagy ki a javításához, így ezen százalékos arány kell konfigurálni, amely elviselni. |
| --upgrade-domain-delta-unhealthy-nodes | Az engedélyezett maximális frissítési tartományi csomópontok százaléka fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.  Alapértelmezett\: 15. <br><br> A különbözeti mérik a frissítési tartományi csomópontok frissítés elején állapotát és a frissítési tartományi csomópontok az állapot kiértékelésekor időpontjában állapotát. Az ellenőrzés után minden frissítési tartomány frissítési befejezési összes frissítési tartományt kell győződjön meg arról, hogy a frissítési tartományok állapota megengedett határokon belül történik. |
| --frissítés-tartományi-időtúllépés | A frissítési tartomány időkorlátjának ezredmásodpercben. |
| --frissítés – időtúllépés | Frissítési időkorlát ezredmásodpercben. |
| --warning-as-error | Figyelmeztetés hibaként a azonos kiegészített kell kezelni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl fürt frissítése-folytatása
Ellenőrizze a fürt frissítése, lépjen tovább a következő frissítési tartományra.

A fürt kód vagy a konfigurációs frissítési lépés elvégzéséhez a a következő frissítési tartományra szükség.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --frissítési tartomány [szükséges] | A fürtök frissítése a következő frissítési tartományra. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl fürt frissítése-visszaállítás
Állítsa vissza a Service Fabric-fürt frissítését.

Állítsa vissza a Service Fabric-fürt kód vagy a konfiguráció frissítése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl fürt frissítése – állapot
Lekérdezi az aktuális Fürtfrissítés előrehaladását.

A folyamatban lévő fürtfelügyeleti frissítési az aktuálisan futó folyamatát lekérdezi. Ha nincs frissítés van folyamatban, az előző Fürtfrissítés utolsó állapotának beolvasására.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl fürt frissítés – frissítés
A Service Fabric-fürtök frissítése frissítési paramétereinek frissítése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-health-map | JSON-kódolású szótár tartalmazó alkalmazás neve és a maximális százalék hiba kiváltása előtt nem kifogástalan. |
| – alkalmazás-típus-rendszerállapot-leképezés | JSON-kódolású szótár pár alkalmazás nevét, és nem kifogástalan hiba kiváltása előtt maximális százalék. |
| --delta-állapot-kiértékelésekor | Lehetővé teszi, hogy mindegyik frissítési tartományon befejezése után abszolút állapotának kiértékelését helyett különbözeti állapot kiértékelésekor. |
| --különbözeti nem kifogástalan csomópontokat | Az engedélyezett maximális százalékos aránya a csomópontok fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.  Alapértelmezett\: 10. <br><br> A különbözeti frissítés elején a csomópontok állapotát és a csomópontok az állapot kiértékelésekor időpontjában állapotának mérik. Az ellenőrzés minden frissítési tartomány frissítési művelet befejezését követően ellenőrizze, hogy a fürt globális állapotának megengedett határokon belül történik. |
| --failure-action | Lehetséges értékek a következők\: "Érvénytelen", "Visszaállítási", "Manual". |
| – force-újraindítás | Újraindítását kényszeríti ki. |
| --health-check-retry | Állapotfigyelő ellenőrizze újra időkorlát ezredmásodpercben. |
| – rendszerállapot-ellenőrzés-stabil | Állapotának ellenőrzése stabil időtartam ezredmásodpercben. |
| --állapot-ellenőrzés-várakozási | Állapotfigyelő ellenőrzés várakozási időtartama ezredmásodpercben. |
| --replica-set-check-timeout | Jelölőnégyzet időtúllépés másodpercben mért frissítési replika beállítása |
| – működés közbeni frissítés-mód | Lehetséges értékek a következők\: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "A figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| – a nem megfelelő alkalmazások | Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést. <br><br> Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez kiszámítása a nem megfelelő alkalmazások száma a teljes száma a fürtben, alkalmazások, amelyek szerepelnek az applicationtypehealthpolicymap paraméter hiányzó értékei alkalmazástípus kizárásának alkalmazáspéldányok keresztül. A számítási kerekít alkalmazások kis számú hiba működését. |
| --nem kifogástalan csomópontokat | Az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést. <br><br> Ahhoz, hogy 10 % a csomópontok sérült állapotot jelez, például ez az érték lenne 10. A százalékos arányát a csomópontokra, amelyeket nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. A százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, ha a rendszerállapot – figyelmeztetés értékeli. Az érték kiszámításához igény szerinti felosztásával sérült csomópontok száma a fürtben található csomópontok száma keresztül. A számítási csomópontok kis számú hiba tűrését kerekít. Nagy fürtök egyes csomópontok mindig lesz le, vagy ki a javításához, így ezen százalékos arány kell konfigurálni, amely elviselni. |
| --upgrade-domain-delta-unhealthy-nodes | Az engedélyezett maximális frissítési tartományi csomópontok százaléka fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.  Alapértelmezett\: 15. <br><br> A különbözeti mérik a frissítési tartományi csomópontok frissítés elején állapotát és a frissítési tartományi csomópontok az állapot kiértékelésekor időpontjában állapotát. Az ellenőrzés után minden frissítési tartomány frissítési befejezési összes frissítési tartományt kell győződjön meg arról, hogy a frissítési tartományok állapota megengedett határokon belül történik. |
| --frissítés-tartományi-időtúllépés | A frissítési tartomány időkorlátjának ezredmásodpercben. |
| --frissítés-típusa | Lehetséges értékek a következők\: "Érvénytelen", "Folyamatban", "Rolling_ForceRestart".  Alapértelmezett\: működés közbeni. |
| --frissítés – időtúllépés | Frissítési időkorlát ezredmásodpercben. |
| --warning-as-error | Figyelmeztetés hibaként a azonos kiegészített kell kezelni. |

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