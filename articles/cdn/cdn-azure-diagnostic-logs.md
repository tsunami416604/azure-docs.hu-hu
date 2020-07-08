---
title: Azure diagnosztikai naplók | Microsoft Docs
description: Az ügyfél engedélyezheti Azure CDN naplózási elemzését.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 2c432b28250dca382f69a992de73d633b5ea45b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84883993"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Az Azure diagnosztikai naplóival megtekintheti az alapvető elemzéseket, és egy vagy több célhelyre mentheti őket, beleértve a következőket:

 - Azure Storage-fiók
 - Azure Event Hubs
 - [Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ez a funkció minden díjszabási szinten elérhető CDN-végpontokon. 

Az Azure Diagnostics-naplók lehetővé teszik az alapszintű használati metrikák exportálását a CDN-végpontról a különböző forrásokra, így testreszabott módon használhatja azokat. Például a következő típusú adatexportálást végezheti el:

- Az adatexportálás blob Storage-ba, exportálás CSV-be és diagramok készítése az Excelben.
- Adatok exportálása Event Hubs és más Azure-szolgáltatásokból származó adatokkal.
- Adatexportálás Azure Monitor naplókba és a saját Log Analytics-munkaterületen tárolt adatmegjelenítés

Az alábbi ábrán egy tipikus CDN Core Analytics-nézet látható.

![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*1. ábra – CDN Core Analytics-nézet*

További információ a diagnosztikai naplókról: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Az Azure Portallal való bejelentkezés engedélyezése

Az alábbi lépéseket követve engedélyezze a naplózást a CDN Core Analytics használatával:

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem engedélyezte a CDN használatát a munkafolyamathoz, a folytatás előtt [hozzon létre egy Azure CDN profilt és végpontot](cdn-create-new-endpoint.md) .

1. A Azure Portal navigáljon a **CDN-profilhoz**.

2. A Azure Portal keressen egy CDN-profilt, vagy válasszon egyet az irányítópulton. Ezután válassza ki azt a CDN-végpontot, amelyhez engedélyezni szeretné a diagnosztikai naplókat.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. A figyelés szakaszban válassza a **diagnosztikai naplók** lehetőséget.

   Megjelenik a **diagnosztikai naplók** lap.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Naplózás engedélyezése az Azure Storage-ban

Ha Storage-fiókot szeretne használni a naplók tárolásához, kövesse az alábbi lépéseket:
    
1. A **név**mezőben adja meg a diagnosztikai napló beállításainak nevét.
 
2. Válassza az **archiválás egy Storage-fiókba**lehetőséget, majd válassza a **CoreAnalytics**lehetőséget. 

2. A **megőrzési idő (nap)** mezőben adja meg a megőrzési napok számát. A nulla nap megőrzése határozatlan ideig tárolja a naplókat. 

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Válassza a **Storage-fiók**lehetőséget.

    Megjelenik a **Storage-fiók kiválasztása** lap.

4. Válassza ki a Storage-fiókot a legördülő listából, majd kattintson **az OK gombra**.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Miután befejezte a diagnosztikai napló beállításait, válassza a **Mentés**lehetőséget.

### <a name="logging-with-azure-monitor"></a>Naplózás az Azure Monitorral

Ha a naplókat a Azure Monitor használatával szeretné tárolni, kövesse az alábbi lépéseket:

1. A **diagnosztikai naplók** lapon válassza a **Küldés log Analyticsba**lehetőséget. 

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Azure Monitor naplózás konfigurálásához válassza a **Konfigurálás** lehetőséget. 

   Megjelenik a **log Analytics munkaterületek** lap.

    >[!NOTE] 
    >Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Válassza az **Új munkaterület létrehozása**lehetőséget.

    Megjelenik a **log Analytics munkaterület** lap.

    >[!NOTE] 
    >Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/07_Create-new.png)

4. **Log Analytics munkaterületen**adja meg a log Analytics-munkaterület nevét. A Log Analytics munkaterület nevének egyedinek kell lennie, és csak betűket, számokat és kötőjeleket tartalmazhat. szóközök és aláhúzások használata nem engedélyezett. 

5. Az **előfizetés**mezőben válasszon ki egy meglévő előfizetést a legördülő listából. 

6. **Erőforráscsoport**esetén hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.

7. A **hely**mezőben válasszon ki egy helyet a listából.

8. Válassza a **rögzítés az irányítópulton** lehetőséget, ha menteni szeretné a napló konfigurációját az irányítópulton. 

9. A konfiguráció befejezéséhez kattintson **az OK gombra** .

10. A munkaterület létrehozása után a rendszer visszaadja a **diagnosztikai naplók** lapját. Erősítse meg az új Log Analytics munkaterület nevét.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Válassza a **CoreAnalytics**, majd a **Mentés**lehetőséget.

12. Az új Log Analytics munkaterület megtekintéséhez válassza az **alapvető elemzés** lehetőséget a CDN-végpont oldaláról.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Az Log Analytics munkaterület most már készen áll az adatnaplózásra. Az ilyen adatfelhasználás érdekében a jelen cikk későbbi részében ismertetett [Azure monitor naplók megoldását](#consuming-diagnostics-logs-from-a-log-analytics-workspace)kell használnia.

További információ a naplózási adatok késéséről: [adatok naplózása késések](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Naplózás engedélyezése a PowerShell-lel

Az alábbi példa bemutatja, hogyan engedélyezheti a diagnosztikai naplókat a Azure PowerShell-parancsmagok segítségével.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Diagnosztikai naplók engedélyezése egy Storage-fiókban

1. Jelentkezzen be, és válasszon egy előfizetést:

    Kapcsolat – AzAccount 

    Select-Azuresubscription parancsot-SubscriptionId 

2. Ha egy Storage-fiókban szeretné engedélyezni a diagnosztikai naplókat, írja be a következő parancsot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. A diagnosztikai naplók engedélyezéséhez Log Analytics munkaterületen írja be a következő parancsot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnosztikai naplók felhasználása az Azure Storage-ből
Ez a szakasz ismerteti a CDN Core Analytics sémáját, az Azure Storage-fiókban való elrendezését, valamint a naplófájlok CSV-fájlban való letöltéséhez szükséges mintát.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer használata
Ahhoz, hogy hozzáférhessen az Azure Storage-fiókhoz tartozó alapvető elemzési adatokhoz, először egy eszközre van szüksége a Storage-fiók tartalmának eléréséhez. Habár a piacon számos eszköz érhető el, az ajánlott Microsoft Azure Storage Explorer. Az eszköz letöltéséhez lásd: [Azure Storage Explorer](https://storageexplorer.com/). A szoftver letöltését és telepítését követően konfigurálja úgy, hogy ugyanazt az Azure Storage-fiókot használja, amelyet a CDN diagnosztikai naplói célhelyként konfigurált.

1.  **Microsoft Azure Storage Explorer** megnyitása
2.  A Storage-fiók megkeresése
3.  Bontsa ki a **blob-tárolók** csomópontot ebben a Storage-fiókban.
4.  Válassza ki az elemzések *-naplók-coreanalytics*nevű tárolót.
5.  Az eredmények a jobb oldali ablaktáblán jelennek meg, az első szinttől kezdve, ahogy a *resourceId =*. Folytassa az egyes szintek kiválasztását, amíg meg nem találja a *PT1H.js*fájlt. Az elérési út magyarázatát lásd: [blob Path Format](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  A fájlokban lévő összes blob- *PT1H.js* egy órányi elemzési naplókat jelöl egy adott CDN-végpont vagy az egyéni tartomány számára.
7.  A JSON-fájl tartalmának sémáját az alapvető elemzési naplók sémája ismerteti.


#### <a name="blob-path-format"></a>BLOB elérési útjának formátuma

Az alapvető elemzési naplók óránként jönnek létre, és az adatok gyűjtése és tárolása egy Azure-blobon belül, JSON-adattartalomként történik. Mivel a Storage Explorer eszköz címtár-elválasztó értelmezi a "/" karaktert, és megjeleníti a hierarchiát, az Azure-Blob elérési útja úgy jelenik meg, mintha hierarchikus struktúra legyen, és a blob nevét jelöli. A blob neve a következő elnevezési konvenciót követi:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Mezők leírása:**

|Érték|Description|
|-------|---------|
|Előfizetés azonosítója    |Az Azure-előfizetés azonosítója GUID formátumban.|
|Erőforráscsoport neve |Azon erőforráscsoport neve, amelyhez a CDN-erőforrások tartoznak.|
|Profilnév |A CDN-profil neve|
|Végpont neve |A CDN-végpont neve|
|Év|  Az év négyjegyű ábrázolása, például 2017|
|Hónap| A hónap számának kétszámjegyű ábrázolása. 01 = Január... 12 = december|
|Nap|   A hónap napjának kétszámjegyű ábrázolása|
|PT1H.jsbekapcsolva| Az elemzési adatokat tároló tényleges JSON-fájl|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Az alapvető elemzési adatfájlok exportálása CSV-fájlba

Annak érdekében, hogy könnyen hozzáférhessen az alapvető elemzésekhez, a rendszer az eszközhöz tartozó mintakód kódját is megadja. Ez az eszköz lehetővé teszi, hogy a JSON-fájlokat sima, vesszővel tagolt fájlformátumba töltse le, amely diagramok vagy egyéb összesítések létrehozásához használható.

A következőképpen használhatja az eszközt:

1.  Látogasson el a GitHub-hivatkozásra:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Töltse le a kódot.
3.  A fordításhoz és a konfiguráláshoz kövesse az utasításokat.
4.  Futtassa az eszközt.
5.  Az eredményül kapott CSV-fájl egy egyszerű, lapos hierarchiában jeleníti meg az elemzési adatforrásokat.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Diagnosztikai naplók felhasználása a Log Analytics-munkaterületről
Azure Monitor egy Azure-szolgáltatás, amely figyeli a Felhőbeli és a helyszíni környezeteket a rendelkezésre állás és a teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

Azure Monitor használatához engedélyeznie kell a [naplózást](#enable-logging-with-azure-storage) az Azure log Analytics munkaterületre, amelyet a jelen cikk korábbi részében ismertetünk.

### <a name="using-the-log-analytics-workspace"></a>A Log Analytics munkaterület használata

 Az alábbi ábrán az adattár bemeneteit és kimeneteit ábrázoló architektúra látható:

![Log Analytics-munkaterület](./media/cdn-diagnostics-log/12_Repo-overview.png)

*3. ábra – Log Analytics adattár*

A felügyeleti megoldások segítségével többféle módon jelenítheti meg az adatmennyiséget. A felügyeleti megoldásokat az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)szerezheti be.

A monitorozási megoldásokat az Azure Marketplace-en is telepítheti az egyes megoldások alján található **Letöltés most** hivatkozásra kattintva.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Azure Monitor CDN figyelési megoldás hozzáadása

Azure Monitor figyelési megoldás hozzáadásához kövesse az alábbi lépéseket:

1.   Jelentkezzen be az Azure Portal Azure-előfizetése segítségével, és lépjen az irányítópultra.
    ![Azure-irányítópult](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Az **új** lap **Marketplace**területén válassza a **figyelés + felügyelet**lehetőséget.

    ![Piactér](./media/cdn-diagnostics-log/14_Marketplace.png)

3. A **figyelés + felügyelet** lapon válassza az **összes**megjelenítése lehetőséget.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/15_See-all.png)

4. Keressen rá a CDN kifejezésre a keresőmezőbe.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/16_Search-for.png)

5. Válassza a **Azure CDN Core Analytics**lehetőséget. 

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. A **Létrehozás**gombra kattintva új log Analytics munkaterületet kell létrehoznia, vagy egy meglévőt kell használnia. 

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Válassza ki a korábban létrehozott munkaterületet. Ezután hozzá kell adnia egy Automation-fiókot.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/19_Add-automation.png)

8. A következő képernyőn látható az Automation-fiók űrlapja, amelyet ki kell töltenie. 

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/20_Automation.png)

9. Az Automation-fiók létrehozása után készen áll a megoldás hozzáadására. Válassza a **Létrehozás** gombot.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/21_Ready.png)

10. A megoldás már hozzá lett adva a munkaterülethez. Térjen vissza a Azure Portal irányítópultra.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/22_Dashboard.png)

    Válassza ki a létrehozott Log Analytics munkaterületet, és nyissa meg a munkaterületet. 

11. Az új megoldás megtekintéséhez válassza a **OMS-portál** csempét.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/23_workspace.png)

12. A portálnak ekkor a következő képernyőhöz hasonlóan kell kinéznie:

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Válassza ki az egyik csempét, hogy több nézetet is lásson az adataihoz.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/25_Interior-view.png)

    A balra vagy jobbra görgetéssel további csempéket tekinthet meg, amelyek az egyes nézeteket jelképezik az adatsorokban. 

    Az adatokkal kapcsolatos további részletek megtekintéséhez válassza ki az egyik csempét.

     ![Az összes megjelenítése](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ajánlatok és díjszabási szintek

[Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)láthatja a felügyeleti megoldások ajánlatait és díjszabását.

### <a name="customizing-views"></a>Nézetek testreszabása

A nézetet a **tervező**használatával testreszabhatja az adataiban. A tervezés megkezdéséhez nyissa meg a Log Analytics munkaterületet, és válassza ki a **Tervező nézete** csempét.

![Nézettervező](./media/cdn-diagnostics-log/27_Designer.png)

Húzza át a diagramok típusait, és töltse ki az elemezni kívánt adatokat.

![Nézettervező](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Naplózási adatkésések

Az alábbi táblázat a **microsofttól Azure CDN standard**Azure CDN, a **Akamai standard**és a **Verizon Azure CDN standard/prémium**szintű adatnaplózási késéseit mutatja be.

Microsoft-naplózási adatkésések | A Verizon-napló adatkésései | Akamai-naplózási adatkésések
--- | --- | ---
Késleltetve 1 órával. | Késleltetve 1 órával, és a végpont-propagálás befejezése után akár 2 órát is igénybe vehet. | 24 órával késleltetve; Ha több mint 24 órája lett létrehozva, a kezdéshez akár 2 órát is igénybe vesz. Ha nemrég jött létre, akár 25 órát is igénybe vehet, hogy a naplók megjelenjenek.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>A CDN Core Analytics diagnosztikai naplóinak típusai

A Microsoft jelenleg csak alapszintű elemzési naplókat kínál, amelyek a HTTP-válaszok statisztikáit és a kilépési statisztikákat bemutató mérőszámokat tartalmaznak a CDN-pop/Edge-ből.

### <a name="core-analytics-metrics-details"></a>Alapvető elemzési mérőszámok részletei
Az alábbi táblázat a **microsofttól Azure CDN standard szintű**, a Akamai-tól **Azure CDN standard**Azure CDN és a **Verizon standard/prémium**szintű elemzési naplókban elérhető mérőszámok listáját tartalmazza. Nem minden metrika érhető el az összes szolgáltatótól, bár az ilyen eltérések minimálisak. A tábla azt is megjeleníti, hogy egy adott metrika elérhető-e a szolgáltatótól. A metrikák csak azokra a CDN-végpontokra érhetők el, amelyeken forgalom van rajtuk.


|Metric                     | Leírás | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | A kérelem összes találatának száma ebben az időszakban. | Igen | Igen |Igen |
| RequestCountHttpStatus2xx | Az 2xx HTTP-kódot eredményező kérések száma (például 200, 202). | Igen | Igen |Igen |
| RequestCountHttpStatus3xx | Az 3xx HTTP-kódot eredményező kérések száma (például 300, 302). | Igen | Igen |Igen |
| RequestCountHttpStatus4xx | Az 4xx HTTP-kódot eredményező kérések száma (például 400, 404). | Igen | Igen |Igen |
| RequestCountHttpStatus5xx | Az 5xx HTTP-kódot eredményező kérések száma (például 500, 504). | Igen | Igen |Igen |
| RequestCountHttpStatusOthers | Az összes többi HTTP-kód száma (a 2xx-5xx kívül). | Igen | Igen |Igen |
| RequestCountHttpStatus200 | Az 200 HTTP-kód válaszát eredményező kérések száma. | Yes | Nem  |Yes |
| RequestCountHttpStatus206 | Az 206 HTTP-kód válaszát eredményező kérések száma. | Yes | Nem  |Yes |
| RequestCountHttpStatus302 | Az 302 HTTP-kód válaszát eredményező kérések száma. | Yes | Nem  |Yes |
| RequestCountHttpStatus304 | Az 304 HTTP-kód válaszát eredményező kérések száma. | Yes | Nem  |Yes |
| RequestCountHttpStatus404 | Az 404 HTTP-kód válaszát eredményező kérések száma. | Yes | Nem  |Yes |
| RequestCountCacheHit | A gyorsítótárban találatot eredményező kérések száma. Az eszköz közvetlenül a POP-ból a-ügyfélhez lett kézbesítve. | Igen | Igen | Nem  |
| RequestCountCacheMiss | A gyorsítótárból kihagyott összes kérelem száma. A gyorsítótár-kihagyás azt jelenti, hogy az eszköz nem található az ügyfélhez legközelebb lévő POP-ban, ezért a forrásból lett lekérve. | Igen | Igen | Nem |
| RequestCountCacheNoCache | Az eszközre irányuló, a peremhálózati felhasználói konfiguráció miatt nem gyorsítótárazott adatkérések száma. | Igen | Igen | Nem |
| RequestCountCacheUncacheable | Azon eszközökre irányuló kérelmek száma, amelyeket az eszköz gyorsítótár-vezérlése nem gyorsítótárazhat, és a fejlécek lejárnak, ami azt jelzi, hogy nem szabad gyorsítótárazni a POP-ban vagy a HTTP-ügyfélen. | Igen | Igen | Nem |
| RequestCountCacheOthers | Az összes olyan kérelem száma, amelynél a gyorsítótár állapota nem szerepel a fentiekben. | Nem | Yes | Nem  |
| EgressTotal | Kimenő adatforgalom GB-ban | Igen |Igen |Igen |
| EgressHttpStatus2xx | Kimenő adatátvitel * a 2xx HTTP-állapotkódok GB-ban való kitöltéséhez. | Igen | Igen | Nem  |
| EgressHttpStatus3xx | Kimenő adatforgalom a 3xx HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus4xx | Kimenő adatforgalom a 4xx HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus5xx | Kimenő adatforgalom a 5xx HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem |
| EgressHttpStatusOthers | Kimenő adatforgalom a más HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem  |
| EgressCacheHit | Kimenő adatforgalom a CDN-gyorsítótárban/-éleken közvetlenül a CDN-gyorsítótárból továbbított válaszok esetében. | Igen | Igen | Nem |
| EgressCacheMiss. | Kimenő adatforgalom olyan válaszokhoz, amelyek nem találhatók a legközelebbi POP-kiszolgálón, és a rendszer lekéri a forrás-kiszolgálóról. | Igen | Igen | Nem |
| EgressCacheNoCache | A kimenő adatforgalom olyan eszközök esetében, amelyek megakadályozták a gyorsítótárazást a peremhálózati felhasználói konfiguráció miatt. | Igen | Igen | Nem |
| EgressCacheUncacheable | Kimenő adatforgalom olyan eszközökön, amelyeket az eszköz gyorsítótár-vezérlése és/vagy a fejlécek lejárata megakadályoz. Azt jelzi, hogy nem szabad gyorsítótárazni a POP-on vagy a HTTP-ügyfélen. | Igen | Igen | Nem |
| EgressCacheOthers | Kimenő adatforgalom más gyorsítótár-forgatókönyvekhez. | Nem | Yes | Nem |

* A kimenő adatátvitel a CDN POP-kiszolgálókról az ügyfélnek továbbított forgalomra utal.


### <a name="schema-of-the-core-analytics-logs"></a>Az alapvető elemzési naplók sémája 

Az összes napló JSON formátumban van tárolva, és minden bejegyzés sztring típusú mezőket tartalmaz a következő séma szerint:

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

Az *idő* az óra határának kezdő időpontját jelöli, amelynél a statisztikát jelenteni kell. Ha egy CDN-szolgáltató nem támogatja a mérőszámot dupla vagy egész érték helyett, akkor Null érték van megadva. Ez a Null érték jelzi a metrika hiányát, és eltér a 0 értéktől. A végponton konfigurált, tartományon belüli metrikák közül egy készlet van.

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

* [Azure-beli diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Core Analytics Azure CDN kiegészítő portálon keresztül](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Naplók Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







