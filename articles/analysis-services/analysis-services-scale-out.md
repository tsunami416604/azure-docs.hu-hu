---
title: Az Azure Analysis Services horizontális felskálázása| Microsoft dokumentumok
description: Az Azure Analysis Services-kiszolgálók replikálása kibővített értékkel. Az ügyféllekérdezések ezután több lekérdezésreplikák között is eloszthatók egy kibővített lekérdezéskészletben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247992"
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services horizontális felskálázása

A horizontális felskálázás az ügyféllekérdezések között elosztható több *lekérdezési replikák* egy *lekérdezési készletben,* csökkenti a válaszidő nagy lekérdezési számítási feladatok során. A feldolgozást a lekérdezéskészlettől is elkülönítheti, így biztosítva, hogy a feldolgozási műveletek ne befolyásolják hátrányosan az ügyféllekérdezéseket. A horizontális felskálázás konfigurálható az Azure Portalon vagy az Analysis Services REST API használatával.

A horizontális felskálázás a standard tarifacsomag kiszolgálóihoz érhető el. Minden lekérdezésreplikát a kiszolgálóval megegyező díjtételként számlázunk. Az összes lekérdezési kópia ugyanabban a régióban jön létre, mint a kiszolgáló. A konfigurálható lekérdezési replikák számát a kiszolgáló által meghatározott régió korlátozza. További információ: [Elérhetőség régiónként](analysis-services-overview.md#availability-by-region). A horizontális felskálázás nem növeli a kiszolgáló számára rendelkezésre álló memória mennyiségét. A memória növeléséhez frissítenie kell a csomagot. 

## <a name="why-scale-out"></a>Miért skála ki?

Egy tipikus kiszolgálói telepítésben az egyik kiszolgáló feldolgozó- és lekérdezési kiszolgálóként is szolgál. Ha a kiszolgálón lévő modellekre leadott ügyféllekérdezések száma meghaladja a kiszolgáló tervéhez rendelt QPU-egységeket, vagy a modellfeldolgozás a nagy lekérdezési munkaterheléssel egy időben történik, a teljesítmény csökkenhet. 

A horizontális felskálázás segítségével legfeljebb hét további lekérdezési replika-erőforrással (összesen nyolc, az *elsődleges* kiszolgálóval együtt) rendelkező lekérdezéskészletet hozhat létre. A lekérdezési készletben lévő replikák számát a qpu-igények kritikus időpontokban történő teljesítéséhez méretezheti, és a feldolgozókiszolgálót bármikor elválaszthatja a lekérdezéskészlettől. 

A lekérdezéskészletben lévő lekérdezési replikák számától függetlenül a feldolgozási munkaterhelések nem kerülnek elosztásra a lekérdezésreplikák között. Az elsődleges kiszolgáló szolgál a feldolgozó kiszolgálóként. A lekérdezési replikák csak az elsődleges kiszolgáló és a lekérdezéskészlet egyes replika i. 

Horizontális felskálázáskor akár öt percet is igénybe vehet, amíg az új lekérdezésreplikák növekményesen hozzákerülnek a lekérdezéskészlethez. Ha az összes új lekérdezésreplikák futnak, az új ügyfélkapcsolatok terheléselosztása a lekérdezéskészlet erőforrásai között. A meglévő ügyfélkapcsolatok nem változnak attól az erőforrástól, amelyhez jelenleg csatlakoznak. A méretezéskor a lekérdezéskészletből eltávolított lekérdezéskészlet-erőforráshoz meglévő ügyfélkapcsolatok megszakadnak. Az ügyfelek újra csatlakozhatnak a fennmaradó lekérdezéskészlet-erőforráshoz.

## <a name="how-it-works"></a>Működés

A horizontális felskálázás első alkalommal történő konfigurálásakor az elsődleges kiszolgálón lévő modelladatbázisok *automatikusan* szinkronizálódnak az új lekérdezéskészlet új replikáival. Az automatikus szinkronizálás csak egyszer történik meg. Az automatikus szinkronizálás során az elsődleges kiszolgáló adatfájljai (a blobstorage-ban infóban titkosítva) egy második helyre kerülnek, amely szintén titkosítva van a blob storage-ban. A lekérdezéskészletben lévő replikák ezután *hidratáltak* a fájlok második készletéből származó adatokkal. 

Bár az automatikus szinkronizálás csak akkor történik meg, amikor először horizontális felskáláz egy kiszolgálót, manuális szinkronizálást is végrehajthat. A szinkronizálás biztosítja, hogy a lekérdezéskészletben lévő kópiák adatai megegyeznek az elsődleges kiszolgáló adataival. Amikor az elsődleges kiszolgálón feldolgozza (frissíti) a modelleket, a feldolgozási műveletek befejezése *után* szinkronizálást kell végrehajtani. Ez a szinkronizálás a frissített adatokat másolja az elsődleges kiszolgáló blob storage-fájljaiból a fájlok második készletébe. A lekérdezési készletben lévő replikák ezután hidratáltak a blobstorage-ban lévő fájlok második készletéből származó frissített adatokkal. 

Egy későbbi horizontális felskálázási művelet végrehajtásakor, például a replikák száma a lekérdezési készletben kettőről ötre, az új replikák hidratált adatokkal a második fájlkészlet blob storage.When performing a subsequent scale-out operation, for example, increasing the number of replicas in the query pool from two to five, the new replicas are hydrated with data from the second set of files in blob storage. Nincs szinkronizálás. Ha ezután a horizontális felskálázás után szinkronizálást hajt végre, a lekérdezéskészletben lévő új replikák kétszer hidratáltak – redundáns hidratálás. Egy későbbi horizontális felskálázási művelet végrehajtásakor fontos szem előtt tartani:

* A kibővített művelet *előtt* végezzen szinkronizálást a hozzáadott kópiák redundáns hidratálásának elkerülése érdekében. Az egyidejűleg futó egyidejű szinkronizálási és horizontális felskálázási műveletek nem engedélyezettek.

* A feldolgozási *és* a horizontális felskálázási műveletek automatizálásakor fontos, hogy először dolgozza fel az adatokat az elsődleges kiszolgálón, majd hajtsa végre a szinkronizálást, majd hajtsa végre a horizontális felskálázási műveletet. Ez a sorozat minimális hatást biztosít a QPU és a memória-erőforrások.

* A szinkronizálás akkor is engedélyezett, ha a lekérdezéskészletben nincsenek kópiák. Ha nulláról egy vagy több kópiára skálázódik az elsődleges kiszolgálón lévő feldolgozási művelet új adataival, először végezze el a szinkronizálást replikák nélkül a lekérdezéskészletben, majd horizontális felskálázást. A horizontális felskálázás előtt szinkronizálása elkerüli az újonnan hozzáadott replikák redundáns hidratálását.

* Ha töröl egy modelladatbázist az elsődleges kiszolgálóról, az nem törlődik automatikusan a lekérdezéskészlet kópiákból. A szinkronizálási műveletet a [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell paranccsal kell végrehajtania, amely eltávolítja az adatbázis fájljait/fájljait a kópia megosztott blobtárolási helyéről, majd törli a modelladatbázist a lekérdezéskészlet replikáiról. Annak megállapításához, hogy létezik-e modelladatbázis a lekérdezéskészlet kópiákon, de az elsődleges kiszolgálón nem, győződjön meg arról, hogy **a feldolgozókiszolgáló elkülönítése a készlet lekérdezésétől** **igen**. Ezután az SSMS segítségével csatlakozzon `:rw` az elsődleges kiszolgálóhoz a minősítő használatával, és nézze meg, hogy létezik-e az adatbázis. Ezután csatlakozzon a replikák a lekérdezési készlet csatlakoztatásával anélkül, hogy a `:rw` minősítő, hogy ha ugyanaz az adatbázis is létezik. Ha az adatbázis a lekérdezéskészlet replikáin található, de az elsődleges kiszolgálón nem, futtasson szinkronizálási műveletet.   

* Az elsődleges kiszolgálón lévő adatbázis átnevezésekor egy további lépésre van szükség annak biztosításához, hogy az adatbázis megfelelően szinkronizálva legyen a replikákkal. Átnevezés után végezzen szinkronizálást a [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) paranccsal, `-Database` amely megadja a paramétert a régi adatbázisnévvel. Ez a szinkronizálás eltávolítja az adatbázist és a régi nevű fájlokat a kópiákból. Ezután hajtson végre egy `-Database` másik szinkronizálást, amely megadja a paramétert az új adatbázisnévvel. A második szinkronizálás az újonnan elnevezett adatbázist másolja a fájlok második készletébe, és hidratálja a replikákat. Ezek a szinkronizálások nem hajthatók végre a portál modellszinkronizálásparancsával.

### <a name="synchronization-mode"></a>Szinkronizálási mód

Alapértelmezés szerint a lekérdezési replikák teljes mértékben rehidratáltak, nem növekményesen. A rehidratálás szakaszokban történik. Ezek levannak választva, és csatolva két egy időben (feltételezve, hogy legalább három replika) annak biztosítása érdekében, legalább egy replika online lekérdezések egy adott időpontban. Bizonyos esetekben előfordulhat, hogy az ügyfeleknek újra kell csatlakozniuk az egyik online replikához, amíg ez a folyamat zajlik. A (bemutatón) **ReplicaSyncMode** beállítás használatával most megadhatja, hogy a lekérdezésreplika szinkronizálása párhuzamosan történjen. A párhuzamos szinkronizálás a következő előnyökkel jár: 

- A szinkronizálási idő jelentős csökkenése. 
- A replikák közötti adatok nagyobb valószínűséggel konzisztensek a szinkronizálási folyamat során. 
- Mivel az adatbázisok a szinkronizálási folyamat során az összes replikán online állapotban vannak, az ügyfeleknek nem kell újra csatlakozniuk. 
- A memórián belüli gyorsítótár csak a módosított adatokkal frissül, ami gyorsabb lehet, mint a modell teljes újrahidratálása. 

#### <a name="setting-replicasyncmode"></a>ReplicaSyncMode beállítása

Az SSMS segítségével állítsa be a ReplicaSyncMode tulajdonságot a Speciális tulajdonságok ban. Lehetséges értékek: 

- `1`(alapértelmezett): Teljes replika adatbázis rehidratálás szakaszokban (növekményes). 
- `2`: Optimalizált szinkronizálás párhuzamosan. 

![RelicaSyncMode beállítás](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

A **ReplicaSyncMode=2**beállításakor attól függően, hogy a gyorsítótár mekkora részét kell frissíteni, a lekérdezésreplikák további memóriát is felemészthetnek. Ahhoz, hogy az adatbázis online állapotban maradjon, és a lekérdezések számára elérhető legyen, attól függően, hogy az adatok mekkora része változott meg, a művelet megduplázhatja a kópia *memóriáját,* mivel a régi és az új szegmensek egyszerre maradnak a memóriában. A replikacsomópontok memóriafoglalása megegyezik az elsődleges csomópontmemóriával, és általában több memória van az elsődleges csomóponton a frissítési műveletekhez, így nem valószínű, hogy a replikákból elfogyna a memória. Emellett a gyakori forgatókönyv az, hogy az adatbázis növekményesen frissül az elsődleges csomóponton, ezért a memória kétszeresének követelménye nem gyakori. Ha a Szinkronizálás művelet nem tapasztal memóriazavart, akkor újra megpróbálja az alapértelmezett technikával (kettő csatolása/leválasztása egyszerre). 

### <a name="separate-processing-from-query-pool"></a>A feldolgozás elkülönítése a lekérdezéskészlettől

A feldolgozási és lekérdezési műveletek maximális teljesítménye érdekében választhatja ki, hogy a feldolgozókiszolgálót elválasztja-e a lekérdezéskészlettől. Ha elválasztjuk őket, az új ügyfélkapcsolatok csak a lekérdezéskészletben lévő kópiák lekérdezési replikáihoz vannak rendelve. Ha a feldolgozási műveletek csak rövid időt vesznek igénybe, választhatja azt is, hogy a feldolgozókiszolgálót csak a feldolgozási és szinkronizálási műveletek végrehajtásához szükséges ideig válassza el a lekérdezési készlettől, majd vegye vissza a lekérdezéskészletbe. Ha elválasztja a feldolgozó kiszolgálót a lekérdezéskészlettől, vagy újra hozzáadja a lekérdezéskészletbe, a művelet befejeződése akár öt percet is igénybe vehet.

## <a name="monitor-qpu-usage"></a>QPU-használat figyelése

Annak megállapításához, hogy szükség van-e a kiszolgáló ravaló horizontális felskálázására, [figyelje a kiszolgálót az](analysis-services-monitor.md) Azure Portalon a Metrikák használatával. Ha a QPU rendszeresen maxes ki, ez azt jelenti, hogy a lekérdezések száma a modellek meghaladja a QPU korlátot a terv. A lekérdezéskészlet feladatvárólista hosszának metrikája is növekszik, ha a lekérdezési szálkészlet várólistájában lévő lekérdezések száma meghaladja a rendelkezésre álló QPU-t. 

Egy másik jó mérőszám nézni az átlagos QPU a ServerResourceType. Ez a metrika összehasonlítja az elsődleges kiszolgáló átlagos QPU-ját a lekérdezéskészlettel. 

![Lekérdezés horizontális felskálázási mutatói](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**A QPU konfigurálása a ServerResourceType segítségével**

1. A Metrikavonal-diagramon kattintson a **Metrika hozzáadása gombra.** 
2. Az **ERŐFORRÁS alkalmazásban**válassza ki a kiszolgálót, majd a **NÉVTÉR metrika**területen válassza az **Analysis Services szabványos mutatóit,** majd a **Metrikus**, a **QPU**, majd **az AGGREGATION**csoportban válassza **az Átlagos**lehetőséget. 
3. Kattintson **a Felosztás alkalmazása gombra.** 
4. A **VALUES (Értékek) mezőben**válassza a **ServerResourceType lehetőséget.**  

### <a name="detailed-diagnostic-logging"></a>Részletes diagnosztikai naplózás

Az Azure Monitor naplók használatával részletesebb diagnosztika a kibővített kiszolgáló erőforrásait. A naplók segítségével Log Analytics-lekérdezések segítségével kitörni QPU és a memória kiszolgáló és replika. További információ: példalekérdezések az [Analysis Services diagnosztikai naplózása című témakörben.](analysis-services-logging.md#example-queries)


## <a name="configure-scale-out"></a>Horizontális felskálázás konfigurálása

### <a name="in-azure-portal"></a>Az Azure Portalon

1. A portálon kattintson a **Horizontális felskálázás**gombra. A csúszkával válassza ki a lekérdezésreplika-kiszolgálók számát. A kiválasztott replikák száma a meglévő kiszolgálón kívül van.  

2. A **Feldolgozókiszolgáló és a lekérdezési készlet elkülönítése**csoportban válassza az igen lehetőséget a feldolgozókiszolgáló lekérdezési kiszolgálókból való kizárásához. Az alapértelmezett kapcsolati karakterláncot `:rw`használó [ügyfélkapcsolatok](#connections) (anélkül ) a rendszer átlesz irányítva a lekérdezéskészlet replikáihoz. 

   ![Kibővített csúszka](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kattintson a **Mentés** gombra az új lekérdezésreplika-kiszolgálók kiépítéséhez. 

A kiszolgáló rakoncátorának első konfigurálásakor az elsődleges kiszolgálómodelljei automatikusan szinkronizálódnak a lekérdezéskészletben lévő replikákkal. Az automatikus szinkronizálás csak egyszer történik meg, amikor először konfigurálja a horizontális felskálázást egy vagy több kópiára. Az ugyanazon a kiszolgálón lévő kópiák számának későbbi *módosításai nem váltanak ki újabb automatikus szinkronizálást.* Az automatikus szinkronizálás nem történik meg újra, még akkor sem, ha a kiszolgálót nullára állítja, majd ismét horizontális felskálázást végez tetszőleges számú kópiára. 

## <a name="synchronize"></a>Szinkronizálás 

A szinkronizálási műveleteket manuálisan vagy a REST API használatával kell végrehajtani.

### <a name="in-azure-portal"></a>Az Azure Portalon

Az **Áttekintés** > modell > **a Modell szinkronizálása című témakörben.**

![Kibővített csúszka](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Használja a **szinkronizálási** műveletet.

#### <a name="synchronize-a-model"></a>Modell szinkronizálása   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Szinkronizálási állapot beszerezni  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Visszáru-állapotkódok:


|Kód  |Leírás  |
|---------|---------|
|-1     |  Érvénytelen       |
|0     | Replikáló        |
|1     |  Rehidratáló       |
|2     |   Befejezve       |
|3     |   Sikertelen      |
|4     |    Véglegesítése     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell használata előtt [telepítse vagy frissítse a legújabb Azure PowerShell-modult.](/powershell/azure/install-az-ps) 

A szinkronizálás futtatásához használja a [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

A lekérdezési replikák számának beállításához használja a [Set-AzAnalysisServicesServer kiszolgálót.](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) Adja meg `-ReadonlyReplicaCount` a választható paramétert.

A feldolgozókiszolgáló és a lekérdezéskészlet elkülönítéséhez használja a [Set-AzAnalysisServicesServer programot.](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) Adja meg `-DefaultConnectionMode` a `Readonly`használni kívánt paramétert.

További információ: [Egyszerű szolgáltatás használata az Az.AnalysisServices modullal](analysis-services-service-principal.md#azmodule)című témakörben olvashat.

## <a name="connections"></a>Kapcsolatok

A kiszolgáló áttekintése lapon két kiszolgálónév található. Ha még nem konfigurálta a kibővített kiszolgálót, mindkét kiszolgálónév ugyanúgy működik. A kiszolgáló rakoncátalértékének konfigurálása után a kapcsolat típusától függően meg kell adnia a megfelelő kiszolgálónevet. 

Végfelhasználói ügyfélkapcsolatok, például Power BI Desktop, Excel és egyéni alkalmazások esetén használja a **Kiszolgáló nevét.** 

Az SSMS, a Visual Studio és a kapcsolati karakterláncok a PowerShell, az Azure Function-alkalmazások és az AMO, használja **a felügyeleti kiszolgáló nevét.** A felügyeleti kiszolgáló neve `:rw` tartalmaz egy speciális (írási és olvasási) minősítőt. Minden feldolgozási művelet az (elsődleges) felügyeleti kiszolgálón történik.

![Kiszolgálónevek](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Felskálázás, kicsinyített és kicsinyített

Módosíthatja a tarifacsomagot egy több replikával rendelkező kiszolgálón. Ugyanaz a tarifacsomag minden replikára vonatkozik. A skálázási művelet először egyszerre hozza le az összes replikát, majd az új tarifacsomag összes replikáját.

## <a name="troubleshoot"></a>Hibaelhárítás

**Probléma:** A felhasználók hibaüzenetet **kapnak:\<Nem található a kiszolgáló neve>" példány a "Csak olvasás" kapcsolatmódban.**

**Megoldás:** Amikor a **feldolgozókiszolgáló elkülönítése a lekérdezési készletről** lehetőséget választja, az alapértelmezett kapcsolati karakterláncot használó ügyfélkapcsolatok (anélkül `:rw`) átlesznek irányítva a lekérdezési készlet replikáihoz. Ha a lekérdezéskészlet replikái még nem online állapotban vannak, mert a szinkronizálás még nem fejeződött be, az átirányított ügyfélkapcsolatok sikertelenek lehetnek. A sikertelen kapcsolatok megakadályozása érdekében a szinkronizálás végrehajtásakor legalább két kiszolgálónak kell lennie a lekérdezéskészletben. Minden kiszolgáló külön-külön szinkronizálódik, míg mások online állapotban maradnak. Ha úgy dönt, hogy a feldolgozási kiszolgáló nem lesz a lekérdezési készletben a feldolgozás során, választhatja azt, hogy eltávolítja azt a készletből feldolgozásra, majd adja hozzá újra a készletbe a feldolgozás befejezése után, de a szinkronizálás előtt. A memória- és QPU-metrikák segítségével figyelheti a szinkronizálás állapotát.



## <a name="related-information"></a>Kapcsolódó információk

[Kiszolgálói mérőszámok figyelése](analysis-services-monitor.md)   
[Az Azure Analysis Services kezelése](analysis-services-manage.md) 
