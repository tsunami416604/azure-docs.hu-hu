---
title: Automatizálási forgatókönyvek az Azure-számlázáshoz és -költségkezeléshez | Microsoft Docs
description: Megismerheti a különböző API-kra leképezett gyakori számlázási és költségkezelési forgatókönyveket.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992815"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatizálási forgatókönyvek a számlázáshoz és költségkezeléshez

Ez a cikk az Azure-számlázás és költségkezelés gyakori forgatókönyveit tartalmazza. Ezeket a forgatókönyveket az Ön által használható API-kra képezi le. Az egyes forgatókönyvek API-kra való leképezéseiben megtalálja az API-k és az általuk kínált funkciók összegzését.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A számlázási és költségkezelési API-kat számos forgatókönyvben használhatja a költségekkel és a használattal kapcsolatos kérdések megválaszolásához. Itt találja a gyakori forgatókönyvek vázlatát:

- **Számla egyeztetése**: A Microsoft a megfelelő összeget számította fel?  Mennyi a számlám, és ki tudom számítani magamnak?

- **Keresztköltségek**: Most, hogy tudom, mennyit számítottak fel, kinek kell kifizetnie a számlát a szervezetben?

- **Költségoptimalizálás**: Tudom, hogy mennyit számítottak fel. Hogyan használhatom ki jobban az Azure-ra költött összeget?

- **Költségkövetés**: Szeretném látni, hogy az idő függvényében mennyit költök az Azure-ra, és mennyit használom. Mik a trendek? Hogyan használhatom jobban?

- **Azure-költségek a hónap során**: Eddig mennyit költöttem az aktuális hónapban? Változtatnom kell azon, mennyit költök az Azure-ra és/vagy hogyan használom? A hónap során mikor használom a legtöbbet az Azure-t?

- **Riasztások**: Hogyan állíthatok be erőforrás-alapú használatot vagy pénzügyi alapú riasztásokat?

## <a name="scenario-to-api-mapping"></a>Forgatókönyv–API leképezés

|         API        | Számla egyeztetése    | Keresztköltségek    | Költségoptimalizálás    | Költségkövetés    | Hónapközepi kiadások    | Riasztások    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Költségvetések                     |                           |                  |           X          |                  |                    |     X     |
| A Marketplace díjai                |             X             |         X        |           X          |         X        |          X         |     X     |
| Árlista                 |             X             |         X        |           X          |         X        |          X         |           |
| Foglalási javaslatok |                           |                  |           X          |                  |                    |           |
| Foglalási adatok         |                           |                  |           X          |         X        |                    |           |
| Foglalási összegzések       |                           |                  |           X          |         X        |                    |           |
| Használati adatok               |             X             |         X        |           X          |         X        |          X         |     X     |
| Billing Periods             |             X             |         X        |           X          |         X        |                    |           |
| Számlák                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Unrated Usage               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> A forgatókönyv–API leképezés nem terjed ki a nagyvállalati használati API-kra. Ha lehetséges, az új fejlesztési forgatókönyvekhez használjon általános használati API-kat.

## <a name="api-summaries"></a>API-összegzések

### <a name="consumption"></a>Használat
A Web Direct- és a nagyvállalati ügyfelek a következő API-kat használhatják, kivéve, ahol máshogy van jelezve:

-   [Budgets API](https://docs.microsoft.com/rest/api/consumption/budgets) (*csak nagyvállalati ügyfelek számára*): Költségalapú vagy használati költségvetéseket hozhat létre erőforrások, erőforráscsoportok vagy számlázási mérőszámok részére. Ha létrehozta a költségvetéseket, riasztásokat konfigurálhat, amelyek értesítést küldenek, ha túllépte a költségvetés meghatározott küszöbeit. Műveleteket is konfigurálhat arra az esetre, ha túllépte a költségvetés összegeit.

-   [Marketplace Charges API](https://docs.microsoft.com/rest/api/consumption/marketplaces): Lekérheti az Azure Marketplace összes erőforrására (Azure-partnerajánlataira) vonatkozó díj- és használati adatokat. Ezekkel az adatokkal összeadhatja az összes piactéri erőforrás költségét, vagy megvizsgálhatja az adott erőforrások költségeit/használati adatait.

-   [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*csak nagyvállalati ügyfelek számára*): Az összes mérőhöz egyéni díjszabást állíthat be. A nagyvállalatok ezekkel az adatokkal, valamint a használati adatokkal és a piactér használati adataival kiszámíthatják a költségeket.

-   [Reservation Recommendations API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Javaslatokat kaphat a Reserved VM Instances megvásárlásához. A javaslatokkal könnyebben elemezheti a várható költségmegtakarításokat és a vásárlási összegeket. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

-   [Reservation Details API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Megtekintheti a korábban megvásárolt virtuális gépek foglalásával kapcsolatos információkat, például a lefoglalt felhasználás mértékét, illetve a felhasznált mennyiséget. Az adatokat virtuális gépenkénti részletességgel tekintheti meg. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

-   [Reservation Summaries API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Megtekintheti a szervezet által megvásárolt virtuális gépek foglalásával kapcsolatos összesített információkat, például hogy mennyi használat van lefoglalva, és mennyi van használatban az összesítésben. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

-   [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usagedetails): Lekérheti a Microsofttól származó összes Azure-erőforrás díj- és használati adatait. Az információk használati adatokra vonatkozó rekordok formájában jelennek meg, amelyek jelenleg naponta mérőnként egyszer vannak kibocsátva. Az adatokkal összeadhatja az összes erőforrás költségét, vagy megvizsgálhatja az adott erőforrások költségeit/használati adatait.

-   [RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)): Web Direct-ügyfélként lekérheti a mérési díjakat. Ezután a visszaadott adatokat az erőforrás-használati adatokkal együtt használva manuálisan számíthatja ki a várt számlát.

-   [Unrated Usage API](/previous-versions/azure/reference/mt219003(v=azure.100)): Lekérheti a nyers használati adatokat, mielőtt az Azure bármilyen mérést/díjazást végezne.

### <a name="billing"></a>Számlázás
-   [Billing Periods API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Meghatározhatja az elemezni kívánt számlázási időszakot, valamint az időszak számlaazonosítóját. Az Invoices API-val számlaazonosítókat használhat.

-   [Invoices API](/rest/api/billing/2019-10-01-preview/invoices): PDF formátumban kérheti le a számlázási időszakok számláinak letöltési URL-címét.

### <a name="enterprise-consumption"></a>Nagyvállalati használat
A következő API-k csak nagyvállalatok számára érhetők el:

-   [Balance Summary API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Havi összefoglaló információkat kérhet le az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről. Ezeket az adatokat az aktuális számlázási időszakra vagy bármely múltbéli időszakra lekérheti. A nagyvállalatok ezeket az adatokat összehasonlíthatják a manuálisan kiszámított összesített díjakkal. Ez az API nem biztosít erőforrás-specifikus adatokat vagy a költségek összesített nézetét.

-   [Usage Details API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Lekérheti a Microsoft-ajánlatok Azure-használatának adatait az aktuális hónapra, egy adott számlázási időszakra vagy egy egyéni időszakra vonatkozóan. A nagyvállalatok ezekkel az adatokkal a díjak és a felhasználás alapján manuálisan kiszámíthatják a számlákat. A nagyvállalatok a részleg vagy szervezet információival különböző szervezetek közötti költségeket is feloszthatnak. Az adatok a használat/költségek erőforrás-specifikus nézetét biztosítják.

-   [Marketplace Store Charge API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Lekérheti a partnerajánlatok Azure-használatának adatait az aktuális hónapra, egy adott számlázási időszakra vagy egy egyéni időszakra vonatkozóan. A nagyvállalatok ezekkel az adatokkal a díjak és a felhasználás alapján manuálisan kiszámíthatják a számlákat. A nagyvállalatok a részleg vagy szervezet információival különböző szervezetek közötti költségeket is feloszthatnak. Ez az API a használat/költségek erőforrás-specifikus nézetét biztosítja.

-   [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): A megadott regisztrációs és számlázási időszakra vonatkozó kérheti le az egyes mérők esetében érvényes díjszabást. Ezekkel a díjadatokkal, valamint a használati adatokkal és a piactér használati adataival manuálisan számíthatja ki a várt számlát.

-   [Billing Periods API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Lekérheti a számlázási időszakok listáját. Az API egy olyan tulajdonságot is elérhetővé tesz, amely a számlázási időszakra vonatkozó nagyvállalati API-adatok négy következő halmazának API-útvonalára mutató tulajdonságot biztosít: BalanceSummary, UsageDetails, Marketplace Charges és PriceSheet.

-   [Reserved Instance Recommendations API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Megtekintheti az elmúlt 7 nap, 30 nap vagy 60 nap virtuálisgép-használati adatait, illetve önálló és közös vásárlásra vonatkozó javaslatokat kaphat. Ezzel az API-val elemezheti a várható költségmegtakarításokat és a javasolt vásárlási összegeket. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Mi a különbség a nagyvállalati jelentéskészítő API-k és a használati API-k között? Mikor érdemes ezeket használni?
Ezek az API-k hasonló funkciókkal rendelkeznek, és ugyanazokat a széles körű kérdéseket válaszolhatják meg a számlázással és a költségkezeléssel kapcsolatban. De különböző közönségeket céloznak meg:

- A nagyvállalati jelentéskészítő API-k olyan ügyfelek számára érhetők el, akik aláírtak egy Nagyvállalati Szerződést a Microsofttal, amely hozzáférést biztosít számukra az egyeztetett pénzügyi kötelezettségvállalásokhoz és az egyéni díjszabáshoz. Az API-khoz szükség van egy kulcsra, amelyet az [Enterprise Portalról](https://ea.azure.com) érhet el. Ezen API-k leírásáért lásd [a nagyvállalati ügyfeleknek készült jelentéskészítő API-k áttekintését](enterprise-api.md).

- A használati API-k minden ügyfél számára elérhetők, néhány kivétellel. További információt az [Azure-használati API-k áttekintésében](consumption-api-overview.md) és az [Azure-használati API-k referenciái](https://docs.microsoft.com/rest/api/consumption/) között talál. A megadott API-kat a legújabb fejlesztési forgatókönyvekhez ajánljuk.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Mi a különbség a Usage Details API és a Usage API között?
Ezek az API-k alapvetően eltérő adatokat biztosítanak:

- A [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usagedetails) Azure-használati és -költségadatokat nyújt mérőpéldányonként. A megadott adatok már áthaladtak az Azure-beli költségmérő rendszeren, és más lehetséges módosításokkal együtt lettek alkalmazva rájuk a költségek:

   - Az előre kifizetett pénzügyi kötelezettségek használata miatt szükséges módosítások
   - Az Azure által felfedezett használati eltérések miatt szükséges módosítások

- A [Usage API](/previous-versions/azure/reference/mt219003(v=azure.100)) nyers Azure-használati adatokat nyújt, mielőtt azok áthaladnának az Azure-beli költségmérési rendszeren. Előfordulhat, hogy ezek az adatok nem feleltethetők meg az Azure költségmérő rendszerén áthaladt használati adatokkal vagy költségmennyiségekkel.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Mi a különbség az Invoice API és a Usage Details API között?
Ezek az API-k ugyanazon adatok különböző nézeteit nyújtják:

- Az [Invoice API](/rest/api/billing/2019-10-01-preview/invoices)-t csak Web Direct-ügyfelek használhatják. A számla havi összesítését biztosítja az egyes mérőtípusok összesített díjai alapján.

- A [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usagedetails) egyes napok használati/költségrekordjainak részletes nézetét nyújtja. A nagyvállalati és a Web Direct-ügyfelek is használhatják.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Mi a különbség a Price Sheet API és a RateCard API között?
Ezek az API-k hasonló adatokat nyújtanak, de különböző közönségekkel rendelkeznek:

- A [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) a nagyvállalati ügyfelek számára egyeztetett egyéni díjszabást nyújt.

- A [RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)) a Web Direct-ügyfelekre érvényes nyilvános díjszabást nyújtja.

## <a name="next-steps"></a>További lépések

- Az Azure használatára vonatkozó programozott elemzések beszerzéséhez szükséges Azure API-k használatával kapcsolatban lásd az [Azure Consumption API áttekintését](consumption-api-overview.md) és [az Azure Billing API áttekintését](usage-rate-card-overview.md).

- Az Azure Portalon a számla és a részletes napi használati adatok fájljának, illetve a költségkezelési jelentések összehasonlításához lásd a [Microsoft Azure-elszámolások értelmezését](../understand/review-individual-bill.md).

- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
