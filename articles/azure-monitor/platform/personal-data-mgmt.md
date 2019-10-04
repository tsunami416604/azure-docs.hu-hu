---
title: Útmutató az Azure-Log Analytics tárolt személyes adatszolgáltatásokhoz | Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezelheti az Azure Log Analyticsban tárolt személyes információkat, valamint az azonosítására és eltávolítására szolgáló metódusokat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: a443931b8340552251fbcbe534f009eeeaf953aa
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617306"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Útmutató a Log Analytics és Application Insights tárolt személyes adatszolgáltatásokhoz

Log Analytics egy adattár, ahol a személyes adattárolók valószínűleg megtalálhatók. A Application Insights Log Analytics partícióban tárolja az adattárolást. Ebből a cikkből megtudhatja, hogy a Log Analytics és Application Insights az ilyen jellegű adatmennyiségeket általában megtalálja, valamint az ilyen jellegű adatkezeléshez rendelkezésre álló képességeket.

> [!NOTE]
> Ennek a cikknek az alkalmazásában az _adatok_ a log Analytics munkaterületre elküldett adatokra vonatkoznak, míg az _alkalmazásadatok_ a Application Insights által összegyűjtött adatokra utalnak.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Személyes adatkezelési stratégia

Míg Ön és a vállalata végső soron meg fogja határozni azt a stratégiát, amellyel a személyes adatait kezelni fogja (ha van), az alábbiakban néhány lehetséges megközelítést talál. A technikai szempontból előnyben részesítettek a legtöbbet a legkevésbé kívánatosabbak:

* Ahol lehetséges, állítsa le a gyűjtést, elhomályosítja, névtelenség vagy egyéb módon módosítsa a gyűjtött adatokat, hogy kizárják azt a "magánjellegű" kifejezésnek. Ez _messze_ az előnyben részesített megközelítés, amely a rendkívül költséges és hatásos adatkezelési stratégia létrehozásához szükséges.
* Ha nem lehetséges, az adatplatformra és a teljesítményre gyakorolt hatás csökkentése érdekében próbálja meg normalizálni az adatmennyiséget. Ha például egy explicit felhasználói azonosítót szeretne naplózni, hozzon létre olyan keresési adatokat, amelyek a felhasználónevet és az adataikat egy olyan belső AZONOSÍTÓra hasonlítják össze, amely aztán máshol is naplózható. Így ha az egyik felhasználó kéri a személyes adatok törlését, akkor lehetséges, hogy csak a felhasználóhoz tartozó keresési táblában lévő sor törlése lesz elegendő. 
* Végül, ha a magánjellegű adatokat össze kell gyűjteni, a kiürítési API elérési útját és a meglévő lekérdezési API elérési útját kell felépíteni, hogy megfeleljenek a felhasználóhoz társított személyes adatok exportálásának és törlésének. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Hol található a személyes adatkeresés a Log Analyticsban?

A Log Analytics egy rugalmas tároló, amely egy séma az adatokhoz való előírásával lehetővé teszi az összes egyéni értékkel rendelkező mező felülbírálását. Emellett minden egyéni séma betölthető. Ezért nem lehet pontosan megállapítani, hogy a személyes adatai hol találhatók az adott munkaterületen. A következő helyek azonban jó kiindulópontot mutatnak a leltárban:

### <a name="log-data"></a>Naplóadatok

* *IP-címek*: A Log Analytics számos különböző típusú IP-információt gyűjt az egyes táblákon. Az alábbi lekérdezés például megjeleníti az összes olyan táblát, amelyben az IPv4-címek gyűjtése az elmúlt 24 órában megtörtént:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Felhasználói azonosítók*: A felhasználói azonosítók számos különböző megoldásban és táblában találhatók. A keresési paranccsal kereshet egy adott felhasználónevet a teljes adatkészletben:
    ```
    search "[username goes here]"
    ```
  Ne felejtse el megkeresni, hogy ne csak az ember számára olvasható felhasználóneveket, hanem a GUID azonosítókat is, amelyek közvetlenül visszavezethetők egy adott felhasználó számára.
* *Eszközök azonosítói*: A felhasználói azonosítók, például az eszközök azonosítói néha "privát" is tekinthetők. Használja ugyanazt a megközelítést, mint a fent felsorolt felhasználói azonosítók a táblák azonosításához, ahol ez problémát jelenthet. 
* *Egyéni*adatértékek: A Log Analytics különböző módszerekkel teszi lehetővé a gyűjtemény használatát: egyéni naplók és egyéni mezők, a http-adatgyűjtő [API](../../azure-monitor/platform/data-collector-api.md) és a rendszer-eseménynaplók részeként gyűjtött egyéni adatok. Ezek mindegyike bizalmas személyes adatmennyiséget tartalmaz, és meg kell vizsgálni, hogy van-e ilyen adatmennyiség.
* *Megoldás – rögzített*adatértékek: Mivel a megoldási mechanizmus egy nyílt végű, javasoljuk, hogy tekintse át a megoldások által generált összes táblázatot a megfelelőség biztosítása érdekében.

### <a name="application-data"></a>Alkalmazásadatok

* *IP-címek*: Míg a Application Insights alapértelmezés szerint az összes IP-cím mezőt "0.0.0.0"-re írja, ez egy meglehetősen gyakori minta, amely felülbírálja ezt az értéket a tényleges felhasználói IP-címmel a munkamenet-információk fenntartása érdekében. Az alábbi elemzési lekérdezés használatával bármely olyan tábla megkereshető, amely az elmúlt 24 órában a "0.0.0.0" értéktől eltérő IP-cím oszlopban található értékeket tartalmazza:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Felhasználói azonosítók*: Alapértelmezés szerint a Application Insights véletlenszerűen generált azonosítókat fog használni a felhasználók és a munkamenetek nyomon követéséhez. Gyakori azonban, hogy ezek a mezők felülbírálva vannak az alkalmazáshoz tartozó AZONOSÍTÓk tárolására. Például: felhasználónevek, HRE GUID azonosítók stb. Ezeket az azonosítókat gyakran tekintik a személyes adatként való hatókörnek, ezért azokat megfelelően kell kezelni. Javasoljuk, hogy az ilyen azonosítók elolvasása vagy névtelenség érdekében mindig próbálkozzon. Ezek az értékek általában a következők: session_Id, user_Id, user_AuthenticatedId, user_AccountId, valamint customDimensions.
* *Egyéni*adatértékek: Application Insights lehetővé teszi egyéni dimenziók egy csoportjának hozzáfűzését bármely adattípushoz. Ezek a méretek *bármilyen* adattal rendelkezhetnek. Az alábbi lekérdezés segítségével azonosíthatja az elmúlt 24 órában összegyűjtött egyéni dimenziókat:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Memóriában tárolt és*átvitt adatforgalom: Application Insights nyomon követi a kivételeket, a kérelmeket, a függőségi hívásokat és a nyomkövetéseket. A magánjellegű adatokat gyakran a kód és a HTTP-hívás szintjén lehet gyűjteni. Tekintse át a kivételeket, a kérelmeket, a függőségeket és a nyomkövetési táblázatokat az ilyen jellegű adat azonosításához. Ha lehetséges, használja a [telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) -inicializálást, ahol a lehető legtöbbet használhatja ezeket az adatfájlokat.
* *Snapshot Debugger rögzítések*: A Application Insights [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) szolgáltatása lehetővé teszi a hibakeresési Pillanatképek gyűjtését, ha kivétel történik az alkalmazás éles példányán. A pillanatképek lehetővé teszik a teljes verem nyomon követését, amely a kivételekhez vezet, valamint a helyi változók értékeit a verem minden lépésében. Ez a funkció sajnos nem teszi lehetővé a Snap pontok szelektív törlését, illetve az adatokhoz való programozott hozzáférést a pillanatképen belül. Ezért ha az alapértelmezett pillanatkép-megőrzési arány nem felel meg a megfelelőségi követelményeknek, a javaslat a funkció kikapcsolására szolgál.

## <a name="how-to-export-and-delete-private-data"></a>Magánjellegű adatexportálás és-törlés

Ahogy azt a [személyes adatkezelési stratégia](#strategy-for-personal-data-handling) című szakaszban is említettük, __erősen__ ajánlott az adatgyűjtési szabályzat átstrukturálása, hogy letiltsa a magánjellegű adatgyűjtést, a titkosítást vagy a anonimizálásával, vagy Ellenkező esetben módosítsa úgy, hogy eltávolítsa azt a "Private"-nek. Az adatkezelés mindenekelőtt költségekkel jár Önnek és a csapatának a stratégia meghatározásához és automatizálásához, valamint az ügyfelek számára az adatkezeléshez és a folyamatos karbantartási költségekhez kapcsolódó kezelőfelület létrehozásához. Emellett az Log Analytics és Application Insights számítási feladatait is számításba kell venni, és az egyidejű lekérdezési vagy kitakarítási API-hívások nagy mennyisége negatív hatással lehet az összes többi interakcióra Log Analytics funkcióval. Azt mondta, hogy valóban vannak olyan érvényes forgatókönyvek, amelyekben a magánjellegű adatokat össze kell gyűjteni. Ezekben az esetekben az adatkezelést az ebben a részben leírtak szerint kell kezelni.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Megtekintés és exportálás

Az adatkérések megtekintésére és exportálására a [log Analytics lekérdezési API](https://dev.loganalytics.io/) -t vagy a [Application INSIGHTS lekérdezési API](https://dev.applicationinsights.io/quickstart) -t kell használni. Ahhoz, hogy a felhasználók számára elérhetővé lehessen tennie az adatalakot, a felhasználónak kell megvalósítania. A [Azure functions](https://azure.microsoft.com/services/functions/) nagyszerű helyet ad az ilyen logika üzemeltetéséhez.

> [!IMPORTANT]
>  Habár a kitisztítási műveletek túlnyomó többsége sokkal gyorsabban teljesíthető, mint az SLA, **a kitisztítási műveletek befejezésére vonatkozó formális SLA-t 30 napon belül állítja be a rendszer** a felhasznált adatplatformra gyakorolt jelentős hatás miatt. Ez egy automatizált folyamat; nem kérhető le a művelet gyorsabb kezelése.

### <a name="delete"></a>Törlés

> [!WARNING]
> A Log Analyticsban lévő törlés romboló és nem visszafordítható! A végrehajtás során rendkívül körültekintően járjon el.

Elérhetővé tettük a *kiürítési* API elérési útját az adatvédelem részeként. Ezt az elérési utat takarékosan kell használni a művelethez kapcsolódó kockázat miatt, a lehetséges teljesítményre gyakorolt hatás, valamint a teljes összesítések, a mérések és a Log Analytics-adat egyéb szempontjainak eldöntése. Tekintse meg a [személyes](#strategy-for-personal-data-handling) adatkezeléssel foglalkozó témakört, amely a magánjellegű információk kezelésére szolgáló alternatív módszereket ismerteti.

A kiürítés egy magas jogosultsági szintű művelet, amelyet az Azure-ban egyetlen alkalmazás vagy felhasználó sem tartalmaz (beleértve az erőforrás tulajdonosát is), anélkül, hogy külön szerepkört kellene kiadni a Azure Resource Managerban. Ez a szerepkör _Adattisztító_ , és az adatvesztés lehetősége miatt körültekintően kell delegálni. 

> [!IMPORTANT]
> A rendszererőforrások kezeléséhez óránként 50 kérelem van szabályozva a kiürítési kérelmek esetében. A kiürítési kérelmek végrehajtását úgy kell elküldenie, hogy egyetlen parancsot küld, amelynek predikátuma minden olyan felhasználói identitást tartalmaz, amelynél törlésre van szükség. Több identitás megadásához használja az [in operátort](/azure/kusto/query/inoperator) . A törlési kérés végrehajtása előtt futtassa a lekérdezést, és ellenőrizze, hogy az eredmények várhatóak-e. 



A Azure Resource Manager szerepkör hozzárendelése után két új API-elérési út érhető el: 

#### <a name="log-data"></a>Naplóadatok

* Törlés [után](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – egy olyan objektumot vesz fel, amely meghatározza a törölni kívánt adatok paramétereit, és egy hivatkozás GUID azonosítóját adja vissza. 
* Kiürítési állapot beolvasása – a törlés utáni hívás egy "x-MS-status-Location" fejlécet ad vissza, amely tartalmaz egy URL-címet, amelyet meghívhat a kiürítő API állapotának meghatározásához. Példa:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Habár a kitisztítási műveletek túlnyomó többségét várhatóan sokkal gyorsabban teljesítjük, mint az SLA-t, az Log Analytics által használt adatplatformra gyakorolt jelentős hatásuk miatt **a kitisztítási műveletek befejezésére vonatkozó formális SLA 30 napon belül be van állítva**. 

#### <a name="application-data"></a>Alkalmazásadatok

* Törlés [után](https://docs.microsoft.com/rest/api/application-insights/components/purge) – egy olyan objektumot vesz fel, amely meghatározza a törölni kívánt adatok paramétereit, és egy hivatkozás GUID azonosítóját adja vissza.
* Kiürítési állapot beolvasása – a törlés utáni hívás egy "x-MS-status-Location" fejlécet ad vissza, amely tartalmaz egy URL-címet, amelyet meghívhat a kiürítő API állapotának meghatározásához. Példa:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Habár a kitisztítási műveletek túlnyomó többsége sokkal gyorsabban teljesíthető, mint az SLA, mivel jelentős hatással vannak az Application Insights által használt adatplatformra, **a törlési műveletek befejezésére vonatkozó formális SLA 30 napon belül be van állítva**.

## <a name="next-steps"></a>További lépések
- Ha többet szeretne megtudni a Log Analytics adatok gyűjtéséről, feldolgozásáról és védelméről, tekintse meg a [log Analytics az](../../azure-monitor/platform/data-security.md)adatbiztonságot ismertető témakört.
- Ha többet szeretne megtudni a Application Insights adatok gyűjtéséről, feldolgozásáról és védelméről, tekintse meg a [Application Insights az](../../azure-monitor/app/data-retention-privacy.md)adatbiztonságot ismertető témakört.
