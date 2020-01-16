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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992815"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatizálási forgatókönyvek a számlázáshoz és költségkezeléshez

Ez a cikk az Azure-számlázás és költségkezelés gyakori forgatókönyveit tartalmazza. Ezeket a forgatókönyveket az Ön által használható API-kra képezi le. Az egyes forgatókönyvek API-kra való leképezéseiben megtalálja az API-k és az általuk kínált funkciók összegzését.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A számlázási és költségkezelési API-kat számos forgatókönyvben használhatja a költségekkel és a használattal kapcsolatos kérdések megválaszolásához. Itt találja a gyakori forgatókönyvek vázlatát:

- **Számla-egyeztetés**: a Microsoft a megfelelő összeget terhelte?  Mennyi a számlám, és ki tudom számítani magamnak?

- **Több költség**: most már tudom, hogy mennyit számolok fel, kik a szervezetem?

- **Költség-optimalizálás**: tudom, mennyit számoltak fel. Hogyan használhatom ki jobban az Azure-ra költött összeget?

- **Költségek követése**: szeretném megtekinteni, hogy mennyit töltöttem és használom az Azure-t az idő múlásával. Mik a trendek? Hogyan használhatom jobban?

- **Azure-kiadások a hónap során**: Mennyibe kerül az aktuális hónap kiadása? Változtatnom kell azon, mennyit költök az Azure-ra és/vagy hogyan használom? A hónap során mikor használom a legtöbbet az Azure-t?

- **Riasztások**: Hogyan állíthatok be erőforrás-alapú vagy pénzügyi alapú riasztásokat?

## <a name="scenario-to-api-mapping"></a>Forgatókönyv–API leképezés

|         API        | Számla egyeztetése    | Keresztköltségek    | Költségoptimalizálás    | Költségkövetés    | Hónapközepi kiadások    | Értesítések    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Költségkeret                     |                           |                  |           X          |                  |                    |     X     |
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

### <a name="consumption"></a>Használatalapú
A Web Direct- és a nagyvállalati ügyfelek a következő API-kat használhatják, kivéve, ahol máshogy van jelezve:

-   [Költségkeretek API](https://docs.microsoft.com/rest/api/consumption/budgets) (*csak vállalati ügyfelek*számára): hozzon létre egy költség-vagy használati költségvetést az erőforrásokhoz, az erőforrás-csoportokhoz vagy a számlázási fogyasztásmérőhöz. Ha létrehozta a költségvetéseket, riasztásokat konfigurálhat, amelyek értesítést küldenek, ha túllépte a költségvetés meghatározott küszöbeit. Műveleteket is konfigurálhat arra az esetre, ha túllépte a költségvetés összegeit.

-   [Piactéri díjak API](https://docs.microsoft.com/rest/api/consumption/marketplaces): az Azure Marketplace összes erőforrásának (Azure-partneri ajánlatok) díjszabási és használati adatait is lekérheti. Ezekkel az adatokkal összeadhatja az összes piactéri erőforrás költségét, vagy megvizsgálhatja az adott erőforrások költségeit/használati adatait.

-   [Árlista API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*csak vállalati ügyfelek*számára): egyéni díjszabás az összes mérőszámhoz. A nagyvállalatok ezekkel az adatokkal, valamint a használati adatokkal és a piactér használati adataival kiszámíthatják a költségeket.

-   [Foglalás Recommendations API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): javaslatokat kaphat a FENNTARTott VM-példányok megvásárlására. A javaslatokkal könnyebben elemezheti a várható költségmegtakarításokat és a vásárlási összegeket. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

-   [Foglalási részletek API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): a korábban MEGvásárolt virtuális gépek foglalásával kapcsolatos információk, például a lefoglalt felhasználások és a felhasznált adatok mennyiségének megtekintése. Az adatokat virtuális gépenkénti részletességgel tekintheti meg. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

-   [Foglalási összefoglalók API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Tekintse meg a szervezet által vásárolt virtuális gépek foglalásával kapcsolatos összesített információkat, például a lefoglalt felhasználás mértékét, illetve az összesítésben használt mennyiség mértékét. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

-   [Használati adatok API](https://docs.microsoft.com/rest/api/consumption/usagedetails): a Microsoft összes Azure-erőforrására vonatkozó díjszabási és használati adatok. Az információk használati adatokra vonatkozó rekordok formájában jelennek meg, amelyek jelenleg naponta mérőnként egyszer vannak kibocsátva. Az adatokkal összeadhatja az összes erőforrás költségét, vagy megvizsgálhatja az adott erőforrások költségeit/használati adatait.

-   [RATECARD API](/previous-versions/azure/reference/mt219005(v=azure.100)): lekérési díjak, ha Ön webes közvetlen ügyfél. Ezután a visszaadott adatokat az erőforrás-használati adatokkal együtt használva manuálisan számíthatja ki a várt számlát.

-   Nem [értékelt használati API](/previous-versions/azure/reference/mt219003(v=azure.100)): nyers használati információk beolvasása az Azure-ban bármilyen mérés/töltés után.

### <a name="billing"></a>Számlázás
-   [Számlázási időszakok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): határozza meg az elemezni kívánt számlázási időszakot, valamint az adott időszakhoz tartozó számlák azonosítóit. Az Invoices API-val számlaazonosítókat használhat.

-   [Számlák API](/rest/api/billing/2019-10-01-preview/invoices): lekérheti a számlázási időszakra vonatkozó letöltési URL-címet PDF formátumban.

### <a name="enterprise-consumption"></a>Nagyvállalati használat
A következő API-k csak nagyvállalatok számára érhetők el:

-   [Balance Summary API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): havi összegzést kaphat a egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a beállításokról és a túlterhelésről. Ezeket az adatokat az aktuális számlázási időszakra vagy bármely múltbéli időszakra lekérheti. A nagyvállalatok ezeket az adatokat összehasonlíthatják a manuálisan kiszámított összesített díjakkal. Ez az API nem biztosít erőforrás-specifikus adatokat vagy a költségek összesített nézetét.

-   [Használati adatok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): az aktuális hónapra, egy adott számlázási időszakra vagy egy egyéni időszakra vonatkozó információk beszerzése az Azure-használatról (Microsoft-ajánlatokról). A nagyvállalatok ezekkel az adatokkal a díjak és a felhasználás alapján manuálisan kiszámíthatják a számlákat. A nagyvállalatok a részleg vagy szervezet információival különböző szervezetek közötti költségeket is feloszthatnak. Az adatok a használat/költségek erőforrás-specifikus nézetét biztosítják.

-   [Marketplace Store Charge API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): információ az Azure-használatról (a partneri ajánlatokról) az aktuális hónapra, egy adott számlázási időszakra vagy egy egyéni dátumra vonatkozóan. A nagyvállalatok ezekkel az adatokkal a díjak és a felhasználás alapján manuálisan kiszámíthatják a számlákat. A nagyvállalatok a részleg vagy szervezet információival különböző szervezetek közötti költségeket is feloszthatnak. Ez az API a használat/költségek erőforrás-specifikus nézetét biztosítja.

-   [Árlista API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): az adott regisztrálási és számlázási időszakra vonatkozó egyes mérőszámok esetén érvényes díjszabást kaphat. Ezekkel a díjadatokkal, valamint a használati adatokkal és a piactér használati adataival manuálisan számíthatja ki a várt számlát.

-   [Számlázási időszakok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): a számlázási időszakok listájának beolvasása. Az API egy olyan tulajdonságot is biztosít, amely az API-útvonalra mutat a következő számlázási időszakra vonatkozó négy vállalati API-adat esetében: BalanceSummary, UsageDetails, Marketplace-díjak és árlista.

-   [Fenntartott példány Recommendations API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): 7 nap, 30 nap vagy 60 nap alatt megtekintheti a virtuálisgép-használatot, és egyszeri és közös vásárlási javaslatokat kaphat. Ezzel az API-val elemezheti a várható költségmegtakarításokat és a javasolt vásárlási összegeket. További információkért lásd [az Azure-foglalás automatizálásához elérhető API-kat](../reservations/reservation-apis.md).

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

## <a name="next-steps"></a>Következő lépések

- Az Azure használatára vonatkozó programozott elemzések beszerzéséhez szükséges Azure API-k használatával kapcsolatban lásd az [Azure Consumption API áttekintését](consumption-api-overview.md) és [az Azure Billing API áttekintését](usage-rate-card-overview.md).

- Az Azure Portalon a számla és a részletes napi használati adatok fájljának, illetve a költségkezelési jelentések összehasonlításához lásd a [Microsoft Azure-elszámolások értelmezését](../understand/review-individual-bill.md).

- Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
