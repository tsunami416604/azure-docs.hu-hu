---
title: Az Azure diagnosztikai naplók |} Microsoft Docs
description: Ügyfél engedélyezheti a webhelynapló elemzése Azure CDN szolgáltatás használata.
services: cdn
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: c367cffa8f0453a0f7e230571d861d039122c291
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Az Azure diagnosztikai naplók egyszerűsített analitika tekintheti meg és mentse el azokat egy vagy több célt, beleértve:

 - Azure Storage-fiók
 - Azure Event Hubs
 - [A Naplóelemzési munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ez a szolgáltatás az összes CDN-végpontok Verizon (Standard és prémium) és a CDN-profilra Akamai (általános) tartozó érhető el. 

Az Azure diagnosztikai naplóihoz alapvető a szoftverhasználati mérési adatok exportálása a CDN-végpontot különböző forrásokból, így képes felhasználni azokat egy egyedi módon. Például a következő típusú adatok exportálása teheti meg:

- A blob-tároló, Exportálás CSV-FÁJLBA és diagramjait létrehozása az Excel adatok exportálása.
- Adatok exportálása az Event Hubs, és más Azure-szolgáltatásokkal együtt összefüggéseket.
- Exportálhatja az adatokat a analytics naplózása, és a saját Naplóelemzési munkaterület adatainak megtekintéséhez

Az alábbi ábrán egy tipikus CDN core analytics adatok nézetét.

![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*1. ábra – CDN core analytics megtekintése*

Diagnosztikai naplók kapcsolatos további információkért lásd: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Az Azure-portálon naplózásának engedélyezése

Hajtsa végre ezeket a CDN egyszerűsített analitika naplózás lépéseket engedélyezése:

Jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha már nincs engedélyezve a munkafolyamat a CDN [Azure CDN engedélyezése](cdn-create-new-endpoint.md) a folytatás előtt.

1. A portálon lépjen a **CDN-profil**.
2. Válassza ki a CDN-profil, majd válassza ki, amelynek engedélyezni szeretné a CDN-végpont **diagnosztikai naplók**.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Válassza ki **diagnosztikai naplók** a a **figyelés** szakasz.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Az Azure Storage naplózásának engedélyezése
    
1. A naplók tárolására az Azure storage használatához válassza **tárfiókba archív**, jelölje be **CoreAnalytics**, és válassza ki az adatmegőrzési napok számát a **megőrzés (nap)**. Egy nulla napos megőrzési határozatlan ideig tárolja a naplókat. 
2. Adjon meg egy nevet a beállítás, majd kattintson a **tárfiók**. Miután kiválasztotta a tárfiók, kattintson a **mentése**.

![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*2. ábra – az Azure Storage-naplózás*

### <a name="logging-with-log-analytics"></a>A Naplóelemzési naplózás

A naplók tárolásához Naplóelemzési használatához kövesse az alábbi lépéseket:

1. Az a **diagnosztikai naplók** panelen válassza **küldeni a Naplóelemzési**. 

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Kattintson a **konfigurálása** napló analytics naplózási. Az OMS-munkaterület párbeszédpanelen válasszon egy előző munkaterületet, vagy hozzon létre egy újat.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Kattintson a **hozzon létre új munkaterületet**.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/07_Create-new.png)

4. Adjon meg egy új Naplóelemzési munkaterület nevet. A Naplóelemzési munkaterület nevét kell egyedinek lennie, és csak betűket, számokat és kötőjeleket tartalmazhat; szóközöket és aláhúzásjeleket tartalmazhat nem engedélyezettek. 
5. Ezután válassza ki egy meglévő előfizetés, a erőforráscsoport (új vagy meglévő), a hely és a tarifacsomagot. Akkor is ezt a konfigurációt az irányítópulton való rögzítéshez lehetőséget. Kattintson a **OK** a konfigurálás befejezéséhez.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  A munkaterület létrehozását követően a rendszer visszairányítja a diagnosztikai naplók windows. Erősítse meg az új naplóelemzési munkaterület nevét.

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    A naplóelemzés konfigurációja beállítása után győződjön meg arról, hogy megadta **CoreAnalytics**.

6. Kattintson a **Save** (Mentés) gombra.

7. Az új Naplóelemzési munkaterület megtekintéséhez nyissa meg az Azure portál Irányítópultjára, majd kattintson a naplóelemzési munkaterület nevét. Kattintson az OMS-portálon csempe a Naplóelemzési munkaterület megtekintéséhez. 

    ![portál – diagnosztikai naplók](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    A Naplóelemzési munkaterület adatokat naplózhatnak készen áll. Adatok felhasználásához, használjon egy [napló elemzési megoldások](#consuming-diagnostics-logs-from-a-log-analytics-workspace), az érintett a cikk későbbi részében.

Naplózási adatok késések kapcsolatos további információkért lásd: [adatok késések jelentkezzen](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>A PowerShell-lel naplózásának engedélyezése

A következő példa bemutatja, hogyan engedélyezése diagnosztikai naplók az Azure PowerShell-parancsmagok használatával.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Diagnosztika engedélyezése bejelentkezik a Storage-fiók

Először jelentkezzen be, és válasszon egy előfizetést:

    Connect-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Engedélyezze a diagnosztikai naplók, a Tárfiók használja ezt a parancsot:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Engedélyezése diagnosztikai naplókat a Naplóelemzési munkaterület használja ezt a parancsot:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnosztikai naplók az Azure Storage felhasználása
Ez a szakasz ismerteti a sémája CDN egyszerűsített analitika, hogyan az Azure-tárfiók belül vannak rendezve, és egy CSV-fájlban a naplók letöltéséhez példakódot tartalmaz.

### <a name="using-microsoft-azure-storage-explorer"></a>A Microsoft Azure Tártallózó használatával
Az alapvető analitikai adatok eléréséhez az Azure Storage-fiókjához, először egy eszköz tárfiókokban tartalmához való hozzáféréshez. Eszközök is elérhetők több a piacon, amíg azt, amelyik ajánlott a Microsoft Azure Tártallózó. Az eszköz letöltéséhez keresse [Azure Tártallózó](http://storageexplorer.com/). Szoftver letöltése és telepítése a, miután konfigurálja úgy, hogy a CDN diagnosztikai naplókat a célként beállított ugyanazt az Azure storage a fiókot használja.

1.  Nyissa meg **Microsoft Azure Tártallózó**
2.  Keresse meg a storage-fiók
3.  Lépjen a **"Blobtárolók"** csomópont alatt ez a tároló fiókot, és bontsa ki a csomópontot
4.  Válassza ki a tárolót **"insights-logs-coreanalytics"** , és kattintson rá duplán
5.  Annak az eredménye megjelenítése fel az első szintjét, mely tűnik kezdve a jobb oldali ablaktáblában lévő **"resourceId ="**. Továbbra is, egészen amíg meg nem jelenik a fájl kattintva **PT1H.json**. Lásd a következő megjegyzést, az elérési út ismertetése.
6.  Minden egyes blob **PT1H.json** számára egy adott CDN-végpont vagy tartozó egyéni tartomány egy órában az elemzési naplókat jelöli.
7.  A sémát a JSON-fájl tartalmának a core analytics naplók a séma szakaszban ismertetett


> [!NOTE]
> **A BLOB elérési út formátuma**
> 
> Core analytics naplók óránként hozza létre, és az adatokat gyűjt, és tárolt egyetlen Azure blob-JSON-adatként. Mivel a tárolási explorer eszköz értelmezi "/" directory elválasztó és megjeleníti a hierarchia, elérési útját az Azure-blobot jelenik meg, hogy van-e olyan hierarchikus struktúra és a blob neve. A blob neve követi a következő elnevezés szabály szerint: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Mezők leírása:**

|érték|leírás|
|-------|---------|
|Előfizetés azonosítója    |Az Azure-előfizetés Guid formátumban azonosítója.|
|Erőforrás |Csoport neve neve az erőforráscsoport, amelybe a CDN-erőforrások tartoznak.|
|Profilnév |A CDN-profil neve|
|A végpont neve |A CDN-végpont neve|
|év|  az év, például 2017 4 számjegyből álló ábrázolása|
|Hónap| a hónapok sorszáma 2 számjegyű ábrázolása. 01 január =... 12 decembert jelenti – =|
|Nap|   a hónap napját 2 számjegyű ábrázolása|
|PT1H.json| Az analitikai adatok tárolására tényleges JSON-fájl|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Az alapvető analitikai adatok exportálása CSV-fájlba

Egyszerűsített analitika hozzáférési megkönnyítése mintakód egy eszköz valósul meg. Ez az eszköz lehetővé teszi, hogy a JSON-fájlok letöltésére olyan egyszerű vesszővel tagolt fájl formátumra, amely könnyen hozzanak létre diagramokat vagy más összesítéseket használható.

Ez az eszköz használatát:

1.  Látogasson el a github-hivatkozást: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Töltse le a kódot.
3.  Kövesse az utasításokat, lefordítása és konfigurálásához.
4.  Futtassa az eszközt.
5.  Az eredményül kapott CSV-fájl egy egyszerű strukturálatlan hierarchia analytics adatainak megjelenítése.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>A Naplóelemzési munkaterület a diagnosztikai naplók felhasználása
A Log Analytics az Azure egy szolgáltatása, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állásuk és teljesítményük fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson. 

Naplóelemzési használandó kell [naplózását](#enable-logging-with-azure-storage) az Azure Naplóelemzés munkaterületet, amely tárgyalt az ebben a cikkben.

### <a name="using-the-log-analytics-workspace"></a>A Naplóelemzési munkaterület használata

 Az alábbi ábrán látható, a tárház kimenetek és a bemenetek architektúrája:

![Log Analytics-munkaterület](./media/cdn-diagnostics-log/12_Repo-overview.png)

*3. ábra - napló Analytics tárház*

Az sokféleképpen megoldások használatával megjelenítheti az adatokat. Ezt úgy szerezheti be a felügyeleti megoldás a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Telepíthető megoldások Azure piactérről kattintva a **most töltse le innen** hivatkozás az egyes megoldások alján.

### <a name="adding-a-log-analytics-cdn-management-solution"></a>A napló Analytics CDN-kezelési megoldás hozzáadása

Kövesse az alábbi lépéseket a felügyeleti megoldás hozzáadása:

1.   Ha még nem tette meg, jelentkezzen be az Azure-előfizetéshez az Azure portálra, és nyissa meg az irányítópulton való rögzítéséhez.
    ![Az Azure irányítópult](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Az a **új** részen **piactér**, jelölje be **figyelés + felügyeleti**.

    ![Piactér](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Az a **figyelés + felügyeleti** panelen kattintson a **láthatja az összes**.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/15_See-all.png)

4.  Keresse meg a CDN a Keresés mezőbe.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Válassza ki **Azure CDN egyszerűsített analitika**. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Miután rákattintott **létrehozása**, kérni fogja az új Naplóelemzési munkaterület létrehozása, vagy használjon egy meglévőt. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Válassza ki a munkaterületet előtt. Majd kell hozzáadnia az automation-fiók.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Az alábbi képernyőn látható az automatizálási fiók formában adja meg az. 

    ![Az összes megtekintése](./media/cdn-diagnostics-log/20_Automation.png)

9. Az automation-fiók létrehozását követően készen áll a megoldás hozzáadása. Kattintson a **Létrehozás** gombra.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/21_Ready.png)

10. A megoldás most már fel lett véve a munkaterületen. Térjen vissza az Azure portál Irányítópultjára.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/22_Dashboard.png)

    Kattintson a Naplóelemzési munkaterület létrehozott nyissa meg a munkaterületet. 

11. Kattintson a **OMS-portálon** csempe az új megoldás megtekintéséhez.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/23_workspace.png)

12. A portál most már a következő képernyő hasonlóan kell kinéznie:

    ![Az összes megtekintése](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Kattintson az áttekintőlapon megjeleníteni az adatokat több nézet.

    ![Az összes megtekintése](./media/cdn-diagnostics-log/25_Interior-view.png)

    Tekintse meg az adatokat az egyes nézetek képviselő további csempék jobbra vagy balra görgetve. 

    A csempék valamelyikére kattintva lehetővé teszi az adatok további információt.

     ![Az összes megtekintése](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ajánlatok és tarifacsomagok

Ajánlatok és a felügyeleti megoldásokra tarifacsomagok [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Nézetek testreszabása

Testre szabhatja a nézet a adatokká használatával a **adatforrásnézet-tervezőből**. Megtervezésének első lépésként nyissa meg a Naplóelemzési munkaterületet, és kattintson a **adatforrásnézet-tervezőből** csempére.

![Nézettervező](./media/cdn-diagnostics-log/27_Designer.png)

Húzza és dobja el a diagramok típusú, és töltse ki az elemezni kívánt adatok adatait.

![Nézettervező](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Naplózási adatok késleltetése

Verizon napló adatok késleltetése | Akamai napló adatok késleltetése
--- | ---
Verizon naplóadatokat 1 óra késleltetett, és indítsa el a végpont-propagálás befejezését követően megjelenő 2 órát igénybe vehet. | Akamai naplóadatokat késik 24 óránként; Ha több mint 24 órája hozták létre, start megjelenő 2 órát vesz igénybe. Ha nemrég készült, start jelenik meg a naplófájlokat akár 25 órát is igénybe vehet.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnosztikai naplófájl típusokat CDN egyszerűsített analitika

Jelenleg csak alapvető analytics naplók, metrikák HTTP-válaszok statisztikai adatainak és a kimenő forgalom statisztika alapegységét megjelenítő a CDN POP/széleit tartalmazó fel.

### <a name="core-analytics-metrics-details"></a>Core analytics metrikák részletei
A következő táblázat a core analytics naplókban elérhető metrikák listáját tartalmazza. Nem minden metrikák érhetők el minden szolgáltató, annak ellenére, hogy ezek az eltérések minimális. Az alábbi táblázatban is látható, ha egy metrika érhető el a szolgáltató által. Ne feledje, hogy a metrikák csak ezek CDN-végpontok, amelyek azokat a forgalom érhető el.


|Metrika                     | Leírás   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Ebben az időszakban kérést találatok száma összesen| Igen  |Igen   |
| RequestCountHttpStatus2xx |Egy 2xx HTTP kódot (például 200, 202) eredményezett összes kérelem száma              | Igen  |Igen   |
| RequestCountHttpStatus3xx | Egy 3xx HTTP kódot (például 300, 302) eredményezett összes kérelem száma              | Igen  |Igen   |
| RequestCountHttpStatus4xx |Egy 4xx HTTP kódot (például 400, 404) eredményezett összes kérelem száma               | Igen   |Igen   |
| RequestCountHttpStatus5xx | 5xx HTTP kódot (például 500, 504) eredményezett összes kérelem száma              | Igen  |Igen   |
| RequestCountHttpStatusOthers |  Minden más HTTP-kód (kívül 2xx-5xx) száma | Igen  |Igen   |
| RequestCountHttpStatus200 | 200 HTTP kód válaszul eredményező összes kérelmek száma              |Nem   |Igen   |
| RequestCountHttpStatus206 | A 206-os HTTP kód válaszul eredményező összes kérelmek száma              |Nem   |Igen   |
| RequestCountHttpStatus302 | HTTP 302-es kód választ eredményező összes kérelmek száma              |Nem   |Igen   |
| RequestCountHttpStatus304 |  304-es HTTP-kód választ eredményező összes kérelmek száma             |Nem   |Igen   |
| RequestCountHttpStatus404 | HTTP 404-es kód választ eredményező összes kérelmek száma              |Nem   |Igen   |
| RequestCountCacheHit |A gyorsítótár találati eredményező összes kérelmek száma. Az eszköz állítása és kiszolgálása között a POP-ről az ügyfélnek.               | Igen  |Nem   |
| RequestCountCacheMiss | A gyorsítótár-tévesztései eredményező összes kérelmek száma. Ez azt jelenti, hogy az eszköz nem található meg az ügyfél legközelebb POP, és ezért be lett olvasva a forrásból.              |Igen   | Nem  |
| RequestCountCacheNoCache | Az eszköz minden kérelemhez, amely megakadályozta a gyorsítótárba egy felhasználói konfiguráció az oldal miatt száma.              |Igen   | Nem  |
| RequestCountCacheUncacheable | Az eszközökhöz, amely megakadályozza az eszköz a Cache-Control és Expires fejléc, amely jelzi, hogy azt nem gyorsítótárazza a POP- vagy HTTP-ügyfél által a gyorsítótárba összes kérelmek száma                |Igen   |Nem   |
| RequestCountCacheOthers | Gyorsítótár állapotú fent nem vonatkozik minden kérelmek száma.              |Igen   | Nem  |
| EgressTotal | Kimenő adatátvitel GB-ban              |Igen   |Igen   |
| EgressHttpStatus2xx | Kimenő adatátviteli * a válaszok a 2xx HTTP-állapotkódok GB-ban            |Igen   |Nem   |
| EgressHttpStatus3xx | Kimenő adatátvitel a válaszok a 3xx HTTP-állapotkódok GB-ban              |Igen   |Nem   |
| EgressHttpStatus4xx | Kimenő adatátvitel a válaszok a 4xx HTTP-állapotkódok GB-ban               |Igen   | Nem  |
| EgressHttpStatus5xx | Kimenő adatátvitel 5xx HTTP-állapotkódok GB-ban a válaszok               |Igen   |  Nem |
| EgressHttpStatusOthers | Kimenő adatátvitel válaszok az egyéb HTTP-állapotkódok GB-ban                |Igen   |Nem   |
| EgressCacheHit |  Kimenő adatátvitel kapott válaszok közvetlenül a CDN-gyorsítótárból a CDN POP/szegély  |Igen   |  Nem |
| EgressCacheMiss | Kimenő adatátvitel a válaszok nem található a legközelebbi POP-kiszolgálón, és lekérése a forráskiszolgálóról              |Igen   |  Nem |
| EgressCacheNoCache | Kimenő adatátvitel eszközök, amely megakadályozta a felhasználói konfiguráció az oldal miatt a gyorsítótárba.                |Igen   |Nem   |
| EgressCacheUncacheable | Kimenő adatátvitel eszközök, amelyek a rendszer megakadályozza az eszköz a Cache-Control vagy Expires fejléc által a gyorsítótárba. Azt jelzi, hogy azt nem gyorsítótárazza a POP vagy a HTTP-ügyfél.                   |Igen   | Nem  |
| EgressCacheOthers |  Kimenő adatátvitel más gyorsítótár forgatókönyvek esetén.             |Igen   | Nem  |

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

Ahol a "time" jelenti az óra határ, amelynek a statisztikáit jelentett kezdési idejét. Amikor metrika nem támogatott a CDN-szolgáltató helyett egy double vagy egész szám, a rendszer null értékű. A null érték metrika jelezné, és eltér a 0 értéket. Nincs a végponthoz tartományonként metrikákat egy készletét.

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







