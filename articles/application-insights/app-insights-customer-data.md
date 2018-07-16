---
title: Útmutató az Azure Application insights szolgáltatásban tárolt személyes adatok |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Application Insights és a metódusok azonosításához és eltávolításához tárolt személyes adatok.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044713"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Az Application insights szolgáltatásban tárolt személyes adatokra vonatkozó útmutatás

Application Insights jelenleg egy adattárba, ahol a személyes adatok valószínűleg találhatók. Ez a cikk ismerteti, ahol az Application insights szolgáltatásban az adatok általában megtalálhatók, valamint elérhető lehetőségeket ezen adatok kezelésének.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Személyes adatok kezelésére vonatkozó stratégia

Bár Ön és vállalata végső soron meghatározni a stratégiát, amellyel a privát fogja kezelni akár lesz adatok (ha egyáltalán), az alábbiakban néhány olyan lehetséges módszert. Egy technikai szempontból a leginkább sorrendben szerepelnek a legkevésbé előnyösebb:
* Ahol lehetséges, az adatgyűjtés leállítása, rejtse, anonimizálása vagy ellenkező esetben módosítsa a fontolóra kizárása gyűjtött adatokra _privát_. Ez a módszer a legmegfelelőbb módszer, így egy költséges és hatásos adatkezelési stratégia létrehozása.
* Ha nem lehetséges, próbálja meg az adatokat a hatást a data platform és a teljesítmény optimalizálása. Naplózás egy explicit felhasználói azonosító, helyett hozzon létre például egy keresési adatok fog korrelációját, ha a felhasználónév és a egy belső azonosítója, amely máshol majd naplózni az adataikat. Így ha egy felhasználó lehetővé kérheti, hogy törli a személyes adatokat, lehetséges, hogy a csak a a keresési táblában a felhasználónak megfelelő a sor törlésével elegendő lesz. 
* Végül ha személyes adatokat kell összegyűjteni, egy folyamatra a végleges törlés API elérési útja és a meglévő lekérdezés API elérési út bármely kötelezettségek exportálása és törlése a felhasználóhoz kapcsolódó személyes adatokat lehet megfelelni hozhat létre.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Hol keresse meg az Application Insights személyes adatot?

Application Insights jelenleg egy teljesen rugalmas tárolja, ami előíró egy sémát az adatokhoz, miközben az egyéni értékeket minden mezőben felülírását is lehetővé teszi. Emiatt a lehetetlen tegyük fel, hogy pontosan hol személyes adatok találhatók az adott alkalmazás. A következő helyeken, azonban olyan jó kiindulási pontként a készlet:

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

Ez __erősen__ ajánlott, ha lehetséges, az adatvédelmi nyilatkozat a obfuscating, vagy névtelenítését, a személyes adatok gyűjtésének a letiltása átalakításának vagy más módon módosítja ezt tekinti "privát" eltávolítása Az adatok kezelésének alapelveit után azt összegyűjtött lett, az Ön és csapata definiálása és stratégia automatizálása, hozhat létre egy felület, az ügyfeleink számára, hogy az adataikat, és folyamatos karbantartást költségek eredményez. További az Application Insights és a egy igen nagy mennyiségű párhuzamos lekérdezés nagy számítási költséges vagy kiürítése API-hívások esetleg negatív hatással az Application Insights funkcióit az összes többi-szal. Te is említetted, hogy valóban néhány érvényes esetben hova legyenek összegyűjtve személyes adatokat. Ezekben az esetekben az adatokat a kezelt ebben a szakaszban leírtak szerint.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Megtekintése és exportálása

Mindkét megtekintése és exportálása a kérelmek, a [Query API](https://dev.applicationinsights.io/quickstart) kell használni. Az alakzat az adatok átalakítása a felhasználók számára egy megfelelő logikai lesz legfeljebb kell megvalósítania. [Az Azure Functions](https://azure.microsoft.com/services/functions/) ilyen logikai üzemeltetésére mutassa.

### <a name="delete"></a>Törlés

> [!WARNING]
> Törli az Application Insights pusztító és nem visszafejthető áll. A végrehajtási járjon el körültekintően használja.

Elérhetővé vált, egy adatvédelmi kezelési részét szövegegység API "kiürítése" elérési utat. Ezt az elérési utat takarékosan miatt a kockázatának ilyen esetben a teljesítményre gyakorolt lehetséges hatás, és így tevékenységdiagramon teljes összesítések, mérés és az Application Insights-adatok más aspektusait. Tekintse meg a [személyes adatok kezelésére vonatkozó stratégia](#strategy-for-personal-data-handling) személyes adatok kezelésének alternatív módszerek fenti szakasz.

Végleges törlés egy olyan magas jogosultsági szintű művelet, hogy nincs alkalmazás vagy a felhasználó az Azure-ban (beleértve a még az erőforrás tulajdonosa) engedéllyel rendelkezik majd hajtható végre explicit módon nélkül biztosított egy szerepkört az Azure Resource Manager. Ez a szerepkör _adatok Purger_ és az esetleges adatvesztés gondosan delegálni.

Miután az Azure Resource Manager-szerepkör van rendelve, két új API-elérési érhető el, a teljes fejlesztői dokumentáció, csatolt alakzat hívja:

* [POST kiürítése](https://docs.microsoft.com/rest/api/application-insights/components/purge) - objektumhoz, az adatok törlése paramétereinek megadása szükséges, és adja vissza a hivatkozás GUID azonosítója
* GET kiürítése állapota - a POST kiürítés hívásához egy "x-ms-állapot – location" fejléc tartalmazza egy URL-címet, amely segítségével meghívhatja a végleges törlés API állapotának meghatározásához adja vissza. Példa:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Amíg a végleges törlés műveletek túlnyomó többsége sokkal gyorsabb, mint az SLA-t, az Application Insights által használt data platform (nagy erőforrásigényű) gyakorolt miatt előfordulhat, hogy befejezéséhez **a formális SLA-kiürítési művelet befejezése után van beállítva, 30 napig**.

## <a name="next-steps"></a>További lépések
Adatok gyűjtése, feldolgozása és biztonságos kapcsolatos további információkért lásd: [Application Insights által nyújtott adatbiztonság](app-insights-data-retention-privacy.md).