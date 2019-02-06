---
title: Automation-forgatókönyvek az Azure-elszámolással és költségkezeléssel |} A Microsoft Docs
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
ms.openlocfilehash: a20ec22bee17bd73df24a6a0653e458241f90cfc
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746438"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Számlázási és a cost Management Automation-forgatókönyvek

Ez a cikk gyakori helyzetek az Azure-elszámolással és költségkezeléssel sorolja fel. Ezekben az esetekben használható API-k vannak leképezve. Minden egyes forgatókönyv-API leképezési alatt az API-k és a szolgáltatásokat, és az eddigieknél is megtalálhatja.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Felügyeleti API-k számos forgatókönyv kapcsolatos költségek és a használati kapcsolatos kérdések megválaszolása céljából a költségek és a számlázás használhatja. A következő gyakori helyzetek röviden ismerteti:

- **Egyeztetés a számlához**: Adott Microsoft díja szerint számítjuk fel velem a megfelelő összeget?  Mi az a számlán, és szeretnék kiszámíthatja, saját magam?

- **Kereszt-díjak**: Most, hogy ellenőrizhető, hogy mennyit vagyok éppen fizetnem, mely munkatársaim kell fizetnem kell?

- **Költségek optimalizálása**: Tudom, hogy mennyivel korábban lett számítanak fel díjat. Hogyan kaphatok további kívül az Azure-beli töltök pénzt?

- **Költségek nyomon követése**: Tekintse meg, mennyit vagyok kiadások és az Azure idővel szeretnék. Mik azok a trendek? Hogyan tehetem jobban?

- **Azure-költségkeretről a hónap során**: IP-címek fenntartási tölti dátum az aktuális hónap? Szükséges e bármilyen módosítás a költségkeret-beállítási és/vagy használat az Azure? Ha a hónap során vagyok I használó Azure a legtöbbet?

- **Riasztások**: Hogyan állítható be a használatalapú erőforrás-alapú vagy pénzügyi alapuló riasztások?

## <a name="scenario-to-api-mapping"></a>A forgatókönyv-API-hozzárendelése

|         API        | Számla egyeztetés    | Kereszt-díjak    | Költségek optimalizálása    | Költségek nyomon követése    | Midmonth kiadások    | Riasztások    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Költségvetések                     |                           |                  |           X          |                  |                    |     X     |
| A Marketplace díjai                |             X             |         X        |           X          |         X        |          X         |     X     |
| Árlista                 |             X             |         X        |           X          |         X        |          X         |           |
| Foglalási javaslatok |                           |                  |           X          |                  |                    |           |
| Foglalási adatok         |                           |                  |           X          |         X        |                    |           |
| Foglalási összegzések       |                           |                  |           X          |         X        |                    |           |
| Használati adatok               |             X             |         X        |           X          |         X        |          X         |     X     |
| Számlázási időszak             |             X             |         X        |           X          |         X        |                    |           |
| Számlák                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nem értékelt használat               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> A forgatókönyv-API leképezés nem tartalmazza a vállalati használati API-kat. Ahol lehetséges, használja az általános felhasználás API-k új fejlesztési célokra.

## <a name="api-summaries"></a>API összefoglaló

### <a name="consumption"></a>Használat
Web Direct- és nagyvállalati ügyfelek használhatják az összes a következő API-kat, nincs másként jelölve:

-   [API költségvetése](https://docs.microsoft.com/rest/api/consumption/budgets) (*csak vállalati ügyfelek*): Erőforrások, erőforráscsoportok vagy számlázási mérőszámok költség- és használati költségvetéseket létrehozni. Ha költségvetése létrehozott, konfigurálhatja úgy a riasztásokat, amelyek figyelmeztetik, ha meghaladta meghatározott költségvetés küszöbértékeket. Beállíthatja úgy is fordulhat elő, ha elérte a költségvetési összegek műveleteket.

-   [Piactér-díjak API](https://docs.microsoft.com/rest/api/consumption/marketplaces): Bevezetés a költség- és használati adatokat minden Azure Marketplace-erőforrások (Azure-partneri ajánlatait). Ezek az adatok között minden Marketplace-erőforrások költségeit összeadódhatnak vagy vizsgálja meg az adott erőforrásokra költségek/használati használható.

-   [Ár lap API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*csak vállalati ügyfelek*): Első egyéni díjszabás minden mérőszámok. Vállalatok segítségével ezeket az adatokat a használat részleteiről és a piactér használati adatokat kombinálva költségek kiszámítása a használati és a piactér használatával. 

-   [Foglalás javaslatok API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Fenntartott VM-példányok megvásárlásával kapcsolatos javaslatokat kaphat. Javaslatok segítenek a várt költségmegtakarítást elemzése, és vásárolja meg összegek. További információkért lásd: [API-k a foglalást az Azure automation](billing-reservation-apis.md).

-   [Foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Korábban megvásárolt Virtuálisgép-foglalásokat, mennyi használatalapú van fenntartva, és mennyi szolgál például információinak megtekintéséhez. Egy Virtuálisgép-szintű részletes adatait tekintheti meg. További információkért lásd: [API-k a foglalást az Azure automation](billing-reservation-apis.md).

-   [Foglalás összegzéseket API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Tekintse meg, hogy a szervezete vásárolt, például mennyi felhasználási és mennyit használja az összesítésben foglalt VM-foglalások jelenít meg összesített adatokat. További információkért lásd: [API-k a foglalást az Azure automation](billing-reservation-apis.md).

-   [Használat részletei API](https://docs.microsoft.com/rest/api/consumption/usagedetails): Az összes Azure-erőforrások díja és használati információk beolvasása a Microsoft. Információk a rendszer jelenleg a kibocsátott egyszer mérni / nap / használati részletes rekordok formájában van. Az információk segítségével a költségek összeadódhatnak összes erőforrás között, vagy vizsgálja meg a költségek/használati az adott erőforrásokra.

-   [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx): Kedvezményekkel mérőszám veheti igénybe, ha Ön a Web Direct-ügyfél. Ezután használhatja a visszaadott információ az erőforrás-használati adatait a várt számla kiszámításának alapjául manuálisan. 

-   [Használati API nem minősített](https://msdn.microsoft.com/library/azure/mt219003.aspx): Nyers használati adatai olvashatók be, mielőtt az Azure elvégzi az összes szoftverhasználat-mérő/díjszabási.

### <a name="billing"></a>Számlázás
-   [Számlázási időszak API](https://docs.microsoft.com/rest/api/billing/billingperiods): Határozza meg, hogy egy számlázási időszak elemzése, a számla azonosítók az adott időszak együtt. A számlák API-val számla azonosítók is használhatja.

-   [A számlák API](https://docs.microsoft.com/rest/api/billing/invoices): Adresa URL Pro stahování beolvasása az adott számlához egy számlázási időszak csak PDF formátumban.

### <a name="enterprise-consumption"></a>Vállalati fogyasztás
A következő API-k csak a vállalati vannak:

-   [API összefoglaló elosztása](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Egy-egy havi kaphat összefoglaló információkat a jóváírásokat összefoglaló balances, új vásárlások, Azure Marketplace szolgáltatási díjai, módosítását és kerettúllépési díjfizetést tesz szükségessé. Ez az aktuális elszámolási időszakban vagy bármelyik az elmúlt időszakban információkat szerezhet a. Vállalatok számára az adatok segítségével manuálisan számított összefoglaló díjak összehasonlítandó. Ez az API nem biztosít erőforrás-specifikus információt, vagy egy összesített nézetet a költségek.

-   [Használat részletei API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Azure-használat (a Microsoft egyéb ajánlatai) információt kaphat az aktuális hónap, egy adott számlázási időszak és a egy egyéni dátum-idő. Vállalatok számára az adatok segítségével manuálisan ki kell számítani a számlák arányát és a használat alapján. Vállalatok szervezetek között is használható attribútum a költségek szervezeti egység/szervezeti adatokat. Az adatok a használat és költségek erőforrás-specifikus nézetét jeleníti meg.

-   [Marketplace-en Store díj API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Az aktuális hónap, egy adott elszámolási időszakban vagy egy egyéni dátum-idő lekérése hatásával kapcsolatos Azure-használat (partneri ajánlatait). Vállalatok számára az adatok segítségével manuálisan ki kell számítani a számlák arányát és a használat alapján. Vállalatok szervezetek között is használható attribútum a költségek szervezeti egység/szervezeti adatokat. Az API-t a használat és költségek erőforrás-specifikus nézetét jeleníti meg.

-   [Árlista API díjszabása](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Szerezze be a alkalmazni sebessége az egyes regisztrálási és a számlázási időszak felbontáson. Használhatja a forgalmi információk és a használat részleteiről és a piactér használati adatokat a várt számla kiszámításának alapjául manuálisan.

-   [Számlázási időszak API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Elszámolási időszakok listájának beolvasása. Az API-t is biztosít olyan tulajdonságot, amely API útvonala a négy adatkészletek Enterprise API, amelyek a projektjükhöz az elszámolási időszakban mutat: BalanceSummary, UsageDetails, piactér-díjak és árlista.

-   [Fenntartott példány javaslatok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): 7 nap, 30 napig, vagy a virtuálisgép-használati elfogadása után 60 napon tekintse meg, és a egy és a megosztott vásárlási javaslatok. Az API segítségével elemezheti a várt költségmegtakarítást és ajánlott beszerzési összeg. További információkért lásd: [API-k a foglalást az Azure automation](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Mi a különbség az Enterprise Reporting API-k és a felhasználás API-kat? Mikor érdemes használni az egyes?
Ezekkel az API-hasonló funkciókat rendelkeznek, és a segítségével választ kaphat a széles körű ugyanazokat a kérdések a számlázási és a cost management területen. De a különböző célközönségek céloznak: 

- Az ügyfelek számára, akik, amely hozzáférést biztosít számukra egyeztetett pénzügyi kötelezettségvállalások és az egyéni díjszabás a Microsoft nagyvállalati szerződések Enterprise Reporting API-kkal érhetők el. Az API-k szükséges egy kulcsot, kérhet le a [Enterprise Portal](https://ea.azure.com). A következő API-k ismertetését lásd: [áttekintése a Reporting API-kkal a vállalati ügyfelek](billing-enterprise-api.md).

- Használatalapú API-k néhány kivételtől eltekintve minden ügyfelünk számára elérhető. További információkért lásd: [Azure-használati API – áttekintés](billing-consumption-api-overview.md) és a [Azure Consumption API-referencia](https://docs.microsoft.com/rest/api/consumption/). A megadott API-k javasoljuk a legújabb fejlesztési forgatókönyvre vonatkozó megoldásként. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Mi a különbség a használati részletek API és a Usage API-t?
Ezen API-k alapvetően más adatokat adja meg:

- A [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails) biztosít Azure mérőszám példányonként használati és információkat. A megadott adatok már átadni a rendszer az Azure-ban mérési költség és volt alkalmazva mellett egyéb lehetséges változások költség:

   - Az előre fizetett pénzügyi kötelezettségvállalással használatáért fiók módosításai
   - Használati fenntartásában derítette fel az Azure-fiók módosításai

- A [használati API](https://msdn.microsoft.com/library/Mt219003.aspx) nyers Azure használati adatokat biztosít, mielőtt a rendszer az Azure-ban mérési költség keresztülhalad. Ezek az adatok esetleg nincs bármely korrelációs a használat vagy az akkumulátor lemerült összeget, amely után az Azure díjat a mérési rendszer látható.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Mi a különbség a számlák API-t és a használati részletek API-t?
Ezekkel az API-adja meg az adatokat egy másik nézet:

- A [számla API](https://docs.microsoft.com/rest/api/billing/invoices) a csak a Web Direct ügyfelei számára. A számla alapján az összesített díjak az egyes mérő havi összesített biztosít. 

- A [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails) a használat és költségek rekordok részletes áttekintést nyújt a minden nap. A vállalati, és Web Direct ügyfelei is használható.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Mi a különbség az ár árlista API-t és a RateCard API-t?
Ezen API-k biztosítanak a hasonló adatkészletek, de különböző célközönségek rendelkezik:

- A [ár lap API](https://docs.microsoft.com/rest/api/consumption/pricesheet) biztosít az egyéni díjszabás, amely a nagyvállalati ügyfelek fiókazonosítóját volt egyeztetve.

- A [RateCard API](https://msdn.microsoft.com/library/mt219005.aspx) biztosít a Web Direct ügyfelei számára, hogy nyilvános vonatkozik.

## <a name="next-steps"></a>További lépések

- További információ az Azure API-k használatával programozott módon juthat az Azure-használat: [Azure Consumption API – áttekintés](billing-consumption-api-overview.md) és [Azure Billing API – áttekintés](billing-usage-rate-card-overview.md).

- Hasonlítsa össze a számla a részletes napi használatot részletező fájl és a költségkezelési jelentések az Azure Portalon, tekintse meg [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md).

- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
