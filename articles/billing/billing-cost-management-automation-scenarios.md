---
title: Azure-elszámolással és a cost management automation forgatókönyvek |} A Microsoft Docs
description: Ismerje meg, hogyan közös számlázás, és optimalizálhatja a költségeket felügyeleti forgatókönyvek különböző leképezett API-kkal.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 97f6339e5c9532250245a97a81dade12db139e98
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227367"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Számlázási és a cost management automation forgatókönyvek

Általános forgatókönyvek a számlázási és a cost management terület neve alább, és különböző API-kat azokra használható leképezve. Minden API-kat és a funkciókat kínálnak a forgatókönyvet, az API leképezési alatt található. 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Használhatja a számlázás és Költségkezelés számos forgatókönyv esetében a válasz felhasználási és API-k kapcsolatos kérdésekre.  Röviden ismerteti a gyakori forgatókönyvek lejjebb találja.

- **Egyeztetés a számlához** -fejeződött Microsoft díja szerint számítjuk fel velem a megfelelő összeget?  Mi az a számlán, és szeretnék kiszámíthatja, saját magam?

- **Adatbázisközi díjak** – most, hogy tudom, hogy mennyit vagyok éppen fizetnem, mely munkatársaim kell fizetnem kell?

- **Költségek optimalizálása** -tudom, hogy mennyivel korábban lett számítanak fel díjat, azonban Hogyan kaphatok további kívül az Azure-beli töltök pénzt?

- **Költségek nyomon követése** – mennyi vagyok kiadások és az Azure idővel látni. Mik azok a trendek? Hogyan sikerült I foglalják jobban?

- **Azure-költések kezelése a hónap során** – dátum mennyi van az aktuális hónap a költségek? Kell végezze el a módosításokat a költségkeret-beállítási és/vagy az Azure használatát? Ha a hónap során vagyok I használó Azure a legtöbbet?

- **Riasztásokat állíthat be** – szeretnék kapni az erőforrás-alapú használat vagy pénzügyi alapuló riasztások beállításához.

## <a name="scenario-to-api-mappings"></a>Az API leképezések forgatókönyv

|         API-forgatókönyv        | Számla egyeztetés    | Adatbázisközi díjak    | Költségek optimalizálása    | Költségek nyomon követése    | Mid havi költség    | Riasztások    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Költségkeret                     |                           |                  |           X          |                  |                    |     X     |
| Piactér                |             X             |         X        |           X          |         X        |          X         |     X     |
| Árlista                 |             X             |         X        |           X          |         X        |          X         |           |
| Foglalás javaslatok |                           |                  |           X          |                  |                    |           |
| Foglalás részletei         |                           |                  |           X          |         X        |                    |           |
| Foglalás összefoglaló       |                           |                  |           X          |         X        |                    |           |
| Használat részletei               |             X             |         X        |           X          |         X        |          X         |     X     |
| Számlázási időszak             |             X             |         X        |           X          |         X        |                    |           |
| Számlák                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nem értékelt használat               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> A forgatókönyvhöz az alábbi API leképezések a nem tartalmazzák a nagyvállalati szolgáltatáshasználati API-jai. Ahol lehetséges, adjon az általános felhasználás, oldja meg megszűnni nettó új fejlesztési forgatókönyvek API-jainkkal.

## <a name="api-summaries"></a>API összefoglaló

### <a name="consumption"></a>Használat
(*Web Direct és vállalati felhasználók az összes API-k, kivéve azokat nevű meg alább*)

-   **Költségvetése** (*csak vállalati ügyfelek*): használja a [költségvetése API](https://docs.microsoft.com/rest/api/consumption/budgets) költség- és használati költségvetés erőforrások, erőforráscsoportok vagy a számlázási mérőszámok létrehozására.  Miután létrehozta a költségvetés, riasztási beállítható úgy, hogy értesítése, ha a felhasználó által definiált költségvetés küszöbérték túllépése. Műveletek fordulhat elő, ha eléri a költségvetési összegeket is konfigurálható.
-   **Piactér**: használja a [Marketplace-díjakat API](https://docs.microsoft.com/rest/api/consumption/marketplaces) díj és használati adatok részletes a Marketplace-erőforrások (Azure 3. fél ajánlatok). Ezek az adatok között minden Marketplace-erőforrások költségeit összeadódhatnak, vagy vizsgálja meg az adott erőforrás(ok) költségek/használati használható.
-   **Árlista** (*csak vállalati ügyfelek*): nagyvállalati ügyfeleink a [ár lap API](https://docs.microsoft.com/rest/api/consumption/pricesheet) beolvasni az egyéni díjszabás minden mérőszámok. Vállalatok használhatja ezeket az adatokat és használati adatokat és a piactér használati adatok együttes alkalmazásával-használat és a piactér adatok számítások végrehajtásához. 
-   **Foglalás javaslatok**: használja a [foglalás ajánlatok API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) Reserved VM Instances megvásárlásával kapcsolatos javaslatokat beolvasni. Javaslatok célja, hogy ügyfeleink várt költségmegtakarítást elemzése és beszerzési összegeket.
-   **Foglalás részletei**: használja a [foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) adatok megjelenítéséhez a korábban megvásárolt Virtuálisgép-foglalásokat, például mennyi fogyasztás van fenntartva és mennyi ténylegesen használatban van. Egy Virtuálisgép-szintű részletes adatait tekintheti meg.
-   **Foglalás összegzéseket**: használja a [foglalás összegzéseket API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) összesített információinak megtekintéséhez korábban megvásárolt Virtuálisgép-foglalásokat, például mennyi fogyasztás van fenntartva és mennyi ténylegesen használatban van az összesítésben. 
-   **Használat részleteiről**: használja a [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails) díj és használati beolvasni az összes Azure-on 1. fél erőforrásokat. Információk a rendszer jelenleg a kibocsátott egyszer mérni / nap / használati részletes rekordok formájában van. Adatok mentése a költségek az összes erőforrás között hozzáadásához, vagy vizsgálja meg az adott erőforrás(ok) költségek/használati használhatók.
-   **RateCard**: Web Direct-ügyfeleink a [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) beolvasni a mérőszám díjait. A visszaadott információ az erőforrás-használat adataik manuálisan a várt számla kiszámításának indítására használhatja. 
-   **Használat nem minősített**: használhatja a [Usage API-t nem értékelt](https://msdn.microsoft.com/library/azure/mt219003.aspx) előtt bármely mérési/díjszabási Azure által végzett nyers használati adatainak beszerzése.

### <a name="billing"></a>Számlázás
-   **Számlázási időszak**: használja a [elszámolási időszakok API](https://docs.microsoft.com/rest/api/billing/billingperiods) elemzéséhez, a számla együtt a számlázási időszak meghatározásához az adott időszak konfigurációba. Számla azonosítóhoz tartozó alábbi a számlák API-val használható. 
-   **Számlák**: használja a [számlák API](https://docs.microsoft.com/rest/api/billing/invoices) a letöltési URL-Címének lekéréséhez az adott számlához egy adott elszámolási időszakban, csak PDF formátumban.

### <a name="enterprise-consumption"></a>Vállalati fogyasztás
*(Az összes API-k Enterprise csak)*

-   **Egyenleg összesítése**: használja a [egyenleg összefoglaló API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) beolvasni egy-egy havi összefoglaló információkat a jóváírásokat összefoglaló balances, új vásárlások, Azure Marketplace szolgáltatási díjai, módosítását és kerettúllépési díjfizetést tesz szükségessé. Ez az aktuális elszámolási időszakban vagy bármelyik az elmúlt időszakban információkat szerezhet a. Vállalatok számára az adatok hajthat végre kézzel számított összefoglaló díjak összehasonlítása. Ez az API nem biztosít erőforrás-specifikus információt, vagy egy összesített nézetet a költségek.
-   **Használat részleteiről**: használja a [használati részletek API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) beolvasni az Azure-használat részletes információkat az aktuális hónap, egy adott számlázási időszak és a egy egyéni dátum-idő 1. fél. A vállalatok használhatja ezeket az adatokat manuálisan a sebesség és a fogyasztás alapján számla kiszámításának, és a szervezetek közötti attribútum a költségek szervezeti egység vagy szervezet adatai is használhatja. Az adatok a használat és költségek erőforrás-specifikus nézetét jeleníti meg.
-   **Marketplace áruházban ingyenesen**: használja a [Marketplace-díj API Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) beolvasni a 3. fél Azure-használat részletes információkat az aktuális hónap, egy adott számlázási időszak és a egy egyéni dátum-idő. A vállalatok használhatja ezeket az adatokat manuálisan a sebesség és a fogyasztás alapján számla kiszámításának, és a szervezetek közötti attribútum a költségek szervezeti egység vagy szervezet adatai is használhatja. A piactér store díjat API használat és költségek erőforrás-specifikus nézetét jeleníti meg.
-   **Árlista**: A [ár lap API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) felbontáson a megadott regisztrációs és számlázási időszak a alkalmazni sebesség biztosít. A forgalmi információk manuálisan a várt számla kiszámításának használati adatait és a piactér használati adatok együtt is használható.
-   **Számlázási időszak**: használja a [elszámolási időszakok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) az elszámolási időszakok és a egy tulajdonság az API-útvonal Enterprise API-adatokat, amelyek a projektjükhöz elszámolási időszakból - BalanceSummary, négy részhalmazához mutató listájának lekérése UsageDetails, a piactér-díjak és árlista.
-   **Az Azure foglalás javaslatok**: A [fenntartott példány ajánlatok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) megvizsgálja az ügyfél 7 nap, 30 napig, vagy a virtuálisgép-használati elfogadása után 60 napon, és egyetlen és a megosztott vásárlási javaslatok kínál. A fenntartott példány API lehetővé teszi, hogy az ügyfelek számára, hogy elemzése várt költséget takaríthat meg, és beszerzési összegek ajánlott.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Mi a különbség az Enterprise Reporting API-k és a felhasználás API-kat? Mikor érdemes használni az egyes?
Ezekkel az API-hasonló funkciókat rendelkeznek, és a segítségével választ kaphat a széles körű ugyanazokat a kérdések a számlázási és a cost management területen. Azonban minden API célozza meg különböző célközönségek: 

- **Enterprise Reporting API-kkal**: ezek API-k, amely hozzáférést biztosít számukra egyeztetett pénzügyi kötelezettségvállalások és az egyéni díjszabás a Microsoft nagyvállalati szerződések elvégző ügyfelek számára érhető el. Az API-k használatával szerezhető kulcs használata szükséges a [Enterprise Portal](https://ea.azure.com). A következő API-k ismertetését lásd: [áttekintése a Reporting API-kkal a vállalati ügyfelek](billing-enterprise-api.md).

- **API-k fogyasztás**: ezek API-k néhány kivételtől eltekintve minden ügyfelünk számára elérhető. További információkért lásd: [Azure-használati API – áttekintés](billing-consumption-api-overview.md) és a [Azure Consumption API-referencia](https://docs.microsoft.com/rest/api/consumption/). A megadott API-k az ajánlott megoldás a legújabb fejlesztési célokra. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Mi a különbség a használati részletek API és a Usage API-t?
Ezen API-k alapvetően más adatokat adja meg:

- **Használat részleteiről**: A [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails) biztosít Azure mérőszám példányonként használati és információkat. A megadott adatok már átadni a mérési rendszer az Azure-költségek és volna lehetséges módosít együtt alkalmazva költség:

    - Az előre fizetett pénzügyi kötelezettségvállalással használatáért fiók módosításai
    - Használati fenntartásában derítette fel az Azure-fiók módosításai

- **Használati**: A [használati API](https://msdn.microsoft.com/library/Mt219003.aspx) biztosít a nyers Azure használati adatokat, mielőtt Azure-on keresztül továbbítja a költségek mérési rendszert. Ezeket az adatokat nem lehet bármely korrelációs a használati és/vagy díj összeget, amely után az Azure díjat a mérési rendszer látható.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Mi a különbség a számlák API-t és a használati részletek API-t?
Ezekkel az API-adja meg az adatokat egy másik nézetet. A [számla API](https://docs.microsoft.com/rest/api/billing/invoices) csak a Web Direct ügyfelei számára, és egy havi összesítő nyújt be, a számlán minden fogyasztásmérő típusa összesített díjai alapján. A [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails) a használat és költségek rekordok részletes áttekintést nyújt a minden nap, és a vállalati, és Web Direct ügyfelei által használható.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Mi a különbség az ár árlista API-t és a RateCard API-t?
Ezen API-k biztosítanak a hasonló adatkészletek, de különböző célközönségek rendelkezik. Az alábbi információkat.

- Árlista API ár: A [ár lap API](https://docs.microsoft.com/rest/api/consumption/pricesheet) biztosít az egyéni díjszabás, amely egy nagyvállalati ügyfél tárgyaltak.
- RateCard API: A [RateCard API](https://msdn.microsoft.com/library/mt219005.aspx) biztosít a Web Direct ügyfelei számára, hogy a nyilvános vonatkozik.

## <a name="next-steps"></a>További lépések

- További információ az Azure API-k használatával programozott módon juthat az Azure-használat: [Azure Consumption API – áttekintés](billing-consumption-api-overview.md) és [Azure Billing API – áttekintés](billing-usage-rate-card-overview.md).
- Hasonlítsa össze a számla a részletes napi használatot részletező fájl és a költségkezelési jelentések az Azure Portalon, tekintse meg [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md)
- Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
