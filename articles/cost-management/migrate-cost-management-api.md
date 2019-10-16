---
title: Migrálás Nagyvállalati Szerződésról a Microsoft Customer Agreement API-khoz – Azure | Microsoft Docs
description: Ez a cikk segít megérteni a Microsoft Nagyvállalati Szerződés (EA) Microsoft-ügyféli szerződésre való áttelepítésének következményeit.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 53c171df47dde58b264b354eea5ff1ccca9f5256
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374728"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrálás Nagyvállalati Szerződésról a Microsoft Customer Agreement API-khoz

Ez a cikk segítséget nyújt az adatstruktúra, az API és az egyéb rendszerintegrációs különbségek megismeréséhez Nagyvállalati Szerződés (EA) és a Microsoft Customer Agreement (MCA) fiókok között. A Azure Cost Management mindkét fióktípus esetében támogatja az API-kat. A folytatás előtt tekintse át a Microsoft ügyfél-szerződés beállítása című cikk [telepítési számlázási fiókját](../billing/mca-setup-account.md) .

A meglévő EA-fiókkal rendelkező szervezeteknek az MCA-fiók beállításával együtt kell áttekinteniük ezt a cikket. Korábban egy EA-fiók megújításához szükséges néhány minimális munka, amely egy régi regisztrációból egy újat helyez át. Az MCA-fiókra való Migrálás azonban további erőfeszítést igényel. A további erőfeszítés az alapul szolgáló számlázási alrendszer változásai miatt történik, ami hatással van az összes Cost-related API-ra és szolgáltatási ajánlatra.

## <a name="mca-apis-and-integration"></a>MCA API-k és integráció

Az MCA API-k és az új integráció lehetővé teszi a következőket:

- Az API-k teljes rendelkezésre állása natív Azure API-kkal.
- Több számla konfigurálása egyetlen számlázási fiókban.
- Egy kombinált API-t érhet el az Azure-szolgáltatások használatáról, a harmadik féltől származó piactér használatáról és a piactéren vásárolt szolgáltatásokról.
- Az Azure Cost Management használatával megtekintheti a számlázási profilok (ugyanazokat a regisztrációkat) közötti költségeket.
- Hozzáférés az új API-khoz a költségek megjelenítéséhez, értesítést kaphat, ha a költségek meghaladják az előre meghatározott küszöbértékeket, és automatikusan exportálják a nyers

## <a name="migration-checklist"></a>Áttelepítési ellenőrzőlista

A következő elemek segítenek az MCA API-kra való áttérésben.

- Ismerkedjen meg az új [Microsoft ügyfél-szerződés számlázási fiókjával](../billing/billing-mca-overview.md).
- Határozza meg, hogy mely API-kat használja, és hogy melyek a következő szakaszban vannak lecserélve.
- Ismerkedjen meg [Azure Resource Manager REST API](/rest/api/azure)-kkal.
- Ha még nem használ Azure Resource Manager API-kat, [regisztrálja az ügyfélalkalmazás az Azure ad](/rest/api/azure/#register-your-client-application-with-azure-ad)-ben.
- Frissítse a programozási kódokat az [Azure ad-hitelesítés használatára](/rest/api/azure/#create-the-request).
- Bármely programozási kód frissítése az EA API-hívások MCA API-hívásokkal való lecseréléséhez.
- A hibakezelés frissítése új hibakódok használatára.
- További szükséges műveletekhez tekintse át a további integrációs ajánlatokat, például a Cloudyn és a Power BI.

## <a name="ea-apis-replaced-with-mca-apis"></a>Az EA API-k az MCA API-kkal helyettesíthetők

Az EA API-k egy API-kulcsot használnak a hitelesítéshez és az engedélyezéshez. Az MCA API-k az Azure AD-hitelesítést használják.

| Rendeltetés | EA API | MCA API |
| --- | --- | --- |
| Egyenleg és kreditek | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft. Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Használat (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft. fogyasztás/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Használat (CSV) | [/UsageDetails/Download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/UsageDetails/Submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. fogyasztás/usageDetails/1. Letöltés](/rest/api/consumption/usagedetails)<sup></sup> |
| Marketplace-használat (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. fogyasztás/usageDetails/1. Letöltés](/rest/api/consumption/usagedetails)<sup></sup> |
| Számlázási időszakok | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft. Billing/billingAccounts/billingProfiles/számlák |
| Árlista | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. Billing/billingAccounts/billingProfiles/árlista/alapértelmezett/letöltési formátum = JSON|CSV Microsoft. Billing/billingAccounts/.../billingProfiles/.../számlák/... /pricesheet/default/Download formátum = JSON|CSV Microsoft. Billing/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Foglalásvásárlások | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft. Billing/billingAccounts/billingProfiles/Transactions |
| Foglalási javaslatok | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[@no__t – 2](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. fogyasztás/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Foglalás használata | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. fogyasztás/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft. fogyasztás/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> az Azure szolgáltatás és a harmadik féltől származó piactér használata a [használati adatok API](/rest/api/consumption/usagedetails)-val érhető el.

A következő API-k érhetők el az MCA számlázási fiókjaihoz:

| Rendeltetés | Microsoft Customer Agreement (MCA) API |
| --- | --- |
| Számlázási fiókok<sup>2</sup> | Microsoft. Billing/billingAccounts |
| <sup>2</sup> . számlázási profilok | Microsoft. Billing/billingAccounts/billingProfiles |
| Számla<sup>2</sup> . szakaszában | Microsoft. Billing/billingAccounts/invoiceSections |
| Számlák | Microsoft. Billing/billingAccounts/billingProfiles/számlák |
| Számlázási előfizetések | {scope}/billingSubscriptions |

<sup>2</sup> API-k olyan objektumok listáját adják vissza, amelyek hatókörök, ahol a Azure Portal és az API-k Cost Management tapasztalatai működnek. További információ a Cost Management hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

Ha a meglévő EA API-kat használja, frissítenie kell őket az MCA számlázási fiókjainak támogatásához. Az alábbi táblázat más integrációs változásokat mutat be:

| Rendeltetés | Régi ajánlat | Új ajánlat |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft-fogyasztási bepillantást](/power-bi/desktop-connect-azure-consumption-insights) tartalmazó tartalomkezelő csomag és összekötő | [Microsoft Azure Consumption Insights Power bi alkalmazás](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) -és [Azure consumption Insights-összekötő](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API-k egyenleg és kreditek beszerzéséhez

A [Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API havi összegzést biztosít:

- Egyenlegek
- Új vásárlások
- Az Azure Marketplace szolgáltatási díjai
- Adjustments (Módosító tételek)
- Szolgáltatási kereten túli díjak

Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request).

A Get Balance Summary API-t a Microsoft. Billing/billingAccounts/billingProfiles/availableBalance API váltja fel.

A rendelkezésre álló egyenleg API-val elérhető egyenlegek lekérése:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>A költségeket és a használatot szolgáló API-k

A következő API-kkal napi bontásban veheti igénybe az Azure-szolgáltatások használatát, a harmadik féltől származó piactér-használatot és az egyéb piactér-vásárlási költségeket. A következő különálló API-kat egyesítjük az Azure-szolgáltatások és a harmadik féltől származó piactér-használat tekintetében. A régi API-kat a [Microsoft. fogyasztás/usageDetails](/rest/api/consumption/usagedetails) API váltja fel. Hozzáadja a Piactéri vásárlásokat, amelyek korábban csak a egyenleg összegzése dátumig voltak láthatók.

- [Használati adatok lekérése/Letöltés](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Használat részleteinek/elküldésének lekérése](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Használat részleteinek/UsageDetails beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Használat részleteinek/usagedetailsbycustomdate beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Marketplace áruházbeli díj/marketplacecharges beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Marketplace áruházbeli díj/marketplacechargesbycustomdate beolvasása](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request).

Az összes korábbi API-t a felhasználási/használati adatok API váltja fel.

A használati adatok API-val történő beszerzése:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A használati adatok API, mint az összes Cost Management API, több hatókörben is elérhető. A számlázott költségek esetében, ahogyan azt hagyományosan a regisztrációs szinten kapja, használja a számlázási profil hatókörét.  További információ a Cost Management hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

| Type (Típus) | AZONOSÍTÓ formátuma |
| --- | --- |
| Számlázási fiók | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Számlázási profil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Előfizetés | `/subscriptions/{subscriptionId}` |
| Erőforráscsoport | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

A következő querystring paraméterek használatával frissítheti a programozási kódokat.

| Régi paraméterek | Új paraméterek |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nem támogatott |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

A válasz törzse is megváltozott.

Régi válasz törzse:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Új válasz törzse:

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

A használati rekordok tömbjét tartalmazó tulajdonságnév módosult az adatokról az _értékekre_. Minden olyan rekord, amely a részletes tulajdonságok egyszerű listáját használja. Azonban minden rekord mostantól minden részletet a _Tulajdonságok_nevű beágyazott tulajdonságban, a címkék kivételével. Az új struktúra konzisztens más Azure API-kkal. Egyes tulajdonságok nevei módosultak. A következő táblázat a megfelelő tulajdonságokat tartalmazza.

| Régi tulajdonság | Új tulajdonság | Megjegyzések |
| --- | --- | --- |
| accountId | – | Az előfizetés létrehozója nincs nyomon követve. Használja a invoiceSectionId (ugyanaz, mint a departmentId). |
| AccountNameAccountOwnerId és AccountOwnerEmail | – | Az előfizetés létrehozója nincs nyomon követve. Használja a invoiceSectionName (ugyanaz, mint a departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Vegye figyelembe, hogy ezek a tulajdonságok ellentétekkel rendelkeznek. Ha a isAzureCreditEnabled értéke igaz, a ChargesBilledSeparately hamis lenne. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | A pontos karakterlánc-értékek eltérőek lehetnek. |
| ConsumedServiceId | None | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Dátum-és usageStartDate | dátum | &nbsp;  |
| Nap | None | A nap elemzése a dátumtól kezdve. |
| DepartmentId | invoiceSectionId | A pontos értékek eltérnek. |
| DepartmentName | invoiceSectionName | A pontos karakterlánc-értékek eltérőek lehetnek. Szükség esetén adja meg a számlázási szakaszokat a részlegek egyeztetéséhez. |
| ExtendedCost és Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Ismétlődő díj | None | &nbsp;  |
| Földrajzi egység | location | &nbsp;  |
| MeterCategory | meterCategory | A pontos karakterlánc-értékek eltérőek lehetnek. |
| MeterId | meterId | A karakterláncok pontos értékei eltérnek. |
| MeterName | meterName | A pontos karakterlánc-értékek eltérőek lehetnek. |
| MeterRegion | meterRegion | A pontos karakterlánc-értékek eltérőek lehetnek. |
| MeterSubCategory | meterSubCategory | A pontos karakterlánc-értékek eltérőek lehetnek. |
| Hónap | None | A hónap elemzése az aktuális dátumtól. |
| Csomag neve | None | Használja a közzétevő neve és a productOrderName. |
| OfferId | None | &nbsp;  |
| Order Number | None | &nbsp;  |
| PartNumber | None | Az árak egyedi azonosításához használja a meterId és a productOrderName. |
| Plan Name | productOrderName | &nbsp;  |
| Termék | Termék |   |
| ProductId | productId | A karakterláncok pontos értékei eltérnek. |
| Publisher Name | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| Erőforrás GUID azonosítója | meterId | A karakterláncok pontos értékei eltérnek. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | None | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | – | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | A pontos karakterlánc-értékek eltérőek lehetnek. |
| ServiceTier | meterSubCategory | A pontos karakterlánc-értékek eltérőek lehetnek. |
| StoreServiceIdentifier | – | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Címkék | tags | A címkék tulajdonság a gyökérszintű objektumra vonatkozik, nem a beágyazott tulajdonságok tulajdonságra. |
| UnitOfMeasure | unitOfMeasure | A karakterláncok pontos értékei eltérnek. |
| usageEndDate | dátum | &nbsp;  |
| Év | None | Az év elemzésének dátuma. |
| új | billingCurrency | A díjszabáshoz használt pénznem. |
| új | billingProfileId | A számlázási profil egyedi azonosítója (megegyezik a beléptetéssel). |
| új | billingProfileName | A számlázási profil neve (megegyezik a beléptetéssel). |
| új | chargeType | Az Azure-szolgáltatások használatának, a piactér használatának és a vásárlásoknak az elkülönítésére szolgál. |
| új | invoiceId | A számla egyedi azonosítója. Üres a jelenlegi, a nyitott hónapban. |
| új | publisherType | A beszerzésekhez tartozó közzétevő típusa. Üres a használathoz. |
| új | serviceFamily | A vásárlás típusa. Üres a használathoz. |
| új | servicePeriodEndDate | A megvásárolt szolgáltatás befejező dátuma. |
| új | servicePeriodStartDate | A megvásárolt szolgáltatás kezdő dátuma. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Számlázási időszakok API lecserélve a számlák API-val

Az MCA számlázási fiókjai nem használnak számlázási időszakokat. Ehelyett a számlákon a költségek hatókörét használják meghatározott számlázási időszakokra. A [Számlázási időszakok API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) -t a számlák API váltja fel. Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request).

Számlák beszerzése a számlák API-val:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Árlista API-k

Ez a szakasz a meglévő árlista-API-kat ismerteti, és javaslatokat tartalmaz a Microsoft Customer-szerződések árlista API-jának áthelyezéséhez. Emellett ismerteti a Microsoft Customer-szerződések díjszabási API-ját, valamint az árlista mezőinek leírását. A [vállalati beszerzési árlista](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) és a [nagyvállalati számlázási időszakok](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API-k helyébe a Microsoft Customer-szerződések (Microsoft. Billing/billingAccounts/BillingProfiles/árlista) árlista-API-jának helyére kerül. Az új API a JSON-és CSV-formátumokat is támogatja aszinkron REST-formátumokban. Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Számlázási vállalati API-k

A számlázási vállalati API-k vállalati beléptetéssel lettek felhasználva, hogy árat és számlázási időszakot kapjanak. A hitelesítés és az engedélyezés Azure Active Directory webes jogkivonatok használatával.

A megadott nagyvállalati beléptetésre vonatkozó díjszabás és a számlázási időszak API-k beszerzése:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>A Microsoft Customer-szerződések árlista API-je

A Microsoft ügyfél-szerződések árlista API-jával az összes Azure-és piactér-használati szolgáltatás árát megtekintheti. A számlázási profilhoz tartozó díjak a számlázási profilhoz tartozó összes előfizetésre érvényesek.

Az árlista API-val az összes Azure-beli felhasználási szolgáltatás árlista-adatai CSV formátumban jeleníthetők meg:

| Módszer | Kérelem URI-ja |
| --- | --- |
| UTÁNI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

A árlista API-val az összes Azure-beli használati árlista JSON-formátumú adatai megtekinthetők:

| Módszer | Kérelem URI-ja |
| --- | --- |
| UTÁNI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Az API használatával visszaadja a teljes fiók árlista értékét. Ugyanakkor PDF formátumban is lekérheti az árlista tömörített verzióját. Az összefoglalás magában foglalja az Azure-beli felhasználást és a piactér használati szolgáltatásait, amelyek számlázása egy adott számla esetében történik. A számlát a ({invoiceId}) azonosítja, amely megegyezik a számla összefoglaló PDF-fájlokban megjelenő **számla számával** . Íme egy példa.

![Példa a InvoiceId megfelelő számla számát ábrázoló képre](./media/migrate-cost-management-api/invoicesummary.png)

A számlázási adatok megtekintése az árlista API-val CSV formátumban:

| Módszer | Kérelem URI-ja |
| --- | --- |
| UTÁNI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

A számlázási adatok megtekintése az árlista API-val JSON formátumban:

| Módszer | Kérelem URI-ja |
| --- | --- |
| UTÁNI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Az aktuálisan nyitott számlázási ciklusban vagy szolgáltatási időszakban a becsült árakat is megtekintheti bármely Azure-beli fogyasztási vagy piactér-használati szolgáltatáshoz.

A fogyasztói szolgáltatások becsült árainak az árlista API-val való megtekintése CSV formátumban:

| Módszer | Kérelem URI-ja |
| --- | --- |
| UTÁNI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

A fogyasztási szolgáltatások becsült árának megtekintése az árlista API-val JSON formátumban:

| Módszer | Kérelem URI-ja |
| --- | --- |
| UTÁNI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

A Microsoft Customer Agreement árlista API-jai *ASZINKRON REST API*-k. Az API-k válaszai megváltoztak a régebbi szinkron API-kkal. Az API-válasz törzse is megváltozott.

#### <a name="old-response-body"></a>Régi válasz törzse

Példa a szinkron REST API válaszra:

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

Az API-k támogatják az [Azure Rest aszinkron](../azure-resource-manager/resource-manager-async-operations.md) formátumot. Hívja meg az API-t a GET használatával, és a következő választ kapja:

```
No Response Body

HTTP Status 202 Accepted
```

A rendszer a következő fejléceket küldi el a kimenet helyére:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Kérjen meg egy másik hívást a helyhez. A GET hívásra adott válasz ugyanaz, amíg a művelet el nem éri a befejezési vagy a meghibásodási állapotot. Ha elkészült, a GET Call (hívás fogadása) helyre küldött válasz a letöltési URL-címet adja vissza. Ugyanúgy, mint a művelet végrehajtásakor. Például:

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

Az ügyfél lekéréses hívást is végezhet a `Azure-AsyncOperation` értékkel. A végpont a művelet állapotát adja vissza.

Az alábbi táblázat a régebbi Enterprise Get árlista API mezőit mutatja be. Magában foglalja a Microsoft Customer-szerződések új árlista megfelelő mezőit:

| Régi tulajdonság | Új tulajdonság | Megjegyzések |
| --- | --- | --- |
| billingPeriodId  | _Nem alkalmazható_ | Nem alkalmazható. A Microsoft Customer-szerződések esetében a számla és a hozzá tartozó árlista a billingPeriodId fogalmát váltotta fel. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | A pontos karakterlánc-értékek eltérőek lehetnek. |
| includedQuantity  | includedQuantity | Nem alkalmazható a Microsoft ügyfél-szerződések szolgáltatásaira. |
| PartNumber  | _Nem alkalmazható_ | Ehelyett használja a productOrderName (ugyanaz, mint a offerId) és a meterid kombinációját. |
| unitPrice  | unitPrice | Az egység ára a Microsoft ügyfél-szerződésekben felhasznált szolgáltatásokra vonatkozik. |
| currencyCode  | pricingCurrency | A Microsoft ügyfél-szerződések díjszabása az árképzési pénznemben és a számlázási pénznemben történik. A currencyCode megfelel a Microsoft pricingCurrency. |
| OfferId | productOrderName | A OfferId helyett használhatja a productOrderName, de nem ugyanaz, mint a OfferId. A productOrderName és a mérő azonban meghatározza a meterId és a OfferID-hez kapcsolódó Microsoft-ügyfél-szerződések díjszabását a régi regisztrációk során. |

## <a name="consumption-price-sheet-api-operations"></a>Felhasználási árlista API-műveletei

Nagyvállalati szerződések esetében a subscriptionId vagy számlázási időszak alapján a használati árlista API-t [lekérdezheti](/rest/api/consumption/pricesheet/get) és [lekérheti számlázási időszakra](/rest/api/consumption/pricesheet/getbybillingperiod) vonatkozó műveletek alapján. Az API Azure Resource Management-hitelesítést használ.

Az árlista API-val rendelkező hatókörhöz tartozó árlista-információk lekérése:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Az árlista-információk számlázási időszakonkénti beszerzése az árlista API-val:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

A fenti API-végpontok helyett használja a következőt a Microsoft ügyfél-szerződésekhez:

**Árlista API a Microsoft Customer-szerződésekhez (aszinkron REST API)**

Ez az API a Microsoft ügyfél-szerződésekhez kapcsolódik, és további attribútumokat is biztosít.

**Számlázási profil hatóköréhez tartozó árlista egy számlázási fiókban**

Ez az API a meglévő API. Frissítve lett a számlázási profilhoz tartozó árlista megadására egy számlázási fiókban.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Egy hatókörre vonatkozó árlista számlázási fiók alapján

Azure Resource Manager hitelesítést akkor használja a rendszer, amikor a regisztrációs hatókörbe beolvassa az árlistát a számlázási fiókban.

Az árlista beszerzése egy számlázási fiókban a beléptetési fiókban:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft-ügyfél szerződés esetén használja a következő szakaszban található információkat. Ez biztosítja a Microsoft ügyfél-szerződésekhez használt mezőtulajdonságokat.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Számlázási profil hatóköréhez tartozó árlista egy számlázási fiókban

A frissített árlista a Számlázási fiók API-val CSV formátumban kapja meg az árlistát. Az MCA számlázási profiljának hatókörében lévő árlista beszerzése:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Az EA regisztrációs hatókörében az API-válasz és a tulajdonságok azonosak. A tulajdonságok ugyanahhoz az MCA-tulajdonsághoz tartoznak.

A [Azure Resource Manager árlista API](/rest/api/consumption/pricesheet) -k régebbi tulajdonságai és ugyanazok az új tulajdonságok a következő táblázatban találhatók.

| A régi Azure Resource Manager árlista API-tulajdonsága  | Új Microsoft Customer Agreement árlista API-tulajdonság   | Leírás |
| --- | --- | --- |
| Meter ID (Mérési azonosító) | _meterId_ | A fogyasztásmérő egyedi azonosítója. Ugyanaz, mint a meterId. |
| Fogyasztásmérő neve | meterName | A fogyasztásmérő neve. A mérő az Azure szolgáltatás üzembe helyezhető erőforrását jelöli. |
| Fogyasztásmérő kategóriája  | szolgáltatás | A fogyasztásmérő osztályozási kategóriájának neve. Ugyanaz, mint a Microsoft ügyfél-szerződés árlista szolgáltatásában. A karakterláncok pontos értékei eltérnek. |
| Fogyasztásmérő alkategóriája | meterSubCategory | A fogyasztásmérő osztályozási alkategóriájának neve. A magas szintű szolgáltatáskészlet besorolása alapján a szolgáltatás megkülönbözteti a szolgáltatást. Például: alapszintű SQL DB vs standard SQL DB. |
| Mérési régió | meterRegion | &nbsp;  |
| Unit (Egység) | _Nem alkalmazható_ | A unitOfMeasure-ből elemezhető. |
| Mértékegység | unitOfMeasure | &nbsp;  |
| Cikkszám | _Nem alkalmazható_ | A partNumber helyett a productOrderName és a MeterId használatával egyedileg azonosíthatja a számlázási profil árát. A mezők az MCA-számlán jelennek meg az MCA-számlákon lévő partNumber helyett. |
| Egységár | unitPrice | Microsoft ügyfél-szerződési egység díja. |
| Pénznemkód | pricingCurrency | A Microsoft ügyfél-szerződések a díjszabási pénznemben és a számlázási pénznemben is érvényesek. A Pénznemkód ugyanaz, mint a Microsoft ügyfél-szerződések pricingCurrency. |
| Included quantity (Bennefoglalt mennyiség) | includedQuantity | Nem alkalmazható a Microsoft ügyfél-szerződések szolgáltatásaira. Megjelenítés nulla értékkel. |
|  Ajánlat azonosítója  | productOrderName | A OfferId helyett használja a productOrderName. Nem ugyanaz, mint a OfferId, azonban a productOrderName és a mérőszám határozza meg a Microsoft ügyfél-szerződések díjszabását. Az örökölt regisztrációk meterId és OfferID kapcsolatos. |

A Microsoft ügyfél-szerződések díja a nagyvállalati szerződéseknek eltérően van definiálva. A nagyvállalati beléptetéssel kapcsolatos szolgáltatások díja egyedi a termékre, a PartNumber, a mérőre és az ajánlatra vonatkozóan. A PartNumber nincs használatban a Microsoft ügyfél-szerződésekben.

A Microsoft ügyfél-szerződés részét képező Azure-használati díj egyedi a productOrderName és a meterId. Ők képviselik a szolgáltatási mérőszámot és a termék tervét.

Az árlista és a használat részleteit tartalmazó API használatának egyeztetéséhez használhatja a productOrderName és a meterId.

A számlázási profil tulajdonosával, közreműködővel, olvasóval és a számlafogadó-kezelő jogosultsággal rendelkező felhasználók letöltheti az árlistát.

Az árlista azon szolgáltatások árait tartalmazza, amelyek díjszabása a használat alapján történik. A szolgáltatások közé tartozik az Azure-felhasználás és a piactér-használat. Az egyes szolgáltatási időszakok végén a legutolsó díj zárolva van, és a rendszer egyetlen szolgáltatási időszakban alkalmazza a használatot. Az Azure-beli felhasználási szolgáltatások esetében a szolgáltatási időszak általában naptári hónap.

### <a name="retired-price-sheet-api-fields"></a>Kivont árlista API-mezői

A következő mezők vagy nem érhetők el a Microsoft Customer Agreement árlista API-jai vagy azonos mezőkkel.

|Kivont mező| Leírás|
|---|---|
| billingPeriodId | Nem alkalmazható. Az MCA InvoiceId felel meg. |
| OfferId | Nem alkalmazható. Az MCA productOrderName felel meg. |
| meterCategory  | Nem alkalmazható. Az MCA szolgáltatásnak felel meg. |
| egység | Nem alkalmazható. A unitOfMeasure-ből elemezhető. |
| currencyCode | Ugyanaz, mint a pricingCurrency az MCA-ben. |
| meterLocation | Ugyanaz, mint a meterRegion az MCA-ben. |
| partNumber partnumber | Nem alkalmazható, mert a cikkszám nem szerepel az MCA-számlákon. A partnumber helyett használja a meterId és a productOrderName kombinációt az árak egyedi azonosításához. |
| totalIncludedQuantity | Nem alkalmazható. |
| pretaxStandardRate  | Nem alkalmazható. |

## <a name="reservation-instance-charge-api-replaced"></a>Lecserélt foglalási példány díja (API)

A foglalási beszerzésekhez tartozó számlázási tranzakciókat a [fenntartott példány díja API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)-val érheti el. Az új API magában foglalja az összes vásárlást, beleértve a harmadik féltől származó Piactéri ajánlatokat is. Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request). A fenntartott példány díjszabási API-ját a Transactions API váltja fel.

A foglalások beszerzési tranzakcióinak beszerzése a Transactions API-val:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Javaslatok API-k lecserélve

A fenntartott példányok vásárlására vonatkozó javaslatok API-k biztosítják a virtuális gépek használatát az elmúlt 7, 30 vagy 60 napon belül. Az API-k foglalási vásárlási javaslatokat is biztosítanak. Ezek például az alábbi jelentések lehetnek:

- [Megosztott fenntartott példányokra vonatkozó javaslat API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Egy fenntartott példány Recommendations API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request). A korábban felsorolt foglalási javaslatok API-kat a [Microsoft. fogyasztás/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API váltja fel.

Foglalási javaslatok beszerzése a foglalási Recommendations API:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Lecserélt foglalási használati API-k

A foglalási használatot a Fenntartott példány használata API-val való regisztráció során kérheti le. Ha egynél több fenntartott példány van regisztrálva egy beléptetéshez, az API-val az összes fenntartott példány vásárlásának használatát is lekérheti.

Ezek például az alábbi jelentések lehetnek:

- [Fenntartott példány használata részletei](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Fenntartott példány használata összegzése](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Az összes felhasználási API-t az Azure AD-t használó natív Azure API-k váltották fel hitelesítéshez és engedélyezéshez. Az Azure REST API-k meghívásával kapcsolatos további információkért lásd: [Bevezetés a REST használatába](/rest/api/azure/#create-the-request). A korábban felsorolt foglalási javaslatok API-k helyébe a [Microsoft. fogyasztás/reservationDetails](/rest/api/consumption/reservationsdetails) és a [Microsoft. fogyasztás/reservationSummaries](/rest/api/consumption/reservationssummaries) API-k tartoznak.

A foglalás részleteit a foglalás részletei API-val szerezheti be:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

A foglalási összefoglalók beszerzése a foglalási összesítő API-val:

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Áthelyezés a Cloudyn a Cost Managementba

A [Cloudyn](https://cloudyn.com) használó szervezeteknek meg kell kezdeniük [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) használatát a Cost Management igényeihez. Cost Management a bevezetést és a nyolc órás késést nem tartalmazó Azure Portal érhető el. További információ: [Cost Management dokumentáció](index.yml).

A Azure Cost Management a következőket teheti:

- Megtekintheti az előre meghatározott költségvetést érintő költségeket. A napi költségek elemzésével azonosíthatja és leállíthatja a kiadások rendellenességeit. A költségek kibontása címkék, Erőforráscsoport, szolgáltatás és hely szerint.
- Hozzon létre költségvetéseket a használat és a költségek korlátainak megadásához, és kapjon értesítést, ha fontos küszöbértékek vannak megközelítve. Hozzon létre automatizálást műveleti csoportokkal az egyéni események elindításához és a rögzített korlátok kikényszerített feltételeihez.
- A költségeket és a használatot a Azure Advisor ajánlásai alapján optimalizálhatja. Fedezze fel a vásárlási optimalizálásokat foglalásokkal, csökkentse a felhasznált virtuális gépeket, és törölje a használaton kívüli erőforrásokat, hogy a költségvetéseken belül maradjon.
- A Cost és a használati adatok exportálásának megadásával napi rendszerességgel közzétehet egy CSV-fájlt a Storage-fiókjában. A külső rendszerekkel való integráció automatizálása a számlázási adatok szinkronizálásban és naprakészen tartásához.

## <a name="power-bi-integration"></a>Power BI-integráció

Ha Power BIt használ a Cost Reporting szolgáltatáshoz, át kell térnie a következőre:

- Microsoft Azure Consumption Insights Power BI alkalmazás
- Azure Consumption Insights asztali összekötő


Az összekötő ajánlott olyan szervezetek számára, akik a legrugalmasabbak. Azonban a Power BI alkalmazás is elérhető a gyors üzembe helyezéshez.

- A [Microsoft Azure Consumption Insights Power bi alkalmazás](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) telepítése
- [Csatlakozás az Azure Consumption Insights-összekötővel](/power-bi/desktop-connect-azure-consumption-insights)

A régebbi felhasználású adatellenőrzési tartalom csomagja és összekötője regisztrációs szinten működött. Legalább olvasási hozzáféréssel kell rendelkeznie. A számlázási profil felhasználói számára elérhető az új felhasználási információk Power BI alkalmazás és az új Azure Consumption Insights-összekötő. Azok a csapatok, amelyeknek további beállításokra van szükségük a költségek áttekintéséhez vagy a számlázási profilok költségeinek megtekintéséhez, a Azure Portal [költség elemzésében](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) kell használniuk.

## <a name="next-steps"></a>Következő lépések

- Olvassa el a [Cost Management dokumentációját](index.yml) , amelyből megtudhatja, hogyan figyelheti és vezérelheti az Azure-kiadásokat. Vagy ha az erőforrás-használatot Cost Management használatával szeretné optimalizálni.
