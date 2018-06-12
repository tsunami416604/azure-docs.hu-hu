---
title: Az Azure diagnosztikai naplók |} Microsoft Docs
description: Ügyfél engedélyezheti a webhelynapló elemzése Azure CDN szolgáltatás használata.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.openlocfilehash: 98a7fc5c4607115811e17a7cf6acd4e867663833
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261304"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Az Azure diagnosztikai naplók egyszerűsített analitika tekintheti meg és mentse el azokat egy vagy több célt, beleértve:

 - Azure Storage-fiók
 - Azure Event Hubs
 - [A Naplóelemzési munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ez a szolgáltatás összes tarifacsomagok CDN-végpontok érhető el. 

Az Azure diagnosztikai naplóihoz alapvető a szoftverhasználati mérési adatok exportálása a CDN-végpontot különböző forrásokból, így képes felhasználni azokat egy egyedi módon. Például a következő típusú adatok exportálása teheti meg:

- A blob-tároló, Exportálás CSV-FÁJLBA és diagramjait létrehozása az Excel adatok exportálása.
- Adatok exportálása az Event Hubs, és más Azure-szolgáltatásokkal együtt összefüggéseket.
- Adatok exportálása saját Naplóelemzési munkaterület Naplóelemzési és nézet adatainak

Az alábbi ábrán egy tipikus CDN core analytics nézet adatok.

![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*1. ábra – CDN core analytics megtekintése*

Diagnosztikai naplók kapcsolatos további információkért lásd: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Az Azure-portálon a naplózás engedélyezése

Hajtsa végre ezeket a CDN egyszerűsített analitika naplózás lépéseket engedélyezése:

Jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nem rendelkezik engedélyezte a CDN a munkafolyamat [hozzon létre egy Azure CDN-profil és a végpont](cdn-create-new-endpoint.md) a folytatás előtt.

1. Az Azure-portálon lépjen a **CDN-profil**.

2. Az Azure portálon keresse meg a CDN-profil, vagy válasszon egyet az irányítópultról. Ezután válassza ki, amelynek a diagnosztikai naplók engedélyezni szeretné a CDN-végpont.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Válassza ki **diagnosztikai naplók** területén.

   A **diagnosztikai naplók** lap jelenik meg.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Az Azure Storage naplózásának engedélyezése

A naplók tárolásához a storage-fiókok használatához kövesse az alábbi lépéseket:
    
1. A **neve**, adjon meg egy nevet a diagnosztikai naplófájl-beállítások.
 
2. Válassza ki **tárfiókba archív**, majd jelölje be **CoreAnalytics**. 

2. A **megőrzés (nap)**, megőrzés (nap) számának kiválasztása. Egy nulla napos megőrzési határozatlan ideig tárolja a naplókat. 

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Válassza ki **tárfiók**.

    A **válasszon egy tárfiókot** lap jelenik meg.

4. A legördülő listából válasszon egy tárfiókot, majd válasszon **OK**.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Miután a diagnosztikai naplófájl-beállítások elvégzése után válassza ki **mentése**.

### <a name="logging-with-log-analytics"></a>A Naplóelemzési naplózás

A naplók tárolásához Naplóelemzési használatához kövesse az alábbi lépéseket:

1. Az a **diagnosztikai naplók** lapon jelölje be **küldeni a Naplóelemzési**. 

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Válassza ki **konfigurálása** Naplóelemzési naplózási. 

   A **OMS-munkaterület** lap jelenik meg.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Válassza ki **hozzon létre új munkaterületet**.

    A **OMS-munkaterület** lap jelenik meg.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/07_Create-new.png)

4. A **OMS-munkaterület**, írja be az OMS-munkaterület nevét. Az OMS-munkaterület nevét kell egyedinek lennie, és csak betűket, számokat és kötőjeleket tartalmazhat; szóközöket és aláhúzásjeleket tartalmazhat nem engedélyezettek. 

5. A **előfizetés**, a legördülő listából válassza ki a egy meglévő előfizetéshez. 

6. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévőt.

7. A **hely**, jelöljön ki egy helyet a listából.

8. Válassza ki **rögzítés az irányítópulton** Ha a naplózási konfiguráció menti az irányítópulton való rögzítéséhez. 

9. Válassza ki **OK** a konfigurálás befejezéséhez.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/08_Workspace-resource.png)

10. A munkaterület létrehozását követően visszatér a **diagnosztikai naplók** lap. Erősítse meg az új Naplóelemzési munkaterület nevét.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Válassza ki **CoreAnalytics**, majd jelölje be **mentése**.

12. Válassza ki, ha az új Naplóelemzési munkaterület **analytics alapvető** a CDN-végpont oldalról.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    A Naplóelemzési munkaterület adatokat naplózhatnak készen áll. Adatok felhasználásához, használjon egy [napló elemzési megoldások](#consuming-diagnostics-logs-from-a-log-analytics-workspace), az érintett a cikk későbbi részében.

Naplózási adatok késések kapcsolatos további információkért lásd: [adatok késések jelentkezzen](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>A PowerShell-lel naplózásának engedélyezése

A következő példa bemutatja, hogyan engedélyezése diagnosztikai naplók az Azure PowerShell-parancsmagok használatával.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>A storage-fiók engedélyezése diagnosztikai bejelentkezik

1. Jelentkezzen be, és válasszon egy előfizetést:

    Connect-AzureRmAccount 

    SELECT-AzureSubscription - előfizetés-azonosító 

2. A Storage-fiókok a diagnosztikai naplók engedélyezéséhez adja meg ezt a parancsot:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. A Naplóelemzési munkaterület diagnosztikai naplók engedélyezéséhez adja meg ezt a parancsot:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnosztikai naplók az Azure Storage felhasználása
Ez a szakasz ismerteti a sémája CDN egyszerűsített analitika, hogyan az Azure-tárfiók belül vannak rendezve, és egy CSV-fájlban a naplók letöltéséhez példakódot tartalmaz.

### <a name="using-microsoft-azure-storage-explorer"></a>A Microsoft Azure Tártallózó használatával
Az alapvető analitikai adatok az Azure-tárfiók próbál hozzáférni, először egy eszköz tárfiókokban tartalmához való hozzáféréshez. Eszközök is elérhetők több a piacon, amíg azt, amelyik ajánlott a Microsoft Azure Tártallózó. Az eszköz letöltéséhez keresse [Azure Tártallózó](http://storageexplorer.com/). Szoftver letöltése és telepítése a, miután konfigurálja úgy, hogy a CDN diagnosztikai naplókat a célként beállított ugyanazt az Azure storage a fiókot használja.

1.  Nyissa meg **Microsoft Azure Tártallózó**
2.  Keresse meg a storage-fiók
3.  Bontsa ki a **Blobtárolók** ezt a tárfiókot csomópontjából.
4.  Válassza ki a tárolót *insights-logs-coreanalytics*.
5.  Annak az eredménye megjelenítése fel a jobb oldali, mint az első szintű indítása *resourceId =*. Folytassa szintenként kijelölését, amíg meg nem látja a fájl *PT1H.json*. Tekintse meg a következőt *Blob elérési út formátumot* Megjegyzés: az elérési út magyarázatot.
6.  Minden egyes blob *PT1H.json* fájlt jelöli az elemzési naplókat egy órán át egy adott CDN-végpont vagy tartozó egyéni tartomány.
7.  A sémát a JSON-fájl tartalmának az alapvető analytics naplók szakasz sémája leírását.


> [!NOTE]
> **A BLOB elérési út formátuma**
> 
> Core analytics naplók óránként hozza létre, és az adatokat gyűjt, és tárolt egyetlen Azure blob-JSON-adatként. Mivel a tárolási explorer eszköz értelmezi "/" directory elválasztó és megjeleníti a hierarchia, elérési útját az Azure-blobot jelenik meg, hogy van-e olyan hierarchikus struktúra és a blob neve. A blob neve követi a következő elnevezés szabály szerint: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Mezők leírása:**

|Érték|Leírás|
|-------|---------|
|Előfizetés azonosítója    |Az Azure-előfizetés Guid formátumban azonosítója.|
|Erőforráscsoport neve |Az erőforráscsoport, amelybe a CDN-erőforrások tartoznak neve.|
|Profilnév |A CDN-profil neve|
|A végpont neve |A CDN-végpont neve|
|Év|  Az év, például 2017 négyjegyű ábrázolása|
|Hónap| A hónapok sorszáma kétjegyű ábrázolása. 01 január =... 12 decembert jelenti – =|
|Nap|   A hónap napját kétjegyű ábrázolása|
|PT1H.json| Az analitikai adatok tárolására tényleges JSON-fájl|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Az alapvető analitikai adatok exportálása CSV-fájlba

Egyszerűsített analitika hozzáférési megkönnyítése mintakód egy eszköz valósul meg. Ez az eszköz lehetővé teszi, hogy a JSON-fájlok letöltésére olyan egyszerű vesszővel tagolt fájl formátumra, amely diagramok vagy más összesítéseket létrehozására használható.

Ez az eszköz használatát:

1.  Látogasson el a github-hivatkozást: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Töltse le a kódot.
3.  Kövesse az utasításokat, lefordítása és konfigurálásához.
4.  Futtassa az eszközt.
5.  Az eredményül kapott CSV-fájl egy egyszerű strukturálatlan hierarchia analytics adatainak megjelenítése.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>A Naplóelemzési munkaterület a diagnosztikai naplók felhasználása
A Naplóelemzési az Azure-szolgáltatások, amely figyeli a felhőalapú és helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

Naplóelemzési használandó kell [naplózását](#enable-logging-with-azure-storage) az Azure Naplóelemzés munkaterületet, amely tárgyalt az ebben a cikkben.

### <a name="using-the-log-analytics-workspace"></a>A Naplóelemzési munkaterület használata

 Az alábbi ábrán látható, a tárház kimenetek és a bemenetek architektúrája:

![Log Analytics-munkaterület](./media/cdn-diagnostics-log/12_Repo-overview.png)

*3. ábra - napló Analytics tárház*

Az sokféleképpen megoldások használatával megjelenítheti az adatokat. Ezt úgy szerezheti be a felügyeleti megoldás a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Telepíthető megoldások Azure piactérről kiválasztásával a **most töltse le innen** hivatkozás az egyes megoldások alján.

### <a name="add-a-log-analytics-cdn-management-solution"></a>A napló Analytics CDN-kezelési megoldás hozzáadása

A lépések végrehajtásával adja hozzá a napló Analytics-kezelési megoldást:

1.   Jelentkezzen be az Azure-előfizetéshez az Azure portálra, majd nyissa meg az irányítópulton való rögzítéséhez.
    ![Az Azure irányítópult](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Az a **új** lap **piactér**, jelölje be **figyelés + felügyeleti**.

    ![Piactér](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Az a **figyelés + felügyeleti** lapon jelölje be **láthatja az összes**.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/15_See-all.png)

4. Keresse meg a CDN a Keresés mezőbe.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/16_Search-for.png)

5. Válassza ki **Azure CDN egyszerűsített analitika**. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Miután kiválasztotta a **létrehozása**, a rendszer felkéri hozzon létre egy új munkaterületet, vagy használjon egy meglévőt. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Válassza ki a munkaterületet előtt. Majd kell hozzáadnia az automation-fiók.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Az alábbi képernyőn látható az automatizálási fiók formában adja meg az. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/20_Automation.png)

9. Az automation-fiók létrehozását követően készen áll a megoldás hozzáadása. Válassza a **Létrehozás** gombot.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/21_Ready.png)

10. A megoldás most már fel lett véve a munkaterületen. Térjen vissza az Azure portál Irányítópultjára.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/22_Dashboard.png)

    Válassza ki a Naplóelemzési munkaterület létrehozott nyissa meg a munkaterületet. 

11. Válassza ki a **OMS-portálon** csempe az új megoldás megtekintéséhez.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/23_workspace.png)

12. A portál most már a következő képernyő hasonlóan kell kinéznie:

    ![Az összes megtekintése](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Válassza ki az áttekintőlapon megjeleníteni az adatokat több nézet egyikét.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/25_Interior-view.png)

    Tekintse meg az adatokat az egyes nézetek képviselő további csempék jobbra vagy balra görgetve. 

    Válasszon egyet a csempék a adatokkal kapcsolatos további részletek megtekintéséhez.

     ![Az összes megtekintése](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ajánlatok és tarifacsomagok

Ajánlatok és a felügyeleti megoldásokra tarifacsomagok [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Nézetek testreszabása

Testre szabhatja a nézet a adatokká használatával a **adatforrásnézet-tervezőből**. Megtervezésének első lépésként nyissa meg a Naplóelemzési munkaterületet, és válassza ki a **adatforrásnézet-tervezőből** csempére.

![Nézettervező](./media/cdn-diagnostics-log/27_Designer.png)

Fogd és vidd diagramok és adja meg az adatokat a részletesen meg szeretné.

![Nézettervező](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Naplózási adatok késleltetése

Az alábbi táblázatban láthatók a naplózási adatok késésének **Azure CDN Standard Microsoft**, **Azure CDN Standard Akamai**, és **Azure CDN Standard vagy prémium verizon**.

Microsoft naplózási adatok késleltetése | Verizon napló adatok késleltetése | Akamai napló adatok késleltetése
--- | --- | ---
1 órával később. | 1 órával késleltetett, és indítsa el a végpont-propagálás befejezését követően megjelenő 2 órát is igénybe vehet. | Késleltetett 24 óránként; Ha több mint 24 órája hozták létre, start megjelenő 2 órát vesz igénybe. Ha nemrég készült, start jelenik meg a naplófájlokat akár 25 órát is igénybe vehet.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnosztikai naplófájl típusokat CDN egyszerűsített analitika

A Microsoft jelenleg core analytics naplók csak, megjelenítő HTTP-válaszok statisztikai adatainak és a kimenő forgalom statisztika alapegységét a CDN POP/szélén a metrikák tartalmazó kínál.

### <a name="core-analytics-metrics-details"></a>Core analytics metrikák részletei
A következő táblázat érhető el az alapvető metrikák listáját tartalmazza az elemzési naplókat **Azure CDN Standard Microsoft**, **Azure CDN Standard Akamai**, és **Azure CDN Standard vagy Premium verizon**. Nem minden metrikák érhetők el minden szolgáltató, annak ellenére, hogy ezek az eltérések minimális. A tábla megjeleníti, hogy egy metrika olyan szolgáltató érhető el. A metrikák csak ezek CDN-végpontok, amelyek azokat a forgalom érhetők el.


|Metrika                     | Leírás | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Ebben az időszakban kérést találatok összesített száma | Igen | Igen |Igen |
| RequestCountHttpStatus2xx | Egy 2xx HTTP kódot (például 200, 202) eredményezett összes kérelem száma. | Igen | Igen |Igen |
| RequestCountHttpStatus3xx | Egy 3xx HTTP kódot (például 300, 302) eredményezett összes kérelem száma. | Igen | Igen |Igen |
| RequestCountHttpStatus4xx | Egy 4xx HTTP kódot (például 400, 404) eredményezett összes kérelem száma. | Igen | Igen |Igen |
| RequestCountHttpStatus5xx | 5xx HTTP kódot (például 500, 504) eredményezett összes kérelem száma. | Igen | Igen |Igen |
| RequestCountHttpStatusOthers | Minden más HTTP-kód (kívül 2xx-5xx) száma. | Igen | Igen |Igen |
| RequestCountHttpStatus200 | 200 HTTP kód válaszul eredményező összes kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus206 | A 206-os HTTP kód válaszul eredményező összes kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus302 | HTTP 302-es kód választ eredményező összes kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus304 | 304-es HTTP-kód választ eredményező összes kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus404 | HTTP 404-es kód választ eredményező összes kérelmek száma. | Igen | Nem  |Igen |
| RequestCountCacheHit | Elérte a gyorsítótár eredményező összes kérelmek száma. Az eszköz állítása és kiszolgálása között a POP-ről az ügyfélnek. | Igen | Igen | Nem  |
| RequestCountCacheMiss | A gyorsítótár-tévesztései eredményező összes kérelmek száma. A gyorsítótár-tévesztései azt jelenti, hogy az eszköz nem található meg az ügyfél legközelebb POP, és ezért be lett olvasva a forrásból. | Igen | Igen | Nem |
| RequestCountCacheNoCache | Az eszköz minden kérelemhez, amely megakadályozta a gyorsítótárba egy felhasználói konfiguráció az oldal miatt száma. | Igen | Igen | Nem |
| RequestCountCacheUncacheable | Az eszközökhöz, amely megakadályozza az eszköz a Cache-Control és Expires fejléc, amely jelzi, hogy azt nem gyorsítótárazza a POP- vagy HTTP-ügyfél által a gyorsítótárba összes kérelmek száma. | Igen | Igen | Nem |
| RequestCountCacheOthers | Gyorsítótár állapotú fent nem vonatkozik minden kérelmek száma. | Nem | Igen | Nem  |
| EgressTotal | Kimenő adatátvitel GB-ban | Igen |Igen |Igen |
| EgressHttpStatus2xx | Kimenő adatátviteli * a válaszok a 2xx HTTP-állapotkódok GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus3xx | Kimenő adatátvitel a válaszok a 3xx HTTP-állapotkódok GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus4xx | Kimenő adatátvitel a válaszok a 4xx HTTP-állapotkódok GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus5xx | Kimenő adatátvitel a válaszok a 5xx HTTP-állapotkódok GB-ban. | Igen | Igen | Nem |
| EgressHttpStatusOthers | Kimenő adatátvitel válaszok az egyéb HTTP-állapotkódok GB-ban. | Igen | Igen | Nem  |
| EgressCacheHit | Kimenő adatátvitel kapott válaszok közvetlenül a CDN-gyorsítótárból a CDN POP/szélén. | Igen | Igen | Nem |
| EgressCacheMiss. | Kimenő adatátvitel a válaszok nem található a legközelebbi POP-kiszolgálón, és olvassa be az eredeti kiszolgálóra. | Igen | Igen | Nem |
| EgressCacheNoCache | Kimenő adatátvitel eszközök, amely megakadályozta a felhasználói konfiguráció az oldal miatt a gyorsítótárba. | Igen | Igen | Nem |
| EgressCacheUncacheable | Kimenő adatátvitel eszközök, amelyek a rendszer megakadályozza az eszköz a Cache-Control vagy Expires fejléc által a gyorsítótárba. Azt jelzi, hogy azt nem gyorsítótárazza a POP vagy a HTTP-ügyfél. | Igen | Igen | Nem |
| EgressCacheOthers | Kimenő adatátvitel más gyorsítótár forgatókönyvek esetén. | Nem | Igen | Nem |

* Kimenő forgalom CDN POP-ra kiszolgálókról kézbesítve lenne az ügyfél hivatkozik.


### <a name="schema-of-the-core-analytics-logs"></a>A core analytics naplók séma 

Összes napló JSON formátumban vannak tárolva, és mindegyik bejegyzés rendelkezik a karakterlánc típusú a következő séma szerint:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Ha *idő* az óra határ, amelynek a statisztikáit jelentett a kezdési időt jelenti. Amikor metrika nem támogatott a CDN-szolgáltató helyett egy double vagy egész szám, a rendszer null értékű. A null érték metrika jelezné, és eltér a 0 értéket. Nincs a végponthoz tartományonként metrikákat egy készletét.

Példa tulajdonságai:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>További források

* [Az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Egyszerűsített analitika Azure CDN kiegészítő portálon keresztül](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Az Azure Naplóelemzés REST API-n](https://docs.microsoft.com/rest/api/loganalytics)







