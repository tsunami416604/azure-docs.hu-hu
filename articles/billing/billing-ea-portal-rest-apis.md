---
title: Azure Enterprise REST API-k
description: Ez a cikk az Azure nagyvállalati regisztrációhoz használható REST API-kat ismerteti.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a8cf0d2eb87a823ee4b1d27484e7ea8d89c1abfc
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105442"
---
# <a name="azure-enterprise-rest-apis"></a>Azure Enterprise REST API-k

Ez a cikk az Azure nagyvállalati regisztrációhoz használható REST API-kat ismerteti. Ezenkívül azt is bemutatja, hogyan háríthatja el a REST API-k leggyakoribb hibáit.

## <a name="consumption-and-usage-apis"></a>Fogyasztási és használati API-k

A Microsoft Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek a REST API-kon keresztül kérhetik le a használati és számlázási információkat. A szerepkör tulajdonosának (a vállalati rendszergazdának, a részlegszintű rendszergazdának vagy a fióktulajdonosnak) engedélyeznie kell az API-hoz való hozzáférést egy kulcs létrehozásával az Azure EA Portalon. Ezután a regisztrációs szám és kulcs tulajdonában bárki hozzáférhet az adatokhoz az API-n keresztül.

### <a name="available-apis"></a>Elérhető API-k

**Egyenleg és összefoglalás** – A [Balance és Summary API](billing-enterprise-api-balance-summary.md) havi összefoglaló információkat nyújt az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről. További információt a [Nagyvállalati ügyfeleknek elérhető jelentéskészítési API-k – Egyenleg és összefoglalás](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) rész tartalmaz.

**Használati adatok** – A [Usage Details API](billing-enterprise-api-usage-detail.md) a felhasznált mennyiségek napi részletezését és az adott regisztrációhoz kapcsolódó becsült költségeket jeleníti meg. Az eredmény a példányokkal, mérőszámokkal és részlegekkel kapcsolatos információkat is tartalmazza. Az API lekérdezhető számlázási időszak vagy egy megadott kezdő és záró dátum alapján. További információt a [Nagyvállalati ügyfeleknek elérhető jelentéskészítési API-k – Használati adatok](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) rész tartalmaz.

**Marketplace-díjak** – A [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) a megadott számlázási időszakra, illetve a kezdési és befejezési dátumokra vonatkozóan napi bontásban adja vissza a használaton alapuló Marketplace-díjakat. További információt a [Nagyvállalati ügyfeleknek elérhető jelentéskészítési API-k – Marketplace-díjak](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) rész tartalmaz.

**Árlista** – A [Price Sheet API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) a regisztrációs és számlázási időszakokra vonatkozóan az egyes mérők esetében érvényes díjszabást biztosítja. További információt a [Nagyvállalati ügyfeleknek elérhető jelentéskészítési API-k – Árlista](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) rész tartalmaz.

**Számlázási időszakok** – A [Billing Periods API](billing-enterprise-api-billing-periods.md) olyan számlázási időszakok listáját adja vissza, amelyekben egy regisztráció használati adatai szerepelnek fordított időrendi sorrendben. Minden időszak tartalmazza a négy következő adathalmaz API-útvonalára mutató egyik tulajdonságot: BalanceSummary, UsageDetails, Marketplace Charges és Price Sheet. További információt a [Nagyvállalati ügyfeleknek elérhető jelentéskészítési API-k – Számlázási időszakok](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) rész tartalmaz.

### <a name="enable-api-data-access"></a>API-adathozzáférés engedélyezése

A szerepkörök tulajdonosai a következő lépéseket hajthatják vége az Azure EA Portalon. A **Jelentések** > **Használati adatok letöltése** > **API hozzáférési kulcsa** menübe navigálva a következőket hajthatják végre:

- Elsődleges és másodlagos hozzáférési kulcsok létrehozása
- Hozzáférési kulcsok letiltása
- Hozzáférési kulcsok kezdő és záró dátumainak megtekintése

### <a name="generate-or-retrieve-the-api-key"></a>Az API-kulcs létrehozása vagy lekérése

1. Jelentkezzen be vállalati rendszergazdaként.
2. Kattintson a **Jelentések** elemre a bal oldali navigációs ablakban, majd kattintson a **Használati adatok letöltése** lapra.
3. Kattintson az **API hozzáférési kulcsa** elemre.
4. A **Regisztrációs hozzáférési kulcsok** területen válassza ki a kulcslétrehozási szimbólumot elsődleges vagy másodlagos kulcs létrehozásához.
5. Válassza a **Kulcs kibontása** lehetőséget a teljes létrehozott API hozzáférési kulcs megtekintéséhez.
6. Válassza a **Másolás** lehetőséget az API hozzáférési kulcs azonnali használatához.

![Az API hozzáférési kulcsok oldalát bemutató példa](./media/billing-ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Amennyiben olyan személyeknek szeretné megadni az API hozzáférési kulcsokat, akik nem vállalati rendszergazdák az Ön regisztrációjában, az alábbi lépéseket kell végrehajtania:

1. A bal oldali navigációs ablakban kattintson a **Kezelés** elemre.
2. Kattintson a ceruza szimbólumra **Díjtételek megtekintése a részlegszintű rendszergazda számára** elem mellett.
3. Válassza az **Engedélyezés** lehetőséget, majd kattintson a **Mentés** elemre.
4. Kattintson a ceruza szimbólumra a **Díjtételek megtekintése a fióktulajdonos számára** elem mellett.
5. Válassza az **Engedélyezés** lehetőséget, majd kattintson a **Mentés** elemre.

![Példa arra, amikor a díjtételek megtekintése engedélyezve van a részlegszintű rendszergazda és a fióktulajdonos számára](./media/billing-ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) Az előző lépések lehetővé teszik az API hozzáférési kulccsal rendelkező személyeknek a költségadatokhoz és a díjszabási információkhoz történő hozzáférést a használati jelentésekben.

### <a name="pass-keys-in-the-api"></a>API-kulcsok átadása

Adja át az API-kulcsokat minden hitelesítési és engedélyezési híváshoz. Adja meg a következő tulajdonságot a HTTP-fejlécekben:

| Kérelemfejléc kulcsa | Érték |
| --- | --- |
| Engedélyezés | Adja meg az értéket ebben a formátumban: **tulajdonos {API\_-KULCS}**
Példa: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Swagger-végpont érhető el az [Enterprise Reporting v3 API-kban](https://consumption.azure.com/swagger/ui/index) a következő API-khoz. A Swagger segít az API vizsgálatában. A Swagger segítségével ügyféloldali SDK-kat hozhat létre az [AutoRest](https://github.com/Azure/AutoRest) vagy a [Swagger CodeGen](http://swagger.io/swagger-codegen/) használatával. A 2014. május 1. után elérhető adatok az API-n keresztül érhetők el.

### <a name="api-response-codes"></a>API-válaszkódok

API használatakor megjelennek a válasz állapotkódjai. Az alábbi táblázat ismerteti ezeket.

| Válasz állapotkódja | Üzenet | Leírás |
| --- | --- | --- |
| 200 | OK | Nincs hiba |
| 401 | Nem engedélyezett | Az API-kulcs nem található, érvénytelen, lejárt stb. |
| 404 | Nem érhető el | A jelentésvégpont nem található |
| 400 | Hibás kérés | Érvénytelen paraméterek – Dátumtartományok, EA-számok stb. |
| 500 | Kiszolgálóhiba | Váratlan hiba a kérelem feldolgozása során |

### <a name="usage-and-billing-data-update-frequency"></a>Használati és számlázási adatok frissítési gyakorisága

A használati és számlázási adatfájlok 24 óránként frissülnek az aktuális számlázási hónapban. Adatkésés azonban akár három napig is előfordulhat. Ha például a használat hétfőn történt, akkor előfordulhat, hogy az adatok csütörtökig nem jelennek meg az adatfájlban.

### <a name="test-enrollment-for-development"></a>Tesztregisztráció a fejlesztéshez

Ha Ön olyan partner vagy fejlesztő, aki nem rendelkezik nagyvállalati Azure-regisztrációval, de hozzá szeretne férni az API-hoz, használhatja a tesztregisztrációt. A regisztráció neve _EnrollmentNumber 100_. Ezután [a regisztrációs hozzáférési kulccsal](https://automaticbillingspec.blob.core.windows.net/spec/TestEnrollmentUsageApiKey.txt) tesztelheti az API-hívást és megtekintheti a mintaadatokat.

### <a name="azure-service-catalog"></a>Azure szolgáltatáskatalógus

Az Azure-szolgáltatások listája megtalálható CSV formátumban az Azure Storage-blogon elérhető katalógusokban. A katalógus akkor hasznos, ha létre kell hoznia egy, a rendszere összes Azure-szolgáltatását tartalmazó válogatott katalógust. Az aktuális katalógus a következő címen érhető el: [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>CSV-adatfájl részletei

Az alábbi információk az API-jelentések tulajdonságait ismertetik.

#### <a name="usage-summary"></a>Használati adatok összegzése

A JSON formátum a CSV-jelentésből jön létre. Ennek következtében a formátum azonos az összegző CSV-fájl formátumával. Az oszlopneveket érdemes deszerializálni egy adattáblába a JSON-formátumú összefoglaló adatok felhasználásakor.

| Oszlopnév a CSV-fájlban | Oszlopnév a JSON-fájlban | Új oszlop a JSON-fájlban | Megjegyzés |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Fiók neve | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Subscription Name | SubscriptionName | SubscriptionName |   |
| Dátum | Dátum | Dátum | Azt a dátumot jeleníti meg, amikor a szolgáltatáskatalógus jelentése futott. A formátum egy időbélyeg nélküli dátumsztring. |
| Month | Month | Month |   |
| Day | Day | Day |   |
| Year | Year | Year |   |
| Product | BillableItemName | Product |   |
| Meter ID (Mérési azonosító) | ResourceGUID | MeterId |   |
| Meter Category (Mérési kategória) | Szolgáltatás | MeterCategory | A szolgáltatások keresésében segít. Olyan szolgáltatásokhoz használható, amelyeknek több szolgáltatástípusa van. Például Virtual Machines. |
| Meter Sub-Category (Mérési alkategória) | ServiceType | MeterSubCategory | Második szintű információkat nyújt a szolgáltatásokról. Például A1 méretű VM (nem Windows).  |
| Meter Region (Mérési régió) | ServiceRegion | MeterRegion | Harmadik szintű információ a szolgáltatásokhoz. Segít megtalálni a ResourceGUID régiókörnyezetét. |
| Meter Name (Mérés neve) | ServiceResource | MeterName | A szolgáltatás neve. |
| Consumed Quantity (Felhasznált mennyiség) | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Resource Location (Erőforrás helye) | ServiceSubRegion | ResourceLocation |   |
| Consumed Service (Használt szolgáltatás) | ServiceInfo | ConsumedService |   |
| Instance ID (Példányazonosító) | Összetevő | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Címkék | Címkék | Címkék |   |
| Store Service Identifier   | OrderNumber | StoreServiceIdentifier   |   |
| Department Name | DepartmentName | DepartmentName |   |
| Cost Center | CostCenter | CostCenter |   |
| Mértékegység | UnitOfMeasure | UnitOfMeasure | Példaértékek: Óra, GB, Események, Leküldések, Egység, Egységórák, MB, Napi egységek |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure Marketplace-jelentés

| Oszlopnév a CSV-fájlban | Oszlopnév a JSON-fájlban | Új oszlop a JSON-fájlban |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Fiók neve | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Subscription Name | SubscriptionName |  SubscriptionName |
| Dátum | BillingCycle |  Dátum (Csak dátumsztring. Nincs időbélyeg)
| Month | Month |  Month |
| Day | Day |  Day |
| Year | Year |  Year |
| Meter ID (Mérési azonosító) | MeterResourceId |  MeterId |
| Publisher Name | PublisherFriendlyName |  Közzétevő neve |
| Offer Name | OfferFriendlyName |  OfferName |
| Plan Name | PlanFriendlyName |  PlanName |
| Consumed Quantity (Felhasznált mennyiség) | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Mértékegység | UnitOfMeasure | UnitOfMeasure |
| Instance ID (Példányazonosító) | InstanceId | InstanceId |
| Additional Info (További információ) | AdditionalInfo | AdditionalInfo |
| Címkék | Címkék | Címkék |
| Order Number | OrderNumber | OrderNumber |
| Department Name | DepartmentNames | DepartmentName |
| Cost Center | CostCenters |  CostCenter |
| Erőforráscsoport | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Árlista

| Oszlopnév a CSV-fájlban | Oszlopnév a JSON-fájlban | Megjegyzés |
| --- | --- | --- |
| Szolgáltatás | Szolgáltatás |  Nincs árbeli változás |
| Mértékegység | UnitOfMeasure |   |
| Overage Part Number | ConsumptionPartNumber |   |
| Overage Unit Price | ConsumptionPrice |   |
| Currency Code | CurrencyCode |     |

### <a name="common-api-issues"></a>Gyakori API-problémák

Az Azure Enterprise REST API-k használatakor a következő gyakori problémák bármelyikét tapasztalhatja.

Elképzelhető, hogy olyan API-kulcsot használ, amely nem a megfelelő engedélyezési típussal rendelkezik. Az API-kulcsokat az alábbiak hozzák létre:

- Vállalati rendszergazda
- Részlegszintű rendszergazda (DA)
- Fióktulajdonos (AO)

A vállalati rendszergazda által előállított kulcs hozzáférést biztosít az adott regisztrációhoz tartozó összes információhoz. Egy csak olvasási engedéllyel rendelkező vállalati rendszergazda nem tud létrehozni API-kulcsot.

Egy részlegszintű rendszergazda vagy fióktulajdonos által létrehozott kulcs nem biztosít hozzáférést az egyenleghez, a díjakhoz és az árlistához.

Az API-kulcsok hathavonta járnak le. Ha lejártak, újra létre kell hoznia őket.

Ha időtúllépési hibát tapasztal, megoldhatja azt az időtúllépési küszöbértékkorlát megnövelésével.

401-es (nem engedélyezett) lejárati hibaüzenetet kaphat. A hibát általában egy lejárt kulcs okozza. Ha a kulcs lejárt, újból létrehozhatja.

Az API-hívásokból 400-as és 404-es (nem érhető el) hibaüzenetek jelenhetnek meg, ha a kiválasztott dátumtartományhoz nem érhető el aktuális adat. Ez a hiba például akkor fordulhat elő, ha nemrég fiókátvitelt kezdeményeztek. Az adatok egy adott dátumtól kezdve egy új regisztrációban találhatók. A hiba akkor is előfordulhat, ha új regisztrációs számot használ a régi regisztrációban található információk lekéréséhez.

## <a name="next-steps"></a>További lépések

- Az Azure EA Portal rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Portal adminisztrációjáról](billing-ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](billing-ea-portal-troubleshoot.md).
