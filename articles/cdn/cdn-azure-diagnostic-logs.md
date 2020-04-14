---
title: Azure diagnosztikai naplók | Microsoft dokumentumok
description: Az ügyfél engedélyezheti a naplóelemzést az Azure CDN számára.
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
ms.topic: article
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 35d028a38e6ac19f270abcc8708a532b3749eb39
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254801"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Az Azure diagnosztikai naplóival megtekintheti az alapvető elemzéseket, és mentheti őket egy vagy több célhelyre, többek között a következőkre:

 - Azure Storage-fiók
 - Azure Event Hubs
 - [Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ez a funkció az összes tarifacsomag CDN-végpontján érhető el. 

Az Azure diagnosztikai naplók lehetővé teszik, hogy a CDN-végpontról a különböző forrásokba exportálja az alapvető használati metrikákat, így testre szabott módon használhatja fel őket. A következő adatexportálási típusokat használhatja például:

- Exportálja az adatokat a blobtárolóba, exportáljon CSV-be, és hozzon létre grafikonokat az Excelben.
- Adatok exportálása az Event Hubs-ba, és korrelál más Azure-szolgáltatásokból származó adatokkal.
- Adatok exportálása az Azure Monitor naplóiba és az adatok megtekintése a saját Log Analytics-munkaterületén

Az alábbi ábrán egy tipikus CDN-alapelemzési nézet látható az adatokról.

![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*1. ábra - CDN core analytics view*

A diagnosztikai naplókról további információt a Diagnosztikai naplók című [témakörben talál.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Az Azure Portallal való bejelentkezés engedélyezése

Kövesse az alábbi lépéseket, engedélyezze a naplózást a CDN alapvető elemzésével:

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem engedélyezte a CDN-t a munkafolyamathoz, [hozzon létre egy Azure CDN-profilt és végpontot](cdn-create-new-endpoint.md) a folytatás előtt.

1. Az Azure Portalon keresse meg a **CDN-profilt.**

2. Az Azure Portalon keressen egy CDN-profilt, vagy válasszon egyet az irányítópultról. Ezután válassza ki azt a CDN-végpontot, amelyhez engedélyezni szeretné a diagnosztikai naplókat.

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Válassza **a Diagnosztika naplók lehetőséget** a FIGYELÉS szakaszban.

   Megjelenik **a Diagnosztikai naplók** lap.

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Naplózás engedélyezése az Azure Storage szolgáltatással

Ha tárfiókot szeretne használni a naplók tárolására, kövesse az alábbi lépéseket:
    
1. A **Név mezőbe**írja be a diagnosztikai naplóbeállítások nevét.
 
2. Válassza **az Archiválás tárfiókba**lehetőséget, majd a **CoreAnalytics**lehetőséget. 

2. Megőrzés **(nap)** esetén adja meg a megőrzési napok számát. A nulla napok megőrzése határozatlan ideig tárolja a naplókat. 

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Válassza **a Tárfiók lehetőséget.**

    Megjelenik **a Tárfiók kiválasztása** lap.

4. Jelöljön ki egy tárfiókot a legördülő listából, majd kattintson az **OK gombra.**

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. A diagnosztikai napló beállításainak megadása után válassza a **Mentés gombot.**

### <a name="logging-with-azure-monitor"></a>Naplózás az Azure Monitor segítségével

Ha az Azure Monitort szeretné használni a naplók tárolásához, kövesse az alábbi lépéseket:

1. A **Diagnosztikai naplók** lapon válassza a **Küldés a Log Analytics szolgáltatásba**lehetőséget. 

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Válassza a **Konfigurálás** lehetőséget az Azure Monitor naplózásának konfigurálásához. 

   Megjelenik **a Log Analytics-munkaterületek** lap.

    >[!NOTE] 
    >Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Válassza **az Új munkaterület létrehozása**lehetőséget.

    Megjelenik **a Log Analytics munkaterület** lapja.

    >[!NOTE] 
    >Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/07_Create-new.png)

4. A **Log Analytics-munkaterülethez**adja meg a Log Analytics munkaterület ének nevét. A Log Analytics munkaterület nevének egyedinek kell lennie, és csak betűket, számokat és kötőjeleket tartalmazhat; szóközök és aláhúzásjelek nem engedélyezettek. 

5. **Előfizetés esetén**válasszon ki egy meglévő előfizetést a legördülő listából. 

6. Az **Erőforráscsoport**csoportban hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.

7. A **Hely listában**válasszon ki egy helyet a listából.

8. Ha menteni szeretné a naplókonfigurációt az irányítópultra, válassza a **Rögzítés az irányítópultra** lehetőséget. 

9. A konfiguráció befejezéséhez válassza az **OK gombot.**

10. A munkaterület létrehozása után visszatér a **Diagnosztikai naplók** lapra. Erősítse meg az új Log Analytics-munkaterület nevét.

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Válassza a **CoreAnalytics**lehetőséget, majd a **Mentés**lehetőséget.

12. Az új Log Analytics-munkaterület megtekintéséhez válassza **a Core analytics** a CDN-végpont oldalon.

    ![portál - Diagnosztikai naplók](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    A Log Analytics-munkaterület most már készen áll az adatok naplózására. Az adatok felhasználásához az Azure [Monitor naplói megoldást kell használnia,](#consuming-diagnostics-logs-from-a-log-analytics-workspace)amelyet a cikk későbbi részében is lefedett.

A naplóadatok késleltetéséről az [Adatkésések naplózása című témakörben](#log-data-delays)talál további információt.

## <a name="enable-logging-with-powershell"></a>Naplózás engedélyezése a PowerShell-lel

A következő példa bemutatja, hogyan engedélyezheti a diagnosztikai naplók az Azure PowerShell-parancsmagokon keresztül.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Diagnosztikai naplók engedélyezése tárfiókban

1. Jelentkezzen be és válasszon ki egy előfizetést:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. A diagnosztikai naplók storage-fiókban való engedélyezéséhez írja be ezt a parancsot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. A diagnosztikai naplók log analytics-munkaterületen való engedélyezéséhez írja be ezt a parancsot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnosztikai naplók felhasználása az Azure Storage-ből
Ez a szakasz ismerteti a CDN-alapelemzés sémáját, az Azure-tárfiókon belüli rendszerezésmódját, és mintakódot biztosít a naplók CSV-fájlban való letöltéséhez.

### <a name="using-microsoft-azure-storage-explorer"></a>A Microsoft Azure Storage Explorer használata
Mielőtt elérheti az alapvető elemzési adatokat egy Azure-tárfiókból, először szüksége van egy eszközre a tárfiók tartalmának eléréséhez. Bár számos eszköz érhető el a piacon, az egyik, hogy javasoljuk a Microsoft Azure Storage Explorer. Az eszköz letöltéséhez olvassa el az Azure Storage Explorer című [témakört.](https://storageexplorer.com/) A szoftver letöltése és telepítése után konfigurálja úgy, hogy ugyanazt az Azure storage-fiókot használja, amely a CDN diagnosztikai naplók célhelyeként konfigurálva volt.

1.  A **Microsoft Azure Storage Explorer megnyitása**
2.  A tárfiók megkeresése
3.  Bontsa ki a **Blob-tárolók** csomópontot a tárfiók alatt.
4.  Válassza ki az *insights-logs-coreanalytics nevű tárolót.*
5.  Az eredmények a jobb oldali ablaktáblán jelennek meg, az első szinttől *kezdve, erőforrásAzonosító=* néven. Folytassa az egyes szintek kiválasztását, amíg meg nem találja a *PT1H.json*fájlt. Az elérési út magyarázatát a Blob elérési útja formátumban című [témakörben tetszés szerint.](cdn-azure-diagnostic-logs.md#blob-path-format)
6.  Minden blob *PT1H.json* fájl egy adott CDN-végpont vagy egyéni tartomány a analytics naplókat jelöli.
7.  A JSON-fájl tartalmának sémája az alapvető elemzési naplók szakaszsémájában található.


#### <a name="blob-path-format"></a>Blob elérési útja formátuma

Core analytics naplók óránként jönnek létre, és az adatok gyűjtése és tárolása egyetlen Azure blobban, mint egy JSON hasznos adat. Mivel a Storage Explorer eszköz a '/' címtárelválasztót értelmezi, és megjeleníti a hierarchiát, az Azure blob elérési útja úgy jelenik meg, mintha hierarchikus struktúra lenne, és a blob nevét jelöli. A blob neve a következő elnevezési konvenciót követi:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Mezők leírása:**

|Érték|Leírás|
|-------|---------|
|Előfizetés azonosítója    |Az Azure-előfizetés guid formátumú azonosítója.|
|Erőforráscsoport neve |Annak az erőforráscsoportnak a neve, amelyhez a CDN-erőforrások tartoznak.|
|Profilnév |A CDN-profil neve|
|Végpont neve |A CDN-végpont neve|
|Year|  Az év négyjegyű ábrázolása, például 2017|
|Month| A hónapszám kétjegyű ábrázolása. 01=január ... 12=december|
|Day|   A hónap napjának kétjegyű ábrázolása|
|PT1H.json| Tényleges JSON-fájl, ahol az elemzési adatok tárolódnak|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Az alapvető elemzési adatok exportálása CSV-fájlba

Az alapvető elemzések elérésének egyszerű stakarása érdekében egy eszköz mintakódját is biztosítják. Ez az eszköz lehetővé teszi a JSON fájlok letöltését egy lapos vesszővel elválasztott fájlformátumba, amelyet diagramok vagy más összesítések létrehozására lehet használni.

Az eszköz a következőképpen használható:

1.  Látogasson el a GitHub-hivatkozásra:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Töltse le a kódot.
3.  Kövesse az utasításokat a fordításhoz és a konfiguráláshoz.
4.  Futtassa az eszközt.
5.  Az eredményül kapott CSV-fájl egyszerű, sima hierarchiában jeleníti meg az elemzési adatokat.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Diagnosztikai naplók felhasználása a Log Analytics-munkaterületről
Az Azure Monitor egy Azure-szolgáltatás, amely figyeli a felhőbeli és a helyszíni környezetben, hogy megőrizze azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

Az Azure Monitor használatához engedélyeznie kell a [naplózást](#enable-logging-with-azure-storage) az Azure Log Analytics munkaterületre, amely et a cikk korábbi cikke tárgyalja.

### <a name="using-the-log-analytics-workspace"></a>A Log Analytics munkaterület használata

 Az alábbi ábra a tárház bemeneteinek és kimeneteinek architektúráját mutatja be:

![Log Analytics-munkaterület](./media/cdn-diagnostics-log/12_Repo-overview.png)

*3. ábra – Log Analytics-tárház*

Az adatokat a Felügyeleti megoldások segítségével többféleképpen jelenítheti meg. Felügyeleti megoldásokat az [Azure Piactérről](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)szerezhet be.

Az Azure piactérről a **Get it now** link kiválasztásával telepítheti a figyelési megoldásokat az egyes megoldások alján.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Azure Monitor CDN figyelési megoldás hozzáadása

Az Azure Monitor figyelési megoldásának hozzáadásához kövesse az alábbi lépéseket:

1.   Jelentkezzen be az Azure Portalra az Azure-előfizetéshasználatával, és lépjen az irányítópultra.
    ![Azure-irányítópult](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Az **Új** lap **Piactér**területén válassza **a Figyelés + kezelés lehetőséget.**

    ![Piactér](./media/cdn-diagnostics-log/14_Marketplace.png)

3. A **Figyelés + kezelés** lapon válassza **az Összes megtekintése**lehetőséget.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/15_See-all.png)

4. Keressen CDN-t a keresőmezőben.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/16_Search-for.png)

5. Válassza az **Azure CDN Core Analytics lehetőséget.** 

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Miután a Létrehozás lehetőséget **választja,** a rendszer kéri, hogy hozzon létre egy új Log Analytics-munkaterületet, vagy használjon egy meglévőt. 

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Jelölje ki a korábban létrehozott munkaterületet. Ezután hozzá kell adnia egy automatizálási fiókot.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/19_Add-automation.png)

8. A következő képernyőn látható az automatizálási fiók űrlapki kell töltenie. 

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/20_Automation.png)

9. Miután létrehozta az automatizálási fiókot, készen áll a megoldás hozzáadására. Válassza a **Létrehozás** gombot.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/21_Ready.png)

10. A megoldás hozzáadva lett a munkaterülethez. Térjen vissza az Azure Portal irányítópultjára.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/22_Dashboard.png)

    Válassza ki a létrehozott Log Analytics munkaterületet a munkaterületre való ugráshoz. 

11. Az új megoldás megtekintéséhez válassza az **OMS Portal** csempét.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/23_workspace.png)

12. A portálnak most a következő képernyőhöz hasonlóan kell kinéznie:

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Jelölje ki az egyik csempét, ha több nézetet szeretne látni az adatokban.

    ![Az összes megjelenítése](./media/cdn-diagnostics-log/25_Interior-view.png)

    Balra vagy jobbra görgetve további csempéket láthat az egyes nézetekben az adatokban. 

    Az adatokkal kapcsolatos további részletek megtekintéséhez jelöljön ki egyet a csempék közül.

     ![Az összes megjelenítése](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ajánlatok és tarifaszintek

A felügyeleti megoldások ajánlatait és tarifacsomagjait [itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)láthatja.

### <a name="customizing-views"></a>Nézetek testreszabása

A nézetet a **Nézettervező**segítségével szabhatja testre az adatokközött. A tervezés megkezdéséhez nyissa meg a Log Analytics-munkaterületet, és válassza a **Tervező megtekintése** csempét.

![Nézettervező](./media/cdn-diagnostics-log/27_Designer.png)

Húzza el a diagramtípusokat, és töltse ki az elemezni kívánt adatokat.

![Nézettervező](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Adatkésések naplózása

Az alábbi táblázat a **Microsoft Azure CDN Standard,** **az Akamai Azure CDN Standard**és a Verizon Azure **CDN Standard/Premium naplózási**adatkéseit mutatja be.

Microsoft-naplóadatok késleltetése | Verizon naplóadatok késleltetése | Akamai naplóadatok késése
--- | --- | ---
1 órát késtem. | 1 órát késlekedhet, és akár 2 órát is igénybe vehet a végpontpropagálás befejezése után megjelenő megjelenés. | 24 órát késik; ha több mint 24 órával ezelőtt hozták létre, akár 2 órát is igénybe vesz a megjelenés. Ha nemrég jött létre, akár 25 órát is igénybe vehet, amíg a naplók megjelennek.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnosztikai naplótípusok a CDN magelemzéséhez

A Microsoft jelenleg csak alapvető elemzési naplókat kínál, amelyek a CDN POP/edges-ből látható HTTP-válaszstatisztikákat és kimenő statisztikákat megjelenítő mutatókat tartalmaznak.

### <a name="core-analytics-metrics-details"></a>Alapvető elemzési mutatók részletei
Az alábbi táblázat az **Azure CDN Standard microsoftos,** Azure **CDN Standard akamai Azure CDN Standard**és a **Verizon Azure CDN Standard/Premium**alapelemzési naplóiban elérhető mérőszámokat tartalmazza. Nem minden mérőszám érhető el az összes szolgáltatótól, bár ezek a különbségek minimálisak. A táblázat azt is megjeleníti, hogy egy adott metrika elérhető-e a szolgáltatótól. A metrikák csak azokra a CDN-végpontokra érhetők el, amelyek forgalmat használnak.


|Metrika                     | Leírás | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | A kérelmek lekéréseinek teljes száma ebben az időszakban. | Igen | Igen |Igen |
| RequestCountHttpStatus2xx | A 2xx HTTP-kódot eredményező kérelmek száma (például 200, 202). | Igen | Igen |Igen |
| RequestCountHttpStatus3xx | A 3xx HTTP-kódot eredményező kérelmek száma (például 300, 302). | Igen | Igen |Igen |
| RequestCountHttpStatus4xx | 4xx HTTP-kódot eredményező kérelmek száma (például 400, 404). | Igen | Igen |Igen |
| RequestCountHttpStatus5xx | Az 5xx HTTP-kódot eredményező kérelmek száma (például 500, 504). | Igen | Igen |Igen |
| RequestCountHttpStatusOthers | Az összes többi HTTP-kód megszámlálása (a 2xx-5xx-en kívül). | Igen | Igen |Igen |
| RequestCountHttpStatus200 | A 200-as HTTP-kódra adott választ eredményező kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus206 | A 206-os HTTP-kódra adott választ eredményező kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpstatus302 | A 302-es HTTP-kódra adott választ eredményező kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpstatus304 | A 304-es HTTP-kódra adott válaszhoz eredményező kérelmek száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus404 | A 404-es HTTP-kódra adott válaszhoz eredményező kérelmek száma. | Igen | Nem  |Igen |
| RequestCountCacheHit | A gyorsítótár-lekérést eredményező összes kérelem száma. Az eszköz közvetlenül a POP-ból került kiszolgálásra az ügyfélnek. | Igen | Igen | Nem  |
| RequestCountCacheMiss | A gyorsítótár-tévesztést eredményező kérelmek száma. A gyorsítótár-tévesztés azt jelenti, hogy az eszköz nem található az ügyfélhez legközelebb eső POP-on, ezért az Originből lett leolvasva. | Igen | Igen | Nem |
| RequestCountCacheNoCache | Az eszközhöz érkező összes olyan kérelem száma, amely a felhasználói konfiguráció miatt nem gyorsítótárazható a peremhálózaton. | Igen | Igen | Nem |
| RequestCountCacheUncacheable | Az eszközökre irányuló összes olyan kérelem megszámlálása, amelyet az eszköz Gyorsítótár-vezérlési és lejárati fejlécei nem gyorsítótáraznak, ami azt jelzi, hogy nem szabad gyorsítótárazni a POP-on vagy a HTTP-ügyfélen. | Igen | Igen | Nem |
| RequestCountCacheOthers | A fenti által nem érintett gyorsítótár-állapotú kérelmek száma. | Nem | Igen | Nem  |
| Kimenő forgalomÖsszes | Kimenő adatátvitel GB-ban | Igen |Igen |Igen |
| Kimenő forgalomHttpStatus2xx | Kimenő adatátvitel* a GB-ban 2xx HTTP állapotkóddal rendelkező válaszokhoz. | Igen | Igen | Nem  |
| Kimenő forgalomHttpStatus3xx | Kimenő adatátvitel a 3xx HTTP állapotkóddal rendelkező válaszokhoz GB-ban. | Igen | Igen | Nem  |
| Kimenő forgalomHttpStatus4xx | Kimenő adatátvitel a GB-ban 4xx HTTP állapotkóddal rendelkező válaszokhoz. | Igen | Igen | Nem  |
| Kimenő forgalomHttpStatus5xx | Kimenő adatátvitel a GB-ban lévő 5xx HTTP-állapotkóddal rendelkező válaszokhoz. | Igen | Igen | Nem |
| Kimenő forgalomHttpStatusOthers | Kimenő adatátvitel a GB-ban található más HTTP-állapotkódokkal rendelkező válaszokhoz. | Igen | Igen | Nem  |
| EgressCacheHit | Kimenő adatátvitel a közvetlenül a CDN POP/Edges CDN gyorsítótárából kézbesített válaszokhoz. | Igen | Igen | Nem |
| EgressCacheMiss. | Kimenő adatátvitel olyan válaszok esetén, amelyek nem találhatók meg a legközelebbi POP-kiszolgálón, és amelyeket az eredeti kiszolgálóról kérnek be. | Igen | Igen | Nem |
| EgressCacheNoCache | Kimenő adatátvitel olyan eszközök esetében, amelyek a felhasználói konfiguráció miatt nem gyorsítótárazhatók a peremhálózaton lévő eszközök esetében. | Igen | Igen | Nem |
| EgressCacheUngyorsítótárazható | Kimenő adatátvitel olyan eszközök esetében, amelyeket az eszköz Gyorsítótár-vezérlésés/vagy Lejárfejlécei nem gyorsítótáraznak. Azt jelzi, hogy nem kell gyorsítótárazza a POP vagy a HTTP-ügyfél. | Igen | Igen | Nem |
| EgressCacheOthers | Kimenő adatátvitel más gyorsítótár-forgatókönyvekhez. | Nem | Igen | Nem |

*A kimenő adatátvitel a CDN POP-kiszolgálókról az ügyfélnek érkező forgalomra vonatkozik.


### <a name="schema-of-the-core-analytics-logs"></a>Az alapvető elemzési naplók sémája 

Minden napló JSON formátumban van tárolva, és minden bejegyzés karakterláncmezőkkel rendelkezik a következő séma szerint:

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

Ahol *az idő* annak az órahatárnak a kezdési idejét jelöli, amelyről a statisztikákat jelentik. Ha egy metrikát nem támogat egy CDN-szolgáltató, dupla vagy egész érték helyett null érték van. Ez a null érték egy metrika hiányát jelzi, és eltér a 0 értéktől. A végponton konfigurált tartományonként ezek ből egy készlet van konfigurálva.

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

* [Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Alapvető elemzések az Azure CDN kiegészítő portálján keresztül](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-naplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







