---
title: Útmutató az Azure Log Analytics szolgáltatásban tárolt személyes adatokhoz| Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Log Analytics tárolt személyes adatok és a módszerek azonosítására és eltávolítására.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: a720627e1783d2e29ef180b7855132ea59444cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248748"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Útmutató a Log Analytics és az Application Insights által tárolt személyes adatokhoz

A Log Analytics egy olyan adattár, ahol valószínűleg személyes adatok találhatók. Az Application Insights az adatokat egy Log Analytics-partíción tárolja. Ez a cikk ismerteti, ahol a Log Analytics és az Application Insights ilyen adatok általában találhatók, valamint az ilyen adatok kezelésére rendelkezésre álló képességek.

> [!NOTE]
> Ez a cikk _naplóadatok_ a Log Analytics-munkaterületre küldött adatok, míg _az alkalmazásadatok_ az Application Insights által gyűjtött adatok.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>A személyes adatok kezelésére vonatkozó stratégia

Bár ez lesz az Ön és vállalata, hogy végső soron meghatározza a stratégiát, amellyel fogja kezelni a személyes adatok (ha egyáltalán), a következő néhány lehetséges megközelítések. Ezek technikai szempontból a legelőnyösebb ek szerint vannak felsorolva a legtöbbtől a legkevésbé előnyösebbig:

* Ahol lehetséges, állítsa le az összegyűjtött adatok gyűjtését, elhomályosítását, anonimizálását vagy más módon történő módosítását annak érdekében, hogy azokat kizárják a "magánjellegű" adatokból. Ez _messze_ az előnyben részesített megközelítés, így meg kell teremteni e nagyon költséges és hatásos adatkezelési stratégiát.
* Ahol ez nem lehetséges, próbálja meg normalizálni az adatokat, hogy csökkentse az adatplatformra és a teljesítményre gyakorolt hatást. Például ahelyett, hogy egy explicit felhasználói azonosítót naplózna, hozzon létre egy olyan keresett adatot, amely korrelálja a felhasználónevet és azok adatait egy belső azonosítóval, amelyet aztán máshol naplózhat. Így, ha az egyik felhasználó kéri, hogy törölje a személyes adatokat, lehetséges, hogy csak a felhasználónak megfelelő keressünk sor törlését. 
* Végül, ha személyes adatokat kell gyűjteni, hozzon létre egy folyamatot a kiürítési API elérési útja és a meglévő lekérdezési API elérési útja körül, hogy megfeleljen a felhasználóhoz társított személyes adatok exportálásával és törlésével kapcsolatos kötelezettségeknek. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Hol kereshet személyes adatokat a Log Analytics szolgáltatásban?

A Log Analytics egy rugalmas tároló, amely a séma felírása mellett lehetővé teszi, hogy minden mezőt egyéni értékekkel bíráljon felül. Emellett bármely egyéni séma beszámítható. Így lehetetlen pontosan megmondani, hogy hol találhatók személyes adatok az adott munkaterületen. A következő helyek azonban jó kiindulási pontok a készletben:

### <a name="log-data"></a>Naplóadatok

* *IP-címek*: A Log Analytics számos IP-adatot gyűjt számos különböző táblában. A következő lekérdezés például az okat az összes táblát megjeleníti, ahol az elmúlt 24 órában IPv4-címeket gyűjtöttek:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Felhasználói azonosítók*: A felhasználói azonosítók számos megoldásban és táblában megtalálhatók. A keresési paranccsal megkereshet egy adott felhasználónevet a teljes adatkészletben:
    ```
    search "[username goes here]"
    ```
  Ne feledje, hogy ne csak az ember által olvasható felhasználóneveket keresse, hanem a GUID-okat is, amelyek közvetlenül visszavezethetők egy adott felhasználóhoz!
* *Eszközazonosítók:* A felhasználói azonosítókhoz hasonlóan az eszközazonosítók is "privátnak" minősülnek. Használja ugyanazt a megközelítést, mint a fent felsorolt felhasználói azonosítók, hogy azonosítsa a táblákat, ahol ez aggodalomra ad okot. 
* *Egyéni adatok:* A Log Analytics lehetővé teszi a gyűjtést különböző módszerekben: egyéni naplók és egyéni mezők, a [HTTP-adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md) és a rendszer eseménynaplók részeként gyűjtött egyéni adatok. Ezek mindegyike alkalmas a személyes adatok tárolására, és meg kell vizsgálni, hogy léteznek-e ilyen adatok.
* *Megoldás által rögzített adatok:* Mivel a megoldás mechanizmus a nyílt végű, javasoljuk, hogy tekintse át az összes megoldás által létrehozott táblák megfelelőségének biztosítása érdekében.

### <a name="application-data"></a>Alkalmazásadatok

* *IP-címek*: Bár az Application Insights alapértelmezés szerint az összes IP-címmezőt "0.0.0.0"-ra fogja tázni, ez egy meglehetősen gyakori minta, amely felülbírálja ezt az értéket a tényleges felhasználói IP-vel a munkamenet-információk karbantartásához. Az alábbi Analytics-lekérdezés segítségével megkeresheti azokat a táblázatokat, amelyek az elmúlt 24 órában az IP-cím oszlopban a "0.0.0.0" értéktől eltérő értékeket tartalmaznak:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Felhasználói azonosítók:* Alapértelmezés szerint az Application Insights véletlenszerűen generált azonosítókat fog használni a felhasználók és a munkamenetek nyomon követéséhez. Azonban gyakori, hogy ezeket a mezőket felülbírálva tárolja az alkalmazás szempontjából relevánsabb azonosítót. Például: felhasználónevek, AAD GUID-ok stb. Ezeket az azonosítókat gyakran személyes adatnak tekintik, ezért megfelelően kell kezelni őket. Javasoljuk, hogy mindig megpróbálja elhomályosítani vagy anonimizálja ezeket az azonosítókat. Azok a mezők, ahol ezek az értékek általában megtalálhatók, session_Id, user_Id, user_AuthenticatedId, user_AccountId, valamint customDimensions mezőket tartalmaznak.
* *Egyéni adatok:* Az Application Insights lehetővé teszi, hogy bármilyen adattípushoz hozzáfűzze az egyéni dimenziók készletét. Ezek a dimenziók *bármilyen* adat lehetnek. Az alábbi lekérdezéssel azonosíthatja az elmúlt 24 órában gyűjtött egyéni dimenziókat:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *A memóriában és az átmenő adatok:* Az Application Insights nyomon követi a kivételeket, kéréseket, függőségi hívásokat és nyomkövetéseket. A személyes adatok gyakran a kód és a HTTP-hívás szintjén gyűjthetők. Tekintse át a kivételek, kérések, függőségek és nyomkövetési táblák az ilyen adatok azonosításához. [Telemetriai inicializálók,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) ahol lehetséges, hogy eltussolja ezeket az adatokat.
* *Pillanatkép-hibakereső rögzíti:* A [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) szolgáltatás Application Insights lehetővé teszi, hogy hibakeresési pillanatképek gyűjtése, ha egy kivétel tanusítaz alkalmazás éles példányát. A pillanatképek a kivételekhez vezető teljes veremnyomkövetést, valamint a helyi változók értékeit teszik elérhetővé a verem minden lépésében. Sajnos ez a funkció nem teszi lehetővé a snappontok szelektív törlését vagy a pillanatképen belüli adatokhoz való automatizált hozzáférést. Ezért ha az alapértelmezett pillanatkép-megőrzési arány nem felel meg a megfelelőségi követelményeknek, a javaslat az, hogy kapcsolja ki a funkciót.

## <a name="how-to-export-and-delete-private-data"></a>Személyes adatok exportálása és törlése

Amint azt a [személyes adatok kezelésére vonatkozó stratégia](#strategy-for-personal-data-handling) korábban említette, __erősen__ ajánlott, ha minden lehetséges, az adatgyűjtési szabályzat átstrukturálása a személyes adatok gyűjtésének letiltása, a személyes adatok elhomályosítása vagy anonimizálása érdekében, vagy más módon történő módosítása annak érdekében, hogy azokat "privátnak" tekintsék. Az adatok kezelése elsősorban költségeket eredményez Önnek és csapatának a stratégia meghatározásához és automatizálásához, az ügyfelek számára az adataikkal való interakcióhoz, valamint a folyamatos karbantartási költségekhez. Továbbá a Log Analytics és az Application Insights esetében számításilag költséges, és az egyidejű lekérdezési vagy kiürítési API-hívások nagy mennyisége negatívhatással lehet a Log Analytics funkcióval való minden egyéb interakcióra. Igaz, valóban vannak olyan érvényes forgatókönyvek, ahol személyes adatokat kell gyűjteni. Ezekben az esetekben az adatokat az ebben a szakaszban leírtak szerint kell kezelni.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Megtekintés és exportálás

Mind a nézet, mind az exportálási adatkérések a [Log Analytics lekérdezési API](https://dev.loganalytics.io/) vagy az Application Insights [lekérdezési API-t](https://dev.applicationinsights.io/quickstart) kell használni. Logika konvertálni az alakzatot az adatok egy megfelelő, hogy a felhasználók számára lesz, hogy végre. [Az Azure Functions](https://azure.microsoft.com/services/functions/) remek helyet ad az ilyen logika üzemeltetéséhez.

> [!IMPORTANT]
>  Bár a tisztítási műveletek túlnyomó többsége sokkal gyorsabban fejeződhet be, mint az SLA, **a tisztítási műveletek befejezésére szolgáló hivatalos SLA 30 nap,** mivel a használt adatplatformra gyakorolt súlyos hatásuk miatt. Ez egy automatizált folyamat; nincs mód arra, hogy egy művelet et gyorsabban kezeljenek.

### <a name="delete"></a>Törlés

> [!WARNING]
> A Log Analytics törlései destruktívak és nem visszafordíthatók! Kérem, legyen ek késedelmük a kivégzésükön.

A *kiürítési* API-útvonal adatvédelmi kezelésének részeként elérhetővé tettük. Ezt az elérési utat takarékosan kell használni az ezzel járó kockázat, a lehetséges teljesítményhatás, valamint a log analytics-adatok összesítésének, méréseinek és egyéb aspektusainak elferdítése miatt. A [személyes adatok kezelésére vonatkozó stratégia](#strategy-for-personal-data-handling) című részben alternatív módszereket talál a személyes adatok kezelésére.

A kiürítés egy magas szintű jogosultsággal rendelkező művelet, amelyet az Azure-ban egyetlen alkalmazás vagy felhasználó sem kap (beleértve még az erőforrás tulajdonosát is) anélkül, hogy kifejezetten szerepkört kapna az Azure Resource Managerben. Ez a szerepkör _data purger,_ és óvatosan kell delegálni az adatvesztés lehetősége miatt. 

> [!IMPORTANT]
> A rendszererőforrások kezelése érdekében a kiürítési kérelmek óránként 50 kérelemesetén vannak szabályozva. A kiürítési kérelmek végrehajtását egyetlen parancs elküldésével kell kötegelnie, amelynek predikátuma tartalmazza az összes olyan felhasználói identitást, amely tisztítást igényel. Az [in operátorral](/azure/kusto/query/inoperator) több identitást adhat meg. A kiürítési kérelem végrehajtása előtt futtassa a lekérdezést, és ellenőrizze, hogy az eredmények várhatók-e. 



Az Azure Resource Manager szerepkör hozzárendelése után két új API-elérési út érhető el: 

#### <a name="log-data"></a>Naplóadatok

* [POST purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - az adatok paramétereit meghatározó objektumot a törléshez, és egy referencia GUID azonosítót ad vissza 
* Get purge status - a POST kiürítési hívás visszaad egy "x-ms-status-location" fejlécet, amely tartalmazni fog egy URL-t, amely et hívhat meg a kiürítési API állapotának meghatározásához. Példa:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Bár arra számítunk, hogy a tisztítási műveletek túlnyomó többsége sokkal gyorsabban befejeződik, mint az SLA, mivel azok nagy hatással vannak a Log Analytics által használt **adatplatformra, a tisztítási műveletek befejezésére szolgáló hivatalos SLA 30 nap.** 

#### <a name="application-data"></a>Alkalmazásadatok

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - az adatok paramétereit meghatározó objektumot a törléshez, és egy referencia GUID azonosítót ad vissza
* Get purge status - a POST kiürítési hívás visszaad egy "x-ms-status-location" fejlécet, amely tartalmazni fog egy URL-t, amely et hívhat meg a kiürítési API állapotának meghatározásához. Példa:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Bár a kiürítési műveletek túlnyomó többsége sokkal gyorsabban fejeződhet be, mint az SLA, mivel azok nagy hatással vannak az Application Insights által használt **adatplatformra, a tisztítási műveletek befejezéséhez szükséges hivatalos SLA 30 nap.**

## <a name="next-steps"></a>További lépések
- Ha többet szeretne megtudni arról, hogyan gyűjti, dolgozza fel és biztosítja a Log Analytics-adatokgyűjtését, feldolgozását és védelmét, olvassa el a [Log Analytics-adatok biztonsága](../../azure-monitor/platform/data-security.md).
- Ha többet szeretne megtudni az Application Insights-adatok gyűjtéséről, feldolgozásáról és biztonságos kezeléséről, olvassa el az [Application Insights adatbiztonsága.](../../azure-monitor/app/data-retention-privacy.md)
