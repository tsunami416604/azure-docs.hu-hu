---
title: Automatizálási forgatókönyvek az Azure-számlázáshoz és a Cost Managementhez | Microsoft Docs
description: Ismerje meg, hogy az általános számlázási és költséghatékonysági forgatókönyvek hogyan vannak leképezve a különböző API-kra.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 3d06df9b9a90f26b39afc17b8fcd02c85da567a1
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443258"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatizálási forgatókönyvek a számlázáshoz és a költséghatékonysághoz

Ez a cikk az Azure-számlázással és a költséghatékonysággal kapcsolatos gyakori forgatókönyveket sorolja fel. Ezeket a forgatókönyveket a használható API-kkal társítja. Az egyes forgatókönyvek és API-k közötti megfeleltetések esetében az API-k és az általuk kínált funkciók összegzését találhatja meg.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A számlázási és a Cost Management API-kat számos forgatókönyvben használhatja a költséghatékony és a használattal kapcsolatos kérdések megválaszolásához. Íme egy vázlat a gyakori forgatókönyvekről:

- **Számla egyeztetése**: A Microsoft felszámolta a megfelelő összeget?  Mi a számla, és tudom kiszámítani magam?

- **Több költség**: Most, hogy tudom, mennyit kell fizetnem, ki kell fizetni a szervezetem számára?

- A **Cost optimalizálása**: Tudom, hogy mennyi díjat számoltak fel. Hogyan tudhatok meg többet az Azure-ban betöltött pénzről?

- **Cost Tracking**: Szeretném megtekinteni, hogy mennyit töltöttem és használom az Azure-t az idő múlásával. Mik azok a trendek? Hogyan tehetek jobbá?

- **Azure-kiadások a hónap során**: Mennyibe kerül az aktuális havi kiadásom? Kell-e módosítani a saját kiadások és/vagy az Azure használatát? Ha a hónapban használom az Azure-t?

- **Riasztások**: Hogyan állíthatok be erőforrás-alapú vagy pénzügyi alapú riasztásokat?

## <a name="scenario-to-api-mapping"></a>Forgatókönyv – API megfeleltetés

|         API        | Számla egyeztetése    | Több költség    | Költségoptimalizálás    | Cost Tracking    | Midmonth-kiadások    | Riasztások    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Költségvetések                     |                           |                  |           X          |                  |                    |     X     |
| A Marketplace díjai                |             X             |         X        |           X          |         X        |          X         |     X     |
| Árlista                 |             X             |         X        |           X          |         X        |          X         |           |
| Foglalási javaslatok |                           |                  |           X          |                  |                    |           |
| Foglalási adatok         |                           |                  |           X          |         X        |                    |           |
| Foglalási összegzések       |                           |                  |           X          |         X        |                    |           |
| Használat részletei               |             X             |         X        |           X          |         X        |          X         |     X     |
| Számlázási időszakok             |             X             |         X        |           X          |         X        |                    |           |
| Számlák                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nem értékelt használat               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> A forgatókönyv – API megfeleltetés nem tartalmazza a vállalati fogyasztási API-kat. Ha lehetséges, használja az általános felhasználási API-kat az új fejlesztési forgatókönyvekhez.

## <a name="api-summaries"></a>API-összefoglalók

### <a name="consumption"></a>Használat
A web Direct és a nagyvállalati ügyfelek a következő API-kat használhatják, kivéve a következőket:

-   [Költségvetési API](https://docs.microsoft.com/rest/api/consumption/budgets) (*Csak vállalati ügyfelek*): Erőforrások, erőforráscsoportok vagy számlázási mérőszámok költség-vagy használati költségvetésének létrehozása. A költségvetések létrehozásakor riasztásokat állíthat be, hogy értesítést kapjon, ha túllépte a meghatározott költségvetési küszöbértékeket. Emellett olyan műveleteket is konfigurálhat, amelyek akkor lépnek fel, ha elérte a költségvetési összegeket.

-   [Marketplace-díjak API](https://docs.microsoft.com/rest/api/consumption/marketplaces): Az Azure Marketplace összes erőforrására (Azure-partneri ajánlatokra) vonatkozó díj-és használati adatok beszerzése. Ezeket az adatokat felhasználhatja az összes Piactéri erőforrásban felmerülő költségek hozzáadására, illetve az adott erőforrásokkal kapcsolatos költségek/használat kivizsgálására.

-   [Árlista API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Csak vállalati ügyfelek*): Az összes fogyasztásmérőre vonatkozó egyéni díjszabás beszerzése. A vállalatok a használati adatokkal és a piactér használati adataival együtt használhatják ezeket az adatokat a költségek kiszámításához a használati és a Piactéri adatok használatával. 

-   [Foglalási Recommendations API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Javaslatok a fenntartott VM-példányok vásárlásához. A javaslatok segítenek elemezni a várható költségmegtakarításokat és a vásárlási összegeket. További információ: API-k [Az Azure foglalás automatizálásához](billing-reservation-apis.md).

-   [Foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Tekintse meg a korábban megvásárolt virtuális gépek foglalásával kapcsolatos információkat, például a lefoglalt felhasználás mértékét, illetve a felhasznált mennyiség mértékét. A virtuális gép szintjén részletes adatokat tekinthet meg. További információ: API-k [Az Azure foglalás automatizálásához](billing-reservation-apis.md).

-   [Foglalási összefoglalók API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Tekintse meg a szervezet által vásárolt virtuális gépek foglalásának összesített információit, például a lefoglalt felhasználás mértékét, illetve az összesítésben használt mennyiség mértékét. További információ: API-k [Az Azure foglalás automatizálásához](billing-reservation-apis.md).

-   [Használati adatok API](https://docs.microsoft.com/rest/api/consumption/usagedetails): A Microsoft összes Azure-erőforrására vonatkozó díjszabási és használati adatok beszerzése. Az információk a használati adatokra vonatkozó rekordok formájában jelennek meg, amelyek napi óránként egyszer lettek kibocsátva. Az információ segítségével felveheti a költségeket az összes erőforrásra, vagy megvizsgálhatja az adott erőforrásokkal kapcsolatos költségeket/használatot.

-   [RATECARD API](/previous-versions/azure/reference/mt219005(v=azure.100)): A mérőszámok díjszabása, ha Ön webes közvetlen ügyfél. Ezután a visszaadott adatokat az erőforrás-használati adatokkal együtt használva manuálisan kiszámíthatja a várt számlát. 

-   Nem [értékelt használati API](/previous-versions/azure/reference/mt219003(v=azure.100)): Nyers használati információk beolvasása az Azure-ban bármilyen mérés/töltés után.

### <a name="billing"></a>Számlázás
-   [Számlázási időszakok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Határozza meg az elemezni kívánt számlázási időszakot, valamint az adott időszakhoz tartozó számlák azonosítóit. A számla-azonosítókat a számlák API-val használhatja.

-   [Számlák API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): Lekérheti a számlázási időszakra vonatkozó, a PDF-űrlapon elszámolt számla letöltési URL-címét.

### <a name="enterprise-consumption"></a>Nagyvállalati felhasználás
A következő API-k csak a vállalati verzióra vonatkoznak:

-   [Balance Summary API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Havi összefoglaló információk az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a túlhasználati költségekről. Ezt az információt az aktuális számlázási időszakra vagy a múltbeli időszakra vonatkozóan szerezheti be. A vállalatok ezekkel az adatokkal összehasonlíthatja a manuálisan számított összefoglaló díjakat. Ez az API nem biztosít erőforrás-specifikus adatokat vagy összesítő nézetet a költségekről.

-   [Használati adatok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Információk az Azure-használatról (Microsoft-ajánlatokról) az aktuális hónapra, egy adott számlázási időszakra vagy egy egyéni dátumra vonatkozóan. A vállalatok ezeket az adatmennyiségeket használhatják a számlák manuális kiszámításához a díjszabás és a felhasználás alapján. A vállalatok a részleg/szervezet adatait is használhatják a különböző szervezetek költségeinek kihasználása érdekében. Az adatok a használat/Cost erőforrás-specifikus nézetét biztosítják.

-   [Marketplace Store Charge API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Információ az Azure-használatról (a partneri ajánlatokról) az aktuális hónapra, egy adott számlázási időszakra vagy egy egyéni dátumra vonatkozóan. A vállalatok ezeket az adatmennyiségeket használhatják a számlák manuális kiszámításához a díjszabás és a felhasználás alapján. A vállalatok a részleg/szervezet adatait is használhatják a különböző szervezetek költségeinek kihasználása érdekében. Ez az API a használat/Cost erőforrás-specifikus nézetét biztosítja.

-   [Árlista API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): A megadott regisztrációs és számlázási időszakra vonatkozó egyes mérőszámok esetén érvényes díjszabást kaphat. Ezt az arányt használhatja a használati adatokkal és a piactér használati adataival együtt a várt számla manuális kiszámításához.

-   [Számlázási időszakok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): A számlázási időszakok listájának beolvasása. Az API egy olyan tulajdonságot is biztosít, amely a számlázási időszakra vonatkozó négy vállalati API-adat API-útvonalára mutat: BalanceSummary, UsageDetails, Marketplace-díjak és árlista.

-   [Fenntartott példány Recommendations API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Tekintse meg a 7 nap, 30 nap vagy 60 nap a virtuálisgép-használatot, és szerezzen be egyetlen és közös vásárlási javaslatokat. Ezzel az API-val elemezheti a várható költségmegtakarításokat és az ajánlott vásárlási összegeket. További információ: API-k [Az Azure foglalás automatizálásához](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Mi a különbség a vállalati jelentési API-k és a felhasználási API-k között? Mikor érdemes használni?
Ezek az API-k hasonló funkciókkal rendelkeznek, és a számlázással és a költséghatékonysággal foglalkozó területtel azonos széles körű kérdésekre tudnak válaszolni. De különböző célközönségeket céloznak meg: 

- A vállalati jelentéskészítési API-k olyan ügyfelek számára érhetők el, akik aláírtak egy Nagyvállalati Szerződés a Microsofttal, amely hozzáférést biztosít számukra az egyeztetett pénzügyi kötelezettségvállalásokhoz és az egyéni díjszabáshoz. Az API-k olyan kulcsot igényelnek, amelyet a [Enterprise Portal](https://ea.azure.com)szerezhet be. Ezen API-k leírását lásd: [a vállalati ügyfelek jelentéskészítési API-jai áttekintése](billing-enterprise-api.md).

- A felhasználási API-k minden ügyfél számára elérhetők, néhány kivétellel. További információ: az [Azure használati API áttekintése](billing-consumption-api-overview.md) és az [Azure használati API](https://docs.microsoft.com/rest/api/consumption/)-referenciája. Javasoljuk, hogy a legújabb fejlesztői forgatókönyvek megoldásához a megadott API-kat ajánljuk. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Mi a különbség a használati adatok API és a használati API között?
Ezek az API-k alapvetően eltérő adattárolást biztosítanak:

- A [használati adatok API](https://docs.microsoft.com/rest/api/consumption/usagedetails) az Azure használati és díjszabási adatait tartalmazza. A megadott adatmennyiség már áthaladt az Azure-beli Cost-mérési rendszeren, és a többi lehetséges módosítással együtt felmerült a díjszabása:

   - Az előre fizetett pénzügyi kötelezettségvállalások használatára vonatkozó változások
   - Az Azure által felderített használati eltérések fiókjának változásai

- A [használati API](/previous-versions/azure/reference/mt219003(v=azure.100)) a nyers Azure-használati adatokat nyújtja, mielőtt áthalad az Azure-beli Cost-mérési rendszeren. Előfordulhat, hogy az adatok nem rendelkeznek az Azure-díjszabási rendszer után észlelt használati vagy díjszabási mennyiséggel.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Mi a különbség a számla API és a használati adatok API között?
Ezek az API-k különböző nézeteket biztosítanak ugyanazokból az értékekről:

- A [számla API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) csak a web Direct-ügyfelek számára érhető el. A számla havi összesítését biztosítja az egyes mérési típusok összesített díjai alapján. 

- A [használati adatok API](https://docs.microsoft.com/rest/api/consumption/usagedetails) részletesen áttekinti a használati/Cost-rekordokat az egyes napokon. Mind a vállalati, mind a web Direct-ügyfél használhatja azt.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Mi a különbség az árlista API és a RateCard API között?
Ezek az API-k hasonló adatkészleteket biztosítanak, de különböző célközönségekkel rendelkeznek:

- Az [árlista API](https://docs.microsoft.com/rest/api/consumption/pricesheet) biztosítja a vállalati ügyfelek számára egyeztetett egyéni díjszabást.

- A [RATECARD API](/previous-versions/azure/reference/mt219005(v=azure.100)) biztosítja a nyilvános díjszabást, amely a webes közvetlen ügyfelekre vonatkozik.

## <a name="next-steps"></a>További lépések

- További információ az Azure API-k használatáról az Azure-beli használatra való programozott betekintéshez: az [Azure használati API áttekintése](billing-consumption-api-overview.md) és az [Azure számlázási API áttekintése](billing-usage-rate-card-overview.md).

- Ha össze szeretné hasonlítani a számlát a részletes napi használati fájllal és a Azure Portalban található Cost Management-jelentésekkel, tekintse meg a számla [értelmezését Microsoft Azure](billing-understand-your-bill.md).

- Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
