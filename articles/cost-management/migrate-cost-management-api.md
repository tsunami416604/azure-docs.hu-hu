---
title: Áttelepítés a nagyvállalati szerződés a Microsoft ügyfél-megállapodás API-k – Azure |} A Microsoft Docs
description: Ez a cikk segít megérteni a Microsoft nagyvállalati szerződés (EA) a Microsoft Ügyfélszerződéséhez-ba való migrálás következményeit.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e4c5607089efb247620766fb311b97cae3772770
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279873"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Áttelepítés a nagyvállalati szerződés a Microsoft ügyfél-megállapodás API-k

Ez a cikk segít az adatok szerkezetét, API és a nagyvállalati szerződés (EA) és a Microsoft ügyfél-szerződés (MCA) fiókok közötti integrációs rendszer eltérések. Az Azure Cost Management API-k két fióktípus esetében támogatja. Tekintse át a [a számlázási fiók beállítása](../billing/billing-mca-setup-account.md) Microsoft Ügyfélszerződéséhez a cikk a folytatás előtt.

Egy meglévő EA-fiókkal rendelkező szervezeteknek át kell tekintenie Ez a cikk egy MCA fiókot együtt. Korábban néhány régi regisztrációs áthelyezése egy új minimális munkahelyi megújítása a nagyvállalati szerződéssel rendelkező fiók szükséges. Azonban további erőfeszítésekre MCA fiók-ba való migrálás igényel. További erőfeszítésekre van az alapul szolgáló számlázási alrendszer költsége kapcsolódó API-k és a szolgáltatási ajánlatok érintő változások miatt.

## <a name="mca-apis-and-integration"></a>MCA API-k és integráció

MCA API-k és az új integrációs teszik lehetővé:

- Rendelkezik teljes körű API-rendelkezésre állási natív Azure API-kon keresztül.
- Adja meg egy olyan számlázási fiók több számlákat.
- Egy kombinált API-hozzáférés az Azure-szolgáltatás használata, a harmadik felek Marketplace használati és a Marketplace-en való vásárlásra.
- Különböző (azonos a regisztrációk) profilok számlázási költségek megtekintése az Azure Cost Managementtel.
- Megjeleníteni a költségeket, és értesítést kaphat, amikor a költségek előre meghatározott küszöbértékekkel meghaladja-e, és automatikusan a nyers adatok exportálása új API-k eléréséhez.

## <a name="migration-checklist"></a>Áttelepítési ellenőrzőlista

A következő elemek súgó MCA API-k a váltás.

- Ismerkedjen meg az új [számlázási fiók Microsoft Ügyfélszerződéséhez](../billing/billing-mca-overview.md).
- Határozza meg, melyik API-kat használ, és melyeket cseréje a következő szakaszban talál.
- Ismerje meg az [Azure Resource Manager REST API-k](/rest/api/azure).
- Ha már nem használja az Azure Resource Manager API-k, [ügyfél alkalmazás regisztrálása az Azure ad-vel](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Programozási kód frissítése [hitelesítés használata az Azure AD](/rest/api/azure/#create-the-request).
- EA API-hívások lecseréli MCA API-hívások programozási kód frissítése.
- Hiba történt az új hibakódok használandó kezelése frissítése.
- Tekintse át a további integrációval ajánlatok, például a Cloudyn és a Power bi-ban, más művelet szükséges.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA API-k helyére MCA API-k

EA API-k API-kulcs használata a hitelesítéshez és engedélyezéshez. MCA API-k használata az Azure AD-hitelesítés.

| Cél | EA API | MCA API |
| --- | --- | --- |
| Egyenleg és jóváírások | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Használat (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Használat (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-használat (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Számlázási időszak | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Árlista | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Foglalás beszerzések | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Foglalás javaslatok | [/ SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Foglalás-használat | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> azure-szolgáltatás és a külső piactér használati érhetők el a [használati részletek API](/rest/api/consumption/usagedetails).

A következő API-k MCA számlázási fiókok érhetők el:

| Cél | A Microsoft ügyfél-szerződés (MCA) API |
| --- | --- |
| Számlázási fiókok<sup>2</sup> | Microsoft.Billing/billingAccounts |
| A számlázás profilok<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| A számlához szakaszok<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Számlák | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Számlázási előfizetés | {scope}/billingSubscriptions |

<sup>2</sup> API-k objektumok, amelyek hatókörök, ahol Cost Management az Azure Portalon, és a működéséhez API-k listáját adja vissza. További információ a Cost Management hatókörök: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

Ha bármely meglévő EA API-kat használja, frissítenie őket MCA számlázási fiókok támogatása. Az alábbi táblázat a többi integrációs módosításokat:

| Cél | Régi ajánlat | Új ajánlat |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [A Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) content pack és összekötő | [A Microsoft Azure Consumption Insights Power BI alkalmazás](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) és [Azure Consumption Insights-összekötő](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API-k egyenlegét és a kreditek

A [egyenleg összefoglaló lekérése](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API havi összegzését biztosítja:

- Egyenlegek
- Új vásárlások
- Az Azure Marketplace szolgáltatási díjai
- Adjustments (Módosító tételek)
- A szolgáltatás kerettúllépési díjai

Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request).

Az első egyenleg összefoglalás API a Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API váltotta fel.

Egyenleg elérhető API-val elérhető egyenlegei lekérése:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API-kat a felhasználási és lekérése

Azure-szolgáltatás használati, harmadik felek piactéren használati és egyéb piactéren történő vásárlások a következő API-kkal lekérheti a költségek napi bontásban. A következő külön API-kat az Azure-szolgáltatások, harmadik felek piactéren használati összevonva. A régi API-k helyébe a [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API-t. Hozzáadja a piactéren történő vásárlások korábban csak jelentek meg az egyenleg összefoglaló dátumhoz.

- [Használat részletei/letöltési beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Használat részletei/elküldése beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Használat részletei/usagedetails beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Használat részletei/usagedetailsbycustomdate beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Díj/marketplacecharges marketplace áruházban beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Díj/marketplacechargesbycustomdate marketplace áruházban beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request).

A fenti API az Szolgáltatáshasználatot/részletek API helyébe lép.

Használat részletei API-val a használat részleteiről lekérése:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Használat részletei API, csakúgy, mint az összes a Cost Management API-k, érhető el több hatókörhöz. Számlázott költségek szerint a hagyományosan kapnia kellett egy regisztráció szintjén, a számlázási profil hatókört használja.  További információ a Cost Management hatókörök: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

| Typo | Azonosító formátuma |
| --- | --- |
| Számlázási fiók | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Számlázási profil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Előfizetés | `/subscriptions/{subscriptionId}` |
| Erőforráscsoport | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

A következő lekérdezési karakterlánc paraméterek használatával bármilyen programozási kód frissítése.

| Régi paraméterek | Új paraméterek |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nem támogatott |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

A válasz törzsében is módosítani.

Régi válasz törzsében:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Új válasz törzsében:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

A tulajdonság nevét, amely tartalmazza a használati rekordokból álló tömböt megváltozott adatok _értékek_. Minden rekord listáját részletes tulajdonságait, hogy a használt. Azonban minden egyes jegyezze fel most az összes részletet, mostantól beágyazott vlastnost s _tulajdonságok_, címkék kivételével. Más Azure API-k összhangban az új struktúráját. Néhány tulajdonság nevek változtak. Az alábbi táblázat a vonatkozó tulajdonságok.

| Régi tulajdonság | Új tulajdonság | Megjegyzések |
| --- | --- | --- |
| Fiókazonosító | – | Az előfizetés létrehozója nem követi nyomon. InvoiceSectionId (azonos departmentId) használja. |
| AccountNameAccountOwnerId és AccountOwnerEmail | – | Az előfizetés létrehozója nem követi nyomon. InvoiceSectionName (azonos departmentName) használja. |
| További információk | additionalInfo | &nbsp;  |
| Külön számlázott díjak | isAzureCreditEligible | Vegye figyelembe, hogy ezek a Tulajdonságok opposites. IsAzureCreditEnabled értéke igaz, ha ChargesBilledSeparately false (hamis) lesz. |
| Felhasznált mennyiség | MENNYISÉG | &nbsp; |
| Használt szolgáltatás | consumedService | Pontos karakterlánc-értékek eltérőek lehetnek. |
| Használt szolgáltatás azonosítója | None | &nbsp; |
| Költséghely | costCenter | &nbsp; |
| Dátum- és usageStartDate | dátum | &nbsp;  |
| Nap | None | Kezdő dátum naponta elemzi. |
| Részleg azonosítója | invoiceSectionId | Pontos értékek eltérőek. |
| Részleg neve | invoiceSectionName | Pontos karakterlánc-értékek eltérőek lehetnek. Szükség esetén konfigurálja a számla szakaszok a részlegek, megfelelően. |
| ExtendedCost és költség | costInBillingCurrency | &nbsp;  |
| Példány azonosítója | resourceId | &nbsp;  |
| Ismétlődő költség | None | &nbsp;  |
| Hely | location | &nbsp;  |
| Fogyasztásmérő kategóriája | meterCategory | Pontos karakterlánc-értékek eltérőek lehetnek. |
| Fogyasztásmérő azonosítója | meterId | Pontos karakterlánc-értékek eltérnek. |
| Fogyasztásmérő neve | meterName | Pontos karakterlánc-értékek eltérőek lehetnek. |
| Fogyasztásmérő régiója | meterRegion | Pontos karakterlánc-értékek eltérőek lehetnek. |
| Fogyasztásmérő alkategóriája | meterSubCategory | Pontos karakterlánc-értékek eltérőek lehetnek. |
| Hónap | None | Kezdő dátum a hónap elemzi. |
| Csomag neve | None | Közzétevő neve és productOrderName használni. |
| Ajánlat azonosítója | None | &nbsp;  |
| Megrendelés száma | None | &nbsp;  |
| Cikkszám | None | Használja a meterId és productOrderName árakat egyedi azonosításához. |
| Csomag neve | productOrderName | &nbsp;  |
| Product | Product |   |
| Termékazonosító | productId | Pontos karakterlánc-értékek eltérnek. |
| Közzétevő neve | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| Erőforrás GUID azonosítója | meterId | Pontos karakterlánc-értékek eltérnek. |
| Erőforrás helye | resourceLocation | &nbsp;  |
| Erőforrás helyének azonosítója | None | &nbsp;  |
| Erőforrásdíj | effectivePrice | &nbsp;  |
| Szolgáltatásadminisztrátor azonosítója | – | &nbsp;  |
| 1. szolgáltatási információ | serviceInfo1 | &nbsp;  |
| 2. szolgáltatási információ | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Pontos karakterlánc-értékek eltérőek lehetnek. |
| ServiceTier | meterSubCategory | Pontos karakterlánc-értékek eltérőek lehetnek. |
| Társzolgáltatás azonosítója | – | &nbsp;  |
| Előfizetési GUID | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Előfizetés neve | subscriptionName | &nbsp;  |
| Címkék | tags | A tags tulajdonságnak gyökérobjektum, hogy a beágyazott properties tulajdonság nem érvényes. |
| Mértékegység | unitOfMeasure | Pontos karakterlánc-értékek eltérnek. |
| usageEndDate | dátum | &nbsp;  |
| Év | None | Kezdő dátum az év elemzi. |
| (új) | billingCurrency | A díj használt pénznem. |
| (új) | billingProfileId | A számlázási profilja (megegyezik a regisztrációs) egyedi azonosítója. |
| (új) | billingProfileName | A számlázási profilja (megegyezik a regisztrációs) neve. |
| (új) | chargeType | Az Azure-szolgáltatás használata, a Marketplace-en használatának és a vásárlások megkülönböztetéséhez használja. |
| (új) | invoiceId | A számla egyedi azonosítója. Az aktuális, nyissa meg a havi üres. |
| (új) | publisherType | Írja be a közzétevő vásárlásokra. Üres használatra. |
| (új) | serviceFamily | Vásárlás típusa. Üres használatra. |
| (új) | servicePeriodEndDate | A megvásárolt szolgáltatás befejező dátumát. |
| (új) | servicePeriodStartDate | A megvásárolt szolgáltatás kezdete. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Számlázási időszak API helyébe a számlák API

MCA számlázási fiókok ne használja a számlázási időszakot. Ehelyett a számlákat hatókör a költségek az adott elszámolási időszakok használnak. A [elszámolási időszakok API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) váltotta fel a számlák API-t. Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request).

A számlák API-val számlákat lekérése:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Ár árlista API-k

Ez a szakasz ismerteti a meglévő ár árlista API-k és ajánlások a Microsoft ügyfél-megállapodások esetén a díj lap API áthelyezni. Ismerteti az ár árlista API-t a Microsoft ügyfél-megállapodások esetén és a mezők az árlistákon szereplő ismerteti. A [árlista vállalati első](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) és [vállalati első elszámolási időszakok](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API-k a Microsoft ügyfél-megállapodásokat (Microsoft.Billing/billingAccounts/billingProfiles lecserélődnek a ár lap API / árlista). Az új API támogatja mind a JSON-t, és a CSV formátumú, aszinkron REST formátumban. Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>A számlázás vállalati API-k

Vállalati belépéseket számlázási vállalati API-k együttes, ár és számlázási időszakra vonatkozó információkat. Hitelesítés és engedélyezés használja az Azure Active Directory web tokenek.

A megadott nagyvállalati beléptetés a árlista és a számlázási időszak API-kat vonatkozó árak lekéréséhez:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Ár árlista API-t a Microsoft ügyfél-megállapodások esetén

A Microsoft ügyfél-megállapodások esetén a díj lap API segítségével megtekintheti az összes Azure-szolgáltatások használati és a piactér fogyasztás szolgáltatások árai. A számlázási profil díjak vonatkoznak a profilhoz tartozó számlázási előfizetéseken.

Az ár lap API használatával az összes Azure-szolgáltatások használati szolgáltatások árlista adatok megtekintése az CSV formátum:

| Módszer | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Az ár lap API használatával az összes Azure-szolgáltatások használati services árlista-adatok megtekintése JSON formátumban:

| Módszer | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

A teljes fiók az árlista API használatával adja vissza. A PDF formátumú azonban az árlista egy rövidített változata is kaphat. Az összefoglalás Azure használat és a piactér fogyasztás szolgáltatást tartalmaz, amelyek egy adott számlához számlázzuk ki. A számla azonosíthatók a {invoiceId}, amely megegyezik a a **számlaszám** látható a számla összegzése PDF-fájlokat. Íme egy példa.

![A példában a kép a számla számát, amely megfelel a InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Az ár árlista API-val számla-információk megtekintése a CSV formátumban:

| Módszer | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Az ár árlista API-val számla-információk megtekintése a JSON formátumban:

| Módszer | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Bármely Azure-szolgáltatások használati vagy a piactér fogyasztás szolgáltatás becsült árak a jelenlegi nyílt elszámolási vagy szolgáltatás időszakban is megtekinthető.

Megtekintéséhez becsült használat árával CSV formátumban ár árlista API-val:

| Módszer | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Az ár árlista API-val fogyasztás szolgáltatások becsült árai megtekintése JSON formátumban:

| Módszer | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

A Microsoft vevői szerződés ár lap API-k *aszinkron REST API-k*. Az API-t a válaszokat a régebbi szinkron API-k megváltozott. Az API-válasz törzsében is módosítani.

#### <a name="old-response-body"></a>Régi adott válasz törzse

Íme egy példa a szinkron REST API-válasz:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Új válasz törzse

Az API-k támogatják a [Azure REST aszinkron](../azure-resource-manager/resource-manager-async-operations.md) formátumban. Az API-t használja a GET és a következő választ kap:

```
No Response Body

HTTP Status 202 Accepted
```

A következő fejlécek lesznek elküldve a kimeneti helye:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Győződjön meg arról, egy másik GET hívja a helyet. A GET-hívásra adott válasz ugyanúgy történik, amíg a művelet sikeres vagy sikertelen állapotba is. Amikor elkészült, a válasz a GET hívást helyre adresa URL Pro stahování adja vissza. Ugyanúgy, mintha a művelet egy időben lett végrehajtva. Például:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

Az ügyfél tudja végrehajtani GET hívást a `Azure-AsyncOperation`. A végpont a művelet állapotát adja vissza.

Az alábbi táblázat a régebbi vállalati első ár lap API mezőket mutatja. A Microsoft ügyfél-megállapodások esetén az új árlistán szereplő tartalmazza megfelelő mezőket:

| Régi tulajdonság | Új tulajdonság | Megjegyzések |
| --- | --- | --- |
| billingPeriodId  | _Nem alkalmazható_ | Nem alkalmazható. A Microsoft ügyfél-megállapodások esetén a számlák és a kapcsolódó árlista helyett billingPeriodId fogalma. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Pontos karakterlánc-értékek eltérőek lehetnek. |
| includedQuantity  | includedQuantity | A szolgáltatás a Microsoft ügyfél-szerződés nem alkalmazható. |
| PartNumber  | _Nem alkalmazható_ | Ehelyett használjon productOrderName (azonos offerId) és meterid kombinációja. |
| Egységár  | Egységár | Egységár a Microsoft vevői szerződés keretében igénybe vett vonatkozik. |
| Pénznemkód  | pricingCurrency | A Microsoft ügyfél-szerződések díjszabása és számlázási pénznemben az ár reprezentációinak rendelkezik. A Microsoft ügyfél-szerződés pricingCurrency felel meg a pénznemkód. |
| offerId | productOrderName | Helyett OfferId productOrderName is használhat, de nem ugyanaz, mint OfferId. Azonban productOrderName és az érték határozza meg örökölt regisztrációk meterId és Offerid kapcsolódó díjszabás a Microsoft ügyfél-szerződés. |

## <a name="consumption-price-sheet-api-operations"></a>Fogyasztási ár lap API-műveletek

A nagyvállalati szerződések, használja a fogyasztási ár lap API [első](/rest/api/consumption/pricesheet/get) és [lekérése a számlázási időszak](/rest/api/consumption/pricesheet/getbybillingperiod) subscriptionId és a egy számlázási időszak hatókör műveletek. Az API-t az Azure Resource Management-hitelesítést használ.

Az árlista lekérése egy hatókör ár árlista API-val:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Árlista információk lekéréséhez számlázási időszak az ár árlista API-val:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

A fenti API-végpontokat helyett használja a következő azokat a Microsoft ügyfél-megállapodások esetén:

**Ár lap API-t a Microsoft ügyfél-szerződések (aszinkron REST API)**

Az API-t a Microsoft ügyfél-megállapodások esetén, és további attribútumok biztosít.

**Árlista számlázási profil hatókör egy számlázási fiók**

Az API-ját a meglévő API-t. Adja meg az árlista egy számlázási profilt a számlázási fiók frissült.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Árlista egy hatókör szerint számlázási fiók

Az Azure Resource Manager-hitelesítés során az árlista kap a regisztrációs hatókörben egy számlázási fiók használatos.

Az árlista beolvasása: a regisztrációs fiók számlázási fiók:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

A Microsoft Ügyfélszerződéséhez használja a következő szakaszban az adatokat. Biztosít a Microsoft Customer megállapodásokban használt mező tulajdonságait.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Árlista számlázási profil hatókör számlázási fiók

A frissített árlista API-számlázási fiók használatával lekéri az árlista CSV formátumban. Az árlista lekérése egy MCA számlázási profil hatókörben:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Az EA regisztrációs hatókörben az API-válasz és a Tulajdonságok megegyeznek. A Tulajdonságok MCA ugyanazok a Tulajdonságok megegyeznek.

A régebbi tulajdonságokat [Azure Resource Manager ár árlista API-k](/rest/api/consumption/pricesheet) és ugyanazokat az új tulajdonságokat az alábbi táblázatban.

| Régi Azure Resource Manager ár API tulajdonság  | A Microsoft vevői szerződés ár lap API új tulajdonság   | Leírás |
| --- | --- | --- |
| Meter ID (Mérési azonosító) | _meterId_ | A mérő egyedi azonosítója. Ugyanaz, mint meterId. |
| Fogyasztásmérő neve | meterName | A mérő neve. Fogyasztásmérő jelöli az Azure-szolgáltatás üzembe helyezhető erőforrásra. |
| Fogyasztásmérő kategóriája  | szolgáltatás | A mérőszám a besorolási kategória neve. Ugyanaz, mint a szolgáltatás a Microsoft vevői szerződés árlista. Pontos karakterlánc-értékek eltérnek. |
| Fogyasztásmérő alkategóriája | meterSubCategory | A mérőszám subclassification kategória nevét. A szolgáltatás magas szintű szolgáltatásainak set differenciálás besorolása alapján. Ha például alapszintű SQL DB és a Standard SQL DB |
| Fogyasztásmérő régiója | meterRegion | &nbsp;  |
| Unit (Egység) | _Nem alkalmazható_ | A unitOfMeasure elemezhető. |
| Mértékegység | unitOfMeasure | &nbsp;  |
| Cikkszám | _Nem alkalmazható_ | PartNumber, helyett használja a productOrderName és MeterId díja a számlázási profil egyedi azonosításához. Mezők helyett a partNumber MCA számlák MCA számlán jelennek meg. |
| Egységár | Egységár | A Microsoft Ügyfélszerződéséhez egységár. |
| Pénznemkód | pricingCurrency | A Microsoft ügyfél-szerződések díjai pénznem árak és számlázás pénzneme az képviseli. Pénznemkód megegyezik a pricingCurrency a Microsoft ügyfél-szerződés. |
| Included quantity (Bennefoglalt mennyiség) | includedQuantity | A Microsoft ügyfél-szerződés szolgáltatások nem alkalmazható. A nulla értékeket tartalmazó megjelenítése. |
|  Ajánlat azonosítója  | productOrderName | Helyett OfferId productOrderName használja. Nem ugyanaz, mint OfferId, azonban a productOrderName és az érték határozza meg, a Microsoft ügyfél-szerződés díjszabása. Örökölt regisztrációk meterId és Offerid kapcsolatban. |

Az ár a Microsoft ügyfél-megállapodások esetén működnek, mint a nagyvállalati szerződések van meghatározva. A nagyvállalati beléptetés a szolgáltatások árát termék, PartNumber, mérőszámok és az ajánlat egyedi rendelkezik. A PartNumber Microsoft ügyfél-megállapodások nincs használatban.

Az Azure-szolgáltatások használati szolgáltatás ár, amely része a Microsoft Ügyfélszerződéséhez productOrderName és meterId egyediségét. A szolgáltatás mérő és az adott termék képviselik.

Egyezteti az árlista ismertetését és a használat, a használat részletei API között, használhatja a productOrderName és meterId.

Az árlista számlázási profil tulajdonos, közreműködő, olvasó, és a számla manager rights rendelkező felhasználók töltheti le.

Az árlista árával, amelynek ár használati tartalmazza. A szolgáltatások közé tartoznak az Azure-használat és a Marketplace-használat. Minden egyes szolgáltatás időszak végén a legfrissebb ár zárolva van, és egyetlen szolgáltatási ponttal használati alkalmazott. Használatalapú Azure-szolgáltatásokhoz a szolgáltatás időtartam, amely általában egy adott naptári hónapban.

### <a name="retired-price-sheet-api-fields"></a>Kivont ár lap API mezők

A következő mezőket is, vagy nem érhető el a Microsoft vevői szerződés ár lap API-k vagy ugyanazokat a mezőket.

|Kivont mező| Leírás|
|---|---|
| billingPeriodId | Nem alkalmazható. InvoiceId MCA a felel meg. |
| offerId | Nem alkalmazható. A MCA productOrderName felel meg. |
| meterCategory  | Nem alkalmazható. Szolgáltatás a MCA felel meg. |
| egység | Nem alkalmazható. A unitOfMeasure elemezhető. |
| Pénznemkód | Ugyanaz, mint a pricingCurrency MCA a. |
| meterLocation | Ugyanaz, mint a meterRegion MCA a. |
| partNumber partnumber | Nem használható, mert cikkszám nem szerepel a MCA számlákat. Partnumber, helyett használja a meterId és productOrderName kombinációja árakat egyedi azonosításához. |
| totalIncludedQuantity | Nem alkalmazható. |
| pretaxStandardRate  | Nem alkalmazható. |

## <a name="reservation-instance-charge-api-replaced"></a>Foglalás példány díja API cseréje

Akkor is első számlázási a Foglalás beszerzések a [fenntartott példány díja API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Az új API-t minden vásárláshoz, beleértve a külső Piactéri ajánlatok elérhetőségének tartalmazza. Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request). A fenntartott példány díja API a tranzakciók API-t váltotta fel.

Az első foglalás vásárlási tranzakció, tranzakciók API-val:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Javaslatok API-k cseréje

Fenntartott példányok vásárlási javaslatok API-k biztosítanak a virtuális gép használatát az utolsó 7, 30 és 60 napban. API-kat is biztosít a Foglalás vásárlási javaslatok. Ezek a következők:

- [A megosztott fenntartott példány javaslatok API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Egyetlen fenntartott példány javaslatok API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request). A Foglalás javaslatok API-k, előzőleg felsorolt hivatkozások váltják fel a [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API-t.

A Foglalás javaslatok foglalás ajánlatok API-val:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Foglalás használati API-k cseréje

A fenntartott példány használati API regisztrációs foglalás használati kérheti le. Ha egynél több fenntartott példány regisztrációs, mind a fenntartott példányok vásárlása az API használatához a használatát is kaphat.

Ezek a következők:

- [Fenntartott példányok használatának részletei](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [A fenntartott példányok használatának összegzése](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Összes használat API-t használó Azure ad-ben a hitelesítéshez és engedélyezéshez, natív Azure API-k helyébe lép. Azure REST API-k kapcsolatos további információkért lásd: [REST – első lépések](/rest/api/azure/#create-the-request). A Foglalás javaslatok API-k, előzőleg felsorolt hivatkozások váltják fel a [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) és [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API-k.

Foglalás részletekért a Foglalás részletei API-val:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Foglalás összesítések a Foglalás összegzéseket API-val lekérése:

| Módszer | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Helyezze át a Cloudyn cost Management

Használó szervezetek [Cloudyn](https://cloudyn.com) kell használatához [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) bármely cost management igényei szerint. A Cost Management az Azure Portalon nem bevezetési és a egy 8 órás késés érhető el. További információkért lásd: a [Cost Management dokumentációja](index.yml).

Az Azure Cost Managementbe a következőket teheti:

- Nézet költségek elleni előre meghatározott költségvetési idővel. Napi költség minták azonosítása és leállíthatja a költségkeret-beállítási rendellenességek elemzése. A költségeket címkék, az erőforráscsoport, a szolgáltatás és a hely által lebontva.
- Hozzon létre költségvetése korlátozhatjuk-használat és költségek, és értesítést kaphat, amikor fontos küszöbértékek vannak válaszadásra. A Műveletcsoportok aktiválhat egyéni eseményeket és saját igényei szerint szigorú korlátozásokat az automation beállítása.
- Az Azure Advisor javaslatainak felhasználási és optimalizálhatja. Fedezze fel a foglalások beszerzési optimalizálása, becsléseim használaton virtuális gépek és költségvetése belülre fel nem használt erőforrások törlése.
- A felhasználási és adatok exportálása CSV-fájl közzététele a tárfiókhoz napi ütemezése. Automatizálhatja a számlázási adatokat tárolhat szinkronizálva és naprakész külső rendszerekkel való integráció.

## <a name="power-bi-integration"></a>Power BI-integráció

Ha Power BI költség jelentéskészítéshez használ, meg kell váltania a következő:

- A Microsoft Azure Consumption Insights Power BI alkalmazás
- Azure Consumption Insights asztali összekötő


Az összekötő javasolt a szervezet számára, aki a legnagyobb rugalmasságot. A Power BI alkalmazás azonban a gyors beállítás is érhető el.

- Telepítse a [Microsoft Azure Consumption Insightshoz a Power BI alkalmazás](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Csatlakozás az Azure Consumption Insights-összekötővel](/power-bi/desktop-connect-azure-consumption-insights)

A régebbi Consumption Insights tartalomcsomag és összekötő működött a regisztráció szintjén. Ez szükséges legalább olvasási hozzáféréssel. Az új Consumption Insights Power BI alkalmazás és az új Azure Consumption Insights-összekötő számlázási profil felhasználók számára érhetők el. Csoportok, amelyek a költségek áttekintése vagy költségek megtekintése számlázási profilok között van szükségük a további beállításokat kell használnia a [költségelemzés](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) az Azure Portalon.

## <a name="next-steps"></a>További lépések

- Olvassa el a [Cost Management dokumentációja](index.yml) megtudhatja, hogyan monitorozását és ellenőrzését, az Azure költségeit. Vagy, ha az erőforrás-használat a Cost Management optimalizálni szeretné.
