---
title: Útmutató a személyes adatok Azure Application Insights tárolt |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Application Insights és a metódusok azonosításához és eltávolításához található személyes adatokat.
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
ms.openlocfilehash: 95e421278b46015e761764792e11dec0351b9785
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294421"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Az Application Insights a személyes adataihoz útmutatást

Az Application Insights, ahol a személyes adatok valószínűleg megtalálhatók az adattárat. A cikk ismerteti, ahol az Application Insightsban ezeket az adatokat általában megtalálható, továbbá az elérhető lehetőségeket ezen adatok kezeléséhez.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Személyes adatok kezelésére stratégia

Akár Ön és vállalata végső soron meghatározásához a stratégiát, amellyel a saját kezelnek lesz adatok (ha egyáltalán), amíg az alábbiakban néhány lehetséges módszer. Egy technikai szempontból a leginkább preferencia szerinti sorrendben szerepelnek a legalább előnyösebb:
* Ahol lehetséges, állítsa le a gyűjteménye, takarják, anonimizálásához vagy más módon állítsa be a kizárandó szabálysértésnek minősülő gyűjtött adatokra _titkos_. Ez a módszer az előnyben részesített módszert használja, így meg lehet spórolni a költséges és impactful adatkezelési stratégia létrehozásához szükség.
* Ha nem lehetséges, próbálja meg az adatok csökkenteni a hatással lehet az adatok platform és a teljesítmény optimalizálása. Naplózás egy explicit felhasználói Azonosítóját, helyett hozzon létre például egy értékét a felhasználónév fog gyűjtött adatokat és a Részletek gombra, majd bejelentkezhet máshol belső azonosító. Így, kérje egy felhasználó személyes adataik törlését esetén lehetséges, hogy a csak a a keresési táblában a felhasználónak megfelelő a sor törlésével elegendő lesz. 
* Ha a titkos adatokat kell gyűjteni, végezetül fordítsa le a folyamat a kiürítési API elérési út és a meglévő lekérdezés API elérési út bármely kötelezettségek, előfordulhat, hogy exportálása és a felhasználóhoz társított titkos adatok törlése kielégítése érdekében.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Hol személyes adatokat a Application Insights?

Az Application Insights egy teljesen rugalmas tárolás közben az adatok egy séma előíró lehetővé teszi minden mezőnek egyedi értékekkel felülbírálását. Ilyen lehetetlen tegyük fel például, pontosan ahol titkos adatok találhatók az adott alkalmazásban. A következő helyeken, azonban nem jó kezdőpont a leltárban:

* *IP-címek*: közben az Application Insights lesz alapértelmezés szerint takarják összes IP-cím mezők "0.0.0.0", de egy viszonylag megszokott mintát követi ezt az értéket a tényleges felhasználói IP-cím kötése a munkamenet-információk karbantartása felülbírálására. Az alábbi lekérdezést a Analytics segítségével bármely az elmúlt 24 óra során az IP-cím oszlopot nem "0.0.0.0" értékeit tartalmazó tábla található:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Felhasználói azonosítók*: alapértelmezés szerint az Application Insights és fogja használni véletlenszerűen létrehozott azonosítókat felhasználói munkamenet-követési. Azonban esetében gyakori, hogy ezek a mezők felül még relevánsabbá teheti az alkalmazás Azonosítóját tárolja. Például: felhasználónevek, AAD GUID, stb. Ezek az azonosítók gyakran kell tekinteni a-hatókör személyes adatokat, és ezért kezelje megfelelően. Azt javasoljuk, mindig takarják vagy ezek az azonosítók anonimizálásához kísérletet. Ha ezek az értékek gyakran talált mezők száma session_Id, user_Id, user_AuthenticatedId, user_AccountId, valamint customDimensions tartalmazza.
* *Egyéni adatok*: Application Insights lehetővé teszi egy egyéni dimenziók készletét hozzáfűzése a bármely adattípushoz. Ezeknek a dimenzióknak lehet *bármely* adatokat. A következő lekérdezés segítségével bármilyen egyéni dimenziók az elmúlt 24 óra során gyűjtött azonosíthatók.
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *A memória és az átvitel közbeni adatok*: Application Insights fogja követni a kivételeket, kérelmek, függőségi hívásokat és nyomkövetési adatokat. Gyakran személyes adatokat lehessen gyűjteni a kód és a HTTP-hívás szintjén. Tekintse át a kivételek, kérelmek, függőségeket, és nyomkövetési táblák ilyen jellegű adatokat azonosításához. Használjon [telemetriai inicializálók](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) ahol lehetséges, a takarják ezeket az adatokat.
* *Pillanatkép-hibakereső rögzítésekre*: A [pillanatkép hibakereső](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) az Application Insights szolgáltatás lehetővé teszi a hibakeresési-pillanatképek gyűjtése, amikor egy kivétel az alkalmazás az éles példányán. A pillanatképek megmutatják a teljes Veremkivonat, ami a kivételeket, valamint a verem minden lépésnél helyi változók értékeit. Sajnos ez a funkció nem érhető el beépülő pontokat, vagy a pillanatkép adatainak programozott hozzáférést szelektív törlését. Ezért az alapértelmezett pillanatkép megőrzési sebessége nem felel meg a megfelelőségi követelményeket, ha a javasoljuk, hogy a szolgáltatás kikapcsolásához.

## <a name="how-to-export-and-delete-private-data"></a>Hogyan kell exportálni, és a személyes adatok törlése

Az __erősen__ ajánlott, ha lehetséges, az adatvédelmi nyilatkozat obfuscating, vagy névtelenítését, a személyes adatok gyűjtésének a letiltása átalakításának vagy egyéb módosításához eltávolítása szabálysértésnek minősülő "privát." Kezeli az adatokat a után az összegyűjtött óta, ha Ön és a csoport határozza meg és automatizálhatja a stratégia, build egy felület, az ügyfelek keresztül az adatok kezeléséhez, és folyamatos karbantartási költségek eredményez. További az Application Insights és a nagy egyidejű lekérdezés számításilag költséges vagy kiürítése API-hívások esetleg negatívan minden más tevékenység Application Insights szolgáltatással. Amely az említett, valóban néhány érvényes esetben hova legyenek összegyűjtve személyes adatokat. Ezekben az esetekben az adatok a kezelt ebben a szakaszban leírtak szerint.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Megtekintése és exportálása

Az is megtekintheti, és exportálhatja a kérelmek, a [lekérdezés API](https://dev.applicationinsights.io/quickstart) kell használni. Az alakzat az adatok átalakítása telepíthet a felhasználók számára egy megfelelő logika végrehajtásához Öntől lesz. [Az Azure Functions](https://azure.microsoft.com/services/functions/) mutassa ilyen logika üzemeltetéséhez.

### <a name="delete"></a>Törlés

> [!WARNING]
> Törli az Application Insightsban ilyen beállítások mellett pusztító és nem visszafejthető! A végrehajtási körültekintően használja.

Hajtottunk érhető el, egy adatvédelmi kezelési részét szövegegység "kiürítése" API elérési útja. Az elérési út takarékosan a kockázatának így, mert a teljesítményre gyakorolt lehetséges hatásának, és így teljes összesítéseket, mérések és egyéb szempontok az Application Insights adatok eltolódhat. Tekintse meg a [személyes adatok kezelésére stratégia](#strategy-for-personal-data-handling) a személyes adatok kezelésének alternatív módszerek fenti szakaszban.

Kiürítési egy olyan magas szintű jogosultsággal rendelkező művelet, hogy egyetlen alkalmazás vagy a felhasználó (beleértve az erőforrás tulajdonosa is) az Azure-ban végrehajtása nélkül explicit módon megadott egy szerepkört az Azure Resource Manager engedélyeket kapnak. Ez a szerepkör _adatok Purger_ és az esetleges adatvesztés gondosan delegálni.

Miután az Azure erőforrás-kezelő szerepkör van hozzárendelve, két új API-elérési utak érhető el, teljes fejlesztői dokumentáció és a kapcsolódó alakzat hívja:

* [POST kiürítése](https://docs.microsoft.com/rest/api/application-insights/components/purge) - adatok törlése paramétereinek megadása objektum vesz igénybe, és visszaad egy hivatkozást GUID
* GET kiürítése állapota - a FELADÁS egy vagy több kiürítése hívás ad vissza egy "x-ms-állapot-location" fejlécet, amelyek tartalmazzák azt egy URL-címet, amely nem sikerült meghatározni az állapotát a kiürítési API hívása. Példa:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

Amíg kiürítése műveletek túlnyomó sokkal gyorsabb, mint az SLA-t, az Application Insights által használt adatok platform nehéz gyakorolt hatásának miatt előfordulhat, hogy befejezéséhez a formális SLA a műveletek befejezését követő kiürítése nem állítják 30 nap.

## <a name="next-steps"></a>További lépések
Milyen adatok gyűjtése, feldolgozása, és biztosított kapcsolatos további információkért lásd: [Application Insights adatbiztonság](app-insights-data-retention-privacy.md).