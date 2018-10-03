---
title: Útmutató az Azure Log Analytics szolgáltatásban tárolt személyes adatok |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Log Analytics és a metódusok azonosításához és eltávolításához tárolt személyes adatok.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 1b9d8d252055bb1368b7024ca9b0acf6c21ba7b3
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041611"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Útmutató a Log Analytics és az Application Insights tárolt személyes adatok

A log Analytics egy adattárba, ahol a személyes adatok valószínűleg találhatók a. Az Application Insights egy Log Analytics a partíción tárolja az adatokat. Ez a cikk bemutatja, hogyan lehet, a Log Analytics és az Application Insights az ilyen adatok általában megtalálhatók, valamint az ilyen adatok kezelésének elérhető lehetőségeket.

> [!NOTE]
> Ez a cikk az alkalmazásában _adatok_ hivatkozik egy Log Analytics-munkaterületen történő adatküldés közben _alkalmazásadatok_ Application Insights által gyűjtött adatok értendők.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Személyes adatok kezelésére vonatkozó stratégia

Bár Ön és vállalata végső soron meghatározni a stratégiát, amellyel a privát fogja kezelni akár lesz adatok (ha egyáltalán), az alábbiakban néhány olyan lehetséges módszert. Egy technikai szempontból a leginkább sorrendben szerepelnek a legkevésbé előnyösebb:

* Ahol lehetséges, állítsa le a gyűjteményét, rejtse, anonimizálása, vagy ellenkező esetben módosítsa a "privát" fontolóra kizárása gyűjtött adatokra. Ez a _messze_ az előnyben részesített módszert használja, így egy nagyon költséges és hatásos adatkezelési stratégia létrehozása.
* Ha nem lehetséges, próbálja meg az adatokat a hatást a data platform és a teljesítmény optimalizálása. Naplózás egy explicit felhasználói azonosító, helyett hozzon létre például egy keresési adatok, amelyek fog korrelációját, ha a felhasználónév és a egy belső azonosítója, amely máshol majd naplózni az adataikat. Ezzel a módszerrel egy felhasználó kérdezze meg, hogy törli a személyes adatokat, akkor lehet, hogy a csak a a keresési táblában a felhasználónak megfelelő a sor törlésével elegendő lesz. 
* Végül ha személyes adatokat kell összegyűjteni, egy folyamatra a végleges törlés API elérési útja és a meglévő lekérdezés API elérési út bármely kötelezettségek exportálása és törlése a felhasználóhoz kapcsolódó személyes adatokat lehet megfelelni hozhat létre. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Ahol a keresett személyes adatokat a Log Analytics?

A log Analytics egy rugalmas tárolja, ami előíró egy sémát az adatokhoz, miközben az egyéni értékeket minden mezőben felülírását is lehetővé teszi a. Emellett minden olyan egyéni séma olvasódnak. Emiatt a lehetetlen tegyük fel, hogy pontosan hol személyes adatok találhatók az adott munkaterületen. A következő helyeken, azonban olyan jó kiindulási pontként a készlet:

### <a name="log-data"></a>Naplóadatok

* *IP-címek*: a Log Analytics különböző IP-adatokat gyűjt a számos különböző táblákon. Például a következő lekérdezés látható összes tábla ahol IPv4-címek összegyűjtése az elmúlt 24 órában:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Felhasználói azonosítók*: felhasználói azonosítók találhatók megoldások és a táblák nagy különböző. Egy adott felhasználónév között a teljes adatkészlet, a keresési paranccsal megkeresheti:
    ```
    search "[username goes here]"
    ```
Keresse meg a nemcsak a természetes nyelven olvasható felhasználónevek, hanem közvetlenül visszakövethető egy adott felhasználó GUID-azonosítói. ne felejtse el!
* *Az eszközazonosítókat*: például a "felhasználói azonosítókat, eszközazonosítókat néha titkosnak számítanak". Használja ugyanazt a megközelítést felhasználói azonosítók a fent felsorolt azonosítja, ha ez egy potenciálisan veszélyes lehet. 
* *Egyéni adatok*: a Log Analytics lehetővé teszi, hogy a különböző módszereiről ad a gyűjteményhez: egyéni naplók és az egyéni mezők a [HTTP-adatgyűjtő API](log-analytics-data-collector-api.md) , és egyéni adatokat gyűjteni a rendszer eseménynaplóit részeként. Ezek mindegyikét ki van téve a titkos adatokat tartalmazó, és ellenőrizze, hogy létezik-e ilyen jellegű adatokat meg kell vizsgálni.
* *Megoldás rögzített adatoknak*: mivel a megoldás mechanizmus egy nyílt, ajánlott áttekinteni a megoldások a megfelelőség biztosítása által létrehozott összes tábla.

### <a name="application-data"></a>Alkalmazásadatok

* *IP-címek*: Bár az Application Insights lesz alapértelmezés szerint rejtse "0.0.0.0" az összes IP-mezők, mert viszonylag gyakori minta felülbírálhatja ezt az értéket a tényleges felhasználói IP-cím munkamenet-információk karbantartásához. Az alábbi elemzési lekérdezés használható bármely az elmúlt 24 órában az IP-cím oszlopot "0.0.0.0" eltérő értékeket tartalmazó táblázat található:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Felhasználói azonosítók*: alapértelmezés szerint az Application Insights fogja használni véletlenszerűen létrehozott azonosítókat a felhasználó és a munkamenet-követési. Azonban szokás ezen felül tárolásához egy további, az alkalmazáshoz kapcsolódó azonosító mező megjelenítéséhez. Például: AAD GUID, felhasználónevek, stb. Ezek az azonosítók gyakran kell tekinteni releváns személyes adatokat, és ezért kezelje megfelelően. Azt javasoljuk, mindig megpróbálja rejtse vagy anonimizálása azonosítóit a részletekben. Ha ezek az értékek gyakran találnak mezők munkamenet-azonosítónak, a USER_ID paraméter értékét, user_AuthenticatedId, user_AccountId, valamint customDimensions tartalmazza.
* *Egyéni adatok*: Application Insights lehetővé teszi, hogy az egyéni készletét hozzáfűzése a bármilyen típusú adatot. Ezeknek a dimenzióknak lehet *bármely* adatokat. Az elmúlt 24 órában gyűjtött minden olyan egyéni dimenziók azonosításához használja a következő lekérdezést:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *A memóriában, és az átvitel közbeni adatok*: Application Insights fogja követni a kivételeket, a kérelmek, a függőségi hívások és a nyomkövetéseket. Személyes adatok gyakran a kód és a HTTP-hívás szintjén lehessen gyűjteni. Tekintse át a kivételek, kérelmeket, függőségeket és nyomkövetési táblák azonosításához az ilyen jellegű adatokat. Használat [telemetriai inicializálók](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) ahol lehetséges, a rejtse ezeket az adatokat.
* *Pillanatkép-hibakereső rögzíti*: A [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) az Application Insights szolgáltatás lehetővé teszi, amikor egy kivétel történt az alkalmazás az éles üzemelő példányok a hibakeresési pillanatképek összegyűjtése. A pillanatképek megmutatják a teljes híváslánc és a kivételeket, valamint a verem minden lépésnél helyi változók értékeit. Sajnos ez a funkció nem engedélyezi az illesztési pont vagy programozott hozzáférést a pillanatkép lévő adatok szelektív törlését. Ezért ha az alapértelmezett pillanatkép megtartási aránya nem felel meg a megfelelőségi előírásokat, a javaslat, hogy kapcsolja ki a szolgáltatást.

## <a name="how-to-export-and-delete-private-data"></a>És törölje a személyes adatok exportálása

Említetteknek megfelelően a [személyes adatok kezelésére vonatkozó stratégia](#strategy-for-personal-data-handling) szakasz korábbi, __erősen__ ajánlott, ha azt minden lehetséges átalakításának az adatvédelmi nyilatkozat a gyűjtésének a letiltása személyes adatok, obfuscating névtelenítését, vagy egyéb módosítása, hogy távolítsa el a "privát" minősül. Az adatok fognak legelső eredményez az Ön és csapata definiálása és stratégia automatizálása, hozhat létre egy felület, az ügyfeleink számára, hogy az adataikat, és folyamatos karbantartást költségek kezelésére. További a Log Analytics és az Application Insights és a egy igen nagy mennyiségű párhuzamos lekérdezés nagy számítási költséges vagy kiürítése API-hívások esetleg negatív hatással a Log Analytics-funkciók más tevékenység. Te is említetted, hogy valóban néhány érvényes esetben hova legyenek összegyűjtve személyes adatokat. Ezekben az esetekben az adatokat a kezelt ebben a szakaszban leírtak szerint.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Megtekintése és exportálása

Mindkét megtekintése és exportálása a kérelmek, a [Log Analytics lekérdezési API](https://dev.loganalytics.io/) vagy a [Application Insights API lekérdezése](https://dev.applicationinsights.io/quickstart) kell használni. Az alakzat az adatok átalakítása a felhasználók számára egy megfelelő logikai lesz legfeljebb kell megvalósítania. [Az Azure Functions](https://azure.microsoft.com/services/functions/) segítségével mutassa be ilyen logikai üzemeltetéséhez.

### <a name="delete"></a>Törlés

> [!WARNING]
> Törli a Log Analytics pusztító és nem visszafejthető áll. A végrehajtási járjon el körültekintően használja.

Elérhetővé vált egy adatvédelmi kezelési részeként egy *kiürítése* API elérési útja. Ezt az elérési utat takarékosan miatt a kockázatának ilyen esetben a teljesítményre gyakorolt lehetséges hatás, és így tevékenységdiagramon teljes összesítések, a mértékek és a Log Analytics-adatok más aspektusait. Tekintse meg a [személyes adatok kezelésére vonatkozó stratégia](#strategy-for-personal-data-handling) személyes adatok kezelésének alternatív módszer a következő szakaszban.

Végleges törlés egy olyan magas jogosultsági szintű művelet, hogy nincs alkalmazás vagy a felhasználó az Azure-ban (beleértve a még az erőforrás tulajdonosa) engedéllyel rendelkezik majd hajtható végre explicit módon nélkül biztosított egy szerepkört az Azure Resource Manager. Ez a szerepkör _adatok Purger_ és az esetleges adatvesztés óvatosan delegálni. 

Miután az Azure Resource Manager-szerepkör van rendelve, két új API-útvonalon érhetők el: 

#### <a name="log-data"></a>Naplóadatok

* [Közzététel kiürítése] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - objektumhoz, az adatok törlése paramétereinek megadása szükséges, és adja vissza a hivatkozás GUID azonosítója 
* GET kiürítése állapota - a POST kiürítés hívásához egy "x-ms-állapot – location" fejléc tartalmazza egy URL-címet, amely segítségével meghívhatja a végleges törlés API állapotának meghatározásához adja vissza. Példa:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Miközben sokkal gyorsabb, mint az SLA-t, a Log Analytics által használt data platform (nagy erőforrásigényű) gyakorolt miatt végrehajtásához kiürítése műveletek túlnyomó többsége várhatóan **a formális SLA-kiürítési művelet befejezése után van beállítva, 30 napig**. 

#### <a name="application-data"></a>Alkalmazásadatok

* [POST kiürítése](https://docs.microsoft.com/rest/api/application-insights/components/purge) - objektumhoz, az adatok törlése paramétereinek megadása szükséges, és adja vissza a hivatkozás GUID azonosítója
* GET kiürítése állapota - a POST kiürítés hívásához egy "x-ms-állapot – location" fejléc tartalmazza egy URL-címet, amely segítségével meghívhatja a végleges törlés API állapotának meghatározásához adja vissza. Példa:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Amíg a végleges törlés műveletek túlnyomó többsége sokkal gyorsabb, mint az SLA-t, az Application Insights által használt data platform (nagy erőforrásigényű) gyakorolt miatt előfordulhat, hogy befejezéséhez **a formális SLA-kiürítési művelet befejezése után van beállítva, 30 napig**.

## <a name="next-steps"></a>További lépések
- A Log Analytics-adatok gyűjtött, feldolgozott és védett kapcsolatos további információkért lásd: [Log Analytics és adatbiztonság](log-analytics-data-security.md).
- Az Application Insights-adatok gyűjtött, feldolgozott és biztonságos kapcsolatos további információkért lásd: [Application Insights által nyújtott adatbiztonság](../application-insights/app-insights-data-retention-privacy.md).