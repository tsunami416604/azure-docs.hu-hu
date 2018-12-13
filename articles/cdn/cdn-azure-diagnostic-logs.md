---
title: Az Azure diagnosztikai naplók |} A Microsoft Docs
description: Ügyfél engedélyezheti a naplóelemzés az Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: 2beb5aa7218df25a1af87411d9481d30b259c353
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256729"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Az Azure diagnosztikai naplók, a core analytics tekintheti meg és mentse a fájlokat egy vagy több célhelyre többek között:

 - Azure Storage-fiók
 - Azure Event Hubs
 - [Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ez a funkció a CDN-végpontok minden árképzési szint esetében érhető el. 

Azure-beli diagnosztikai naplók alapvető használati metrikák exportálhat a CDN-végpontra többféle forrásból, így felhasználhatja őket egy személyre szabott módon teszi lehetővé. Például a következő típusú adatok exportálása teheti:

- A blob storage-, csv-fájlba exportálása és diagramjait létrehozni az Excel adatok exportálása.
- Adatok exportálása az Event hubs szolgáltatásba, és az egyéb Azure-szolgáltatásokból származó adatokkal.
- Adatok exportálása a saját Log Analytics-munkaterület az adatok Log Analytics és megtekintése

A következő ábrán egy tipikus CDN core analytics adatok nézetét.

![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*1. ábra – CDN core analytics megtekintése*

Diagnosztikai naplók kapcsolatos további információkért lásd: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Az Azure Portallal naplózás engedélyezése

Kövesse ezeket a CDN – egyszerűsített analitika naplózás lépéseket engedélyezése:

Jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha nincs már engedélyezte a CDN a munkafolyamathoz [Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md) a folytatás előtt.

1. Az Azure Portalon lépjen **CDN-profil**.

2. Az Azure Portalon keresse meg a CDN-profil, vagy válasszon ki egy, az irányítópulton. Ezután válassza ki a CDN-végpontot, amelyhez szeretné engedélyezni, diagnosztikai naplók.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Válassza ki **diagnosztikai naplók** területén belül.

   A **diagnosztikai naplók** lap jelenik meg.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Az Azure Storage naplózás engedélyezése

A naplók tárolásához egy storage-fiók használatához kövesse az alábbi lépéseket:
    
1. A **neve**, adjon meg egy nevet a diagnosztikai naplót beállítások.
 
2. Válassza ki **archiválás tárfiókba**, majd **CoreAnalytics**. 

2. A **megőrzés (nap)**, válassza ki a megőrzési napok számát. Egy nulla napnyi adatmegőrzéshez határozatlan ideig tárolja a naplókat. 

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Válassza ki **tárfiók**.

    A **válassza ki a tárfiókot** lap jelenik meg.

4. Jelöljön ki egy storage-fiókot a legördülő listából, majd **OK**.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Miután befejezte a diagnosztikai napló beállításait, válassza ki a **mentése**.

### <a name="logging-with-log-analytics"></a>A Log Analytics-naplózás

A naplók tárolására a Log Analytics használatához kövesse az alábbi lépéseket:

1. Az a **diagnosztikai naplók** lapon jelölje be **Küldés a Log Analyticsnek**. 

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Válassza ki **konfigurálása** Log Analytics naplózási. 

   A **Log Analytics-munkaterületek** lap jelenik meg.

    >[!NOTE] 
    >Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Válassza ki **új munkaterület létrehozása**.

    A **Log Analytics-munkaterület** lap jelenik meg.

    >[!NOTE] 
    >Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/07_Create-new.png)

4. A **Log Analytics-munkaterület**, a Log Analytics munkaterület nevét kötelező megadni. A Log Analytics-munkaterület neve kell egyedinek lennie, és csak betűket, számokat és kötőjeleket tartalmazhat; tartalmazhat szóközöket és aláhúzásjeleket tartalmazhat nem engedélyezettek. 

5. A **előfizetés**, válasszon ki egy meglévő előfizetést a legördülő listából. 

6. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.

7. A **hely**, válasszon ki egy helyet a listából.

8. Válassza ki **rögzítés az irányítópulton** , ha a naplózási konfiguráció menti az irányítópultján. 

9. Válassza ki **OK** konfigurálásának befejezéséhez.

10. A munkaterület létrehozását követően visszatér a **diagnosztikai naplók** lapot. Erősítse meg az új Log Analytics-munkaterület nevét.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Válassza ki **CoreAnalytics**, majd **mentése**.

12. Az új Log Analytics-munkaterület megtekintéséhez jelölje ki **Core analytics** a CDN-végpont oldaláról.

    ![Portál – diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    A Log Analytics-munkaterülethez már készen áll az adatok. Annak érdekében, hogy felhasználhatják az adatokat, használjon egy [Log Analytics megoldás](#consuming-diagnostics-logs-from-a-log-analytics-workspace), az érintett a cikk későbbi részében.

Naplózási adatok késések kapcsolatos további információkért lásd: [adatok késések jelentkezzen](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Naplózás engedélyezése a PowerShell-lel

Az alábbi példa bemutatja, hogyan engedélyezze a diagnosztikát az Azure PowerShell-parancsmagok használatával.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>A storage-fiókban engedélyezése diagnosztikai naplók

1. Jelentkezzen be, és válasszon ki egy előfizetést:

    Connect-AzureRmAccount 

    SELECT-AzureSubscription - SubscriptionId 

2. Diagnosztikai naplók engedélyezése a Storage-fiókban, adja meg ezt a parancsot:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Ahhoz, hogy a diagnosztikai naplók a Log Analytics-munkaterületen, adja meg ezt a parancsot:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Az Azure Storage-ból a felhasználó diagnosztikai naplók
Ez a szakasz ismerteti a CDN – egyszerűsített analitika, sémája alapjait az Azure storage-fiók van rendezve, és milyen biztosít a mintakód a naplók a CSV-fájl letöltéséhez.

### <a name="using-microsoft-azure-storage-explorer"></a>A Microsoft Azure Storage Explorer használatával
Próbál hozzáférni a core analytics-adatok egy Azure storage-fiókból, szüksége lesz egy olyan eszközt, a tartalma a storage-fiók eléréséhez. Noha több eszközt érhető el a piacon, azt, amelyik javasoljuk, hogy a Microsoft Azure Storage Explorer. Az eszköz letöltéséhez keresse [Azure Storage Explorer](http://storageexplorer.com/). Szoftver letöltése és telepítése a, után konfigurálja úgy, hogy a CDN-diagnosztikai naplók a célként beállított ugyanazt az Azure storage-fiókot használja.

1.  Nyissa meg **a Microsoft Azure Storage Explorerrel**
2.  Keresse meg a storage-fiók
3.  Bontsa ki a **Blobtárolók** csomópont alatt ezt a tárfiókot.
4.  Válassza ki a tárolót nevű *insights-logs-coreanalytics*.
5.  Eredmények megjelenítése fel a jobb oldali ablaktáblán, mint az első szintű, kezdve a *resourceId =*. Folytassa az egyes fenyegetési kijelölését, amíg meg nem találja a fájl *PT1H.json*. Az elérési út egy ismertetése: [Blob elérési út formátuma](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Minden egyes blob *PT1H.json* fájlt jelöli az elemzési naplók egy adott CDN-végpont vagy az egyéni tartomány egy órán keresztül.
7.  A séma a JSON-fájl tartalmát, a core analytics naplók szakasz sémája ismerteti.


#### <a name="blob-path-format"></a>A BLOB elérési út formátuma

Alapvető elemzési naplók óránként jönnek létre, és az adatokat gyűjt, és a egy Azure blob-belül tárolt JSON-adattartalomként. Mivel a Storage explorer eszköz értelmezi (/) directory elválasztó és jeleníti meg a hierarchiában, az Azure blob elérési útja jelenik meg, ha van olyan hierarchikus struktúra és a blob nevét jelöli. A blob nevét, az alábbi elnevezési konvenciót követi:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Mező leírása:**

|Érték|Leírás|
|-------|---------|
|Előfizetés azonosítója    |Az Azure-előfizetés Guid formátumú azonosítója.|
|Erőforráscsoport neve |Az erőforrás nevét, amely a CDN-erőforrások tartoznak.|
|Profilnév |A CDN-profil neve|
|Végpont neve |A CDN-végpont neve|
|Év|  Az év, például 2017 négyjegyű ábrázolása|
|Hónap| A hónapok sorszáma kétjegyű ábrázolása. 01 január =... 12. December =|
|Nap|   A hónap napját kétjegyű ábrázolása|
|PT1H.json| Tényleges JSON-fájlt az elemzési adatok tárolására|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>A core analytics-adatok exportálása CSV-fájlba

Hogy megkönnyítse a core analytics eléréséhez, egy eszköz bemutató kódminta biztosítunk. Ez az eszköz lehetővé teszi, hogy a JSON-fájlok letöltésével egy egyszerű vesszővel tagolt fájl formátumra, amely diagramokat vagy más összesítéseket létrehozására használható.

Itt látható, hogyan használhatja az eszközt:

1.  Tekintse meg a GitHub-hivatkozás: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Töltse le a kódot.
3.  Kövesse az utasításokat fordításához és konfigurálása.
4.  Futtassa az eszközt.
5.  Az eredményül kapott CSV-fájl az elemzési adatok bemutatja egy egyszerű, egybesimított hierarchiában.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>A Log Analytics-munkaterület fogyasztó diagnosztikai naplók
Log Analytics az Azure-szolgáltatás figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

A Log Analytics használatához be kell [naplózás engedélyezése](#enable-logging-with-azure-storage) a Azure Log Analytics-munkaterületet, amely a következő cikkben a cikk korábbi részében.

### <a name="using-the-log-analytics-workspace"></a>A Log Analytics munkaterület használata

 Az alábbi ábrán látható, az architektúra a bemeneti adatok és a tárház kimenetek:

![Log Analytics-munkaterület](./media/cdn-diagnostics-log/12_Repo-overview.png)

*3. ábra – Log Analytics-adattárhoz*

Felügyeleti megoldások használatával egy számos különböző módon képes megjeleníteni az adatokat. A felügyeleti megoldások szerezheti be a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Telepítheti az eszközfelügyeleti megoldások Azure marketplace-ről kiválasztásával a **Letöltés most** hivatkozás az egyes megoldások a lap alján.

### <a name="add-a-log-analytics-cdn-management-solution"></a>Log Analytics CDN felügyeleti megoldás hozzáadása

Adja hozzá a Log Analytics felügyeleti megoldás az alábbi lépéseket követve:

1.   Az Azure Portalon az Azure-előfizetés használatával jelentkezzen be, és nyissa meg az irányítópultot.
    ![Azure-irányítópulton](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Az a **új** lap **Marketplace**válassza **figyelés + felügyelet**.

    ![Piactér](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Az a **figyelés + felügyelet** lapon jelölje be **összes**.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/15_See-all.png)

4. Keresse meg a CDN a keresőmezőbe.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/16_Search-for.png)

5. Válassza ki **az Azure CDN – egyszerűsített analitika**. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Miután kiválasztotta **létrehozás**, a rendszer felkéri hozzon létre egy új Log Analytics-munkaterületet, vagy használjon egy meglévőt. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Válassza ki a előtt létrehozott munkaterülethez. Majd hozzá kell egy automation-fiókot.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/19_Add-automation.png)

8. A következő képernyőn látható az automation-fiók ki kell töltenie űrlap. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/20_Automation.png)

9. Az automation-fiók létrehozását követően készen áll a megoldás hozzáadása. Válassza a **Létrehozás** gombot.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/21_Ready.png)

10. A megoldás már hozzá van adva a munkaterülethez. Térjen vissza az Azure portal Irányítópultjára.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/22_Dashboard.png)

    Válassza ki a létrehozott nyissa meg a munkaterületet a Log Analytics-munkaterületet. 

11. Válassza ki a **OMS-portálon** csempére kattintva tekintse meg az új megoldásokat.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/23_workspace.png)

12. A portál a következő képernyőhöz hasonlóan kell kinéznie:

    ![Az összes megtekintése](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Válassza ki az egyik csempét az adatok számos nézet megtekintéséhez.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/25_Interior-view.png)

    További csempék jelölő egyéni nézeteket az adatokban, hogy jobbra vagy balra görgetve. 

    Válassza ki az egyik csempét az adatokkal kapcsolatos további részletek megtekintéséhez.

     ![Az összes megtekintése](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Az ajánlatok és tarifacsomagok

Láthatja, ajánlatokat és felügyeleti megoldásokat tarifacsomagok [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Nézetek testreszabása

Testre szabhatja a nézetet az adatok használatával a **adatforrásnézet-tervezőből**. Tervezési a kezdéshez nyissa meg a Log Analytics-munkaterületet, és válassza a **adatforrásnézet-tervezőből** csempére.

![Nézettervező](./media/cdn-diagnostics-log/27_Designer.png)

És húzza a típusú diagramok, és adja meg az adatokat részletesen meg szeretnék elemezni.

![Nézettervező](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Naplózási adatok késések

Az alábbi táblázatban láthatók a napló adatokat késésének **Azure CDN Standard a Microsoft**, **Azure CDN Akamai Standard**, és **Azure CDN Standard/prémium verizon**.

Microsoft naplózási adatok késések | Verizon naplózási adatok késések | Akamai naplózási adatok késések
--- | --- | ---
1 órával később. | Késnek-e 1 óra, és indítsa el a végpont-propagálás befejezését követően megjelenő akár 2 órát is igénybe vehet. | Késleltetett 24 órával; Ha létrehozták a több mint 24 órával ezelőtt, elindításához megjelenő akár 2 órát vesz igénybe. Ha nemrég készült, akár 25 órát elindításához jelennek meg a naplókat is eltarthat.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN – egyszerűsített analitika diagnosztikai napló típusa

A Microsoft jelenleg kínál alapvető elemzési naplók, mérőszámok HTTP-válaszok statisztikai adatainak és a kimenő forgalom statisztikák látható módon, a CDN POP-k/élek tartalmaznak.

### <a name="core-analytics-metrics-details"></a>Core analytics metrikák részletei
Az alábbi táblázat az alapvető elérhető metrikáinak listája látható az elemzési naplók **Azure CDN Standard a Microsoft**, **Azure CDN Akamai Standard**, és **Standard/prémium szintű Azure CDN a Verizontól**. Nem minden metrikák érhetők el minden szolgáltató, bár ezek a különbségek minimálisak. A tábla szintén elérhető-e egy metrika szolgáltatói jeleníti meg. Csak ezek CDN-végpontok, amelyeken az adatforgalom őket a metrikák érhetők el.


|Metrika                     | Leírás | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Ebben az időszakban kérést találatok teljes száma. | Igen | Igen |Igen |
| RequestCountHttpStatus2xx | Minden a HTTP-kód (Ha például 200-as, 202) a 2xx eredményező kérések száma. | Igen | Igen |Igen |
| RequestCountHttpStatus3xx | Minden a HTTP-kód (Ha például 300, 302) egy 3xx eredményező kérések száma. | Igen | Igen |Igen |
| RequestCountHttpStatus4xx | Minden a HTTP-kód (például a 400-as, a 404-es) egy 4xx eredményező kérések száma. | Igen | Igen |Igen |
| RequestCountHttpStatus5xx | Minden (például 500-as, 504) egy 5XX kódú HTTP-kód a eredményező kérelmek száma. | Igen | Igen |Igen |
| RequestCountHttpStatusOthers | Minden más HTTP-kód (kívül 2xx-5xx) száma. | Igen | Igen |Igen |
| RequestCountHttpStatus200 | Minden, a HTTP 200 kód választ eredményező kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus206 | Az összes, a 206-os HTTP-kód választ eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus302 | Az összes HTTP 302 kód választ eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus304 | Minden, a 304-es HTTP-kód választ eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus404 | Az összes kérelem a 404-es HTTP-kód választ eredményező száma. | Igen | Nem  |Igen |
| RequestCountCacheHit | Nyomja le az összes, a gyorsítótár eredményező kérelmek száma. Az eszköz az ügyfél közvetlenül a jelenléti pontra Irányíthatja a kiszolgálása. | Igen | Igen | Nem  |
| RequestCountCacheMiss | Minden, a gyorsítótár-tévesztés eredményező kérelmek száma. Gyorsítótár-tévesztés azt jelenti, az eszköz nem található a jelenléti pont az ügyfél legközelebb a, és ezért a forrásból beolvasott. | Igen | Igen | Nem |
| RequestCountCacheNoCache | Az eszköz minden kérést, ami megakadályozta a peremhálózaton egy felhasználói konfiguráció miatt a gyorsítótárba száma. | Igen | Igen | Nem |
| RequestCountCacheUncacheable | Minden kérelemhez, amely ebben az esetben a gyorsítótárba helyezésből parancsra az eszköz Cache-Control és Expires fejlécek, amely jelzi, hogy azt nem gyorsítótárazza a csatlakozási pont vagy a HTTP-alapú eszközök száma. | Igen | Igen | Nem |
| RequestCountCacheOthers | A gyorsítótár állapot szerint a fent nem említett összes kérelem száma. | Nem | Igen | Nem  |
| EgressTotal | Kimenő adatforgalom GB-ban | Igen |Igen |Igen |
| EgressHttpStatus2xx | Kimenő adatok átviteli * jelöli a 2xx HTTP-állapotkódok GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus3xx | A válaszok 3xx HTTP-állapotkódok GB-ban a kimenő adatforgalom. | Igen | Igen | Nem  |
| EgressHttpStatus4xx | Kimenő adatforgalom a válaszok a 4xx HTTP-állapotkódok GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus5xx | Válaszok az 5XX kódú HTTP-állapotkódok GB kimenő adatforgalom. | Igen | Igen | Nem |
| EgressHttpStatusOthers | A válaszok más HTTP-állapotkódok GB-ban a kimenő adatforgalom. | Igen | Igen | Nem  |
| EgressCacheHit | Kimenő adatforgalom a válaszok, amelyek kézbesítése közvetlenül a CDN-gyorsítótárból a CDN POP-k és élek. | Igen | Igen | Nem |
| EgressCacheMiss. | Kimenő adatforgalom a válaszok nem található a legközelebbi POP-kiszolgálón, és a forrás-kiszolgálóról való lekérdezés. | Igen | Igen | Nem |
| EgressCacheNoCache | Kimenő adatforgalom az eszközök, amelyek ebben az esetben a gyorsítótárba helyezésből parancsra a peremhálózaton egy felhasználói konfiguráció miatt. | Igen | Igen | Nem |
| EgressCacheUncacheable | Kimenő adatforgalom az eszközök, amelyek ebben az esetben az eszköz Cache-Control és/vagy a lejárat fejlécek alapján a gyorsítótárba. Azt jelzi, hogy azt nem gyorsítótárazza a POP- vagy HTTP-ügyfél által. | Igen | Igen | Nem |
| EgressCacheOthers | Kimenő adatátvitel más gyorsítótár-forgatókönyvekhez. | Nem | Igen | Nem |

* Kimenő adatátviteli forgalom CDN POP-kiszolgálókról továbbítja az ügyfélnek hivatkozik.


### <a name="schema-of-the-core-analytics-logs"></a>A core analytics naplók sémája 

Az összes napló JSON formátumban vannak tárolva, és mindegyik bejegyzés rendelkezik karakterlánc típusú mezők alapján a következő mintát követik:

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

Ahol *idő* az óra határ, amelynek a statisztikáit jelentett kezdési idejét jelzi. Ha egy metrika használata nem támogatott egy CDN-szolgáltató helyett a dupla vagy egész érték, nincs null értékű. A null érték érhető el a metrika azt jelzi, és eltér a 0 értéket. Nincs a végponthoz tartományonként ezeket a metrikákat egy készletét.

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
* [Egyszerűsített analitika az Azure CDN-t kiegészítő portálon keresztül](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Az Azure Log Analytics REST API-val](https://docs.microsoft.com/rest/api/loganalytics)







