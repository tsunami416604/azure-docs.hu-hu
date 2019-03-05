---
title: Áttelepítés a nagyvállalati szerződés a Microsoft ügyfél-megállapodás API-k – Azure |} A Microsoft Docs
description: Ez a cikk segít megérteni az API-k gyakorolt hatása, a Microsoft nagyvállalati szerződés (EA) a Microsoft vevői Szerződéssel való migráláskor.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: a12360e60026fd4251cc232caccd1bc52a34d2ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344782"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Áttelepítés a nagyvállalati szerződés a Microsoft ügyfél-megállapodás API-k

Ez a cikk segít megérteni az API-k gyakorolt hatása, a Microsoft nagyvállalati szerződés (EA) a Microsoft vevői Szerződéssel való migráláskor. A költségek és a díjszabás megtekintéséhez a következő új API-k használhatók:

- Árlista API-hoz a Microsoft ügyfél-megállapodások esetén
  - A számla ellenében árlista
  - Árlista számlázási profil
  - A megnyitott számlázási időszak árlista
- API használati adatait a Microsoft ügyfél-szerződések
  - Invoice (számla szakasz szűrés) profil számlázási használati adatok
    - Használat részletei használati dátum szerint
    - Számlázási fiók használati adatait
    - A számlázás profil használati adatait
    - Számla szakasz használati adatait
    - Előfizetés használati adatait
  - Használat részletei az elmúlt hónapban
    - Számlázási fiók használati adatait
    - A számlázás profil használati adatait
    - Számla szakasz használati adatait
    - Előfizetés használati adatait
  - Használat részletei számlázási időszak szerint
- API-kreditek a Microsoft ügyfél-szerződés
  - Aktuális egyenleg összefoglaló API számlázási profil
  - Hitelkockázat-események API-számlázási profillal
  - Hitelkockázat-sok API által a számlázási profilja
- Lekérdezési API
- Díjak API
  - Díjak API által a számlázási fiók
  - A számlázás profil API díjak
  - Díjak szerint számla szakasz API
- Invoice List API
  - A számlázás profil
  - Számlázási fiók
- Foglalási API-k
- Power BI-integráció API
- Export API
  - Számlázási fiók
  - a számlázás profil
  - A számla szakasz
- Tranzakciós API
- Költségvetés API
- Összesített költsége API

## <a name="effect-on-automation"></a>Automation hatása

A Microsoft vevői szerződéssel rendelkező használt API-k eltérnek a nagyvállalati szerződéssel rendelkező használtakkal. Előfordulhat, hogy állított be API automation korábban a vállalati-alapú API-k vagy Azure Resource Manager-alapú API-k vállalati belépéseket. Ha igen, frissítse az automation-konfigurációjához, amely a Microsoft ügyfél-megállapodások használt újabb API-k.

Microsoft-ügyfelek szerződései régebbi nem támogatott API-k listáját, olvassa el [nem támogatott API-k](#unsupported-apis).

API-k a Microsoft ügyfél-megállapodások esetén csak az Azure Resource Manager-hitelesítés támogatásához – API-kulcs alapú hitelesítés nem támogatják.

## <a name="pricesheet-apis-show-prices-for-azure-services"></a>Árlista API-k megjelenítése az Azure-szolgáltatások díjai

Az árlista API-k a Microsoft Ügyfélszerződéséhez tartalmazzák az összes olyan szolgáltatás, amely a számlázás során értékelése a díjszabásról használati mennyiség alapján. Az API-t nem tartalmazza a jogosultság vásárlásokra. Például piactér-beli vásárlásokra jogosultságot, fenntartott példány árai és támogatási ajánlatainkat is. Az API-t biztosít az egyedi ár egyes szolgáltatásokhoz tartozó, egy adott mérőszám sorrendje a název produktu.

### <a name="view-the-pricesheet-for-azure-consumption-services-by-billing-profile"></a>Számlázási profil megtekintéséhez az Azure-szolgáltatások használati szolgáltatások árlistát kéri le

A számlázási profilja árlistát kéri le az első és harmadik fél Azure Consumption szolgáltatások árai tekintheti meg. Az API segítségével megtekintheti az Azure-szolgáltatások használati szolgáltatások árai. A számlázási profil díjak vonatkoznak a profilhoz tartozó számlázási előfizetéseken.

- CSV-formátum
  - POST -`https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31& format=csv`
- JSON-formátumban
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json`

### <a name="view-current-open-billing-or-service-period-estimated-prices"></a>Nézet aktuális nyissa meg a számlázás vagy szolgáltatás időszak becsült árak

Szeretné megtekinteni a becsült bármely szolgáltatás díjszabása az aktuális nyílt elszámolási vagy szolgáltatási pontot, megtekintheti az árlista CSV vagy JSON formátumban.

- CSV-formátum
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`
- JSON-formátumban
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

Az árlista mindig az aktuális szolgáltatás időszak látható díjak a mérőszám a legújabb díjszabása. Árak a legújabb ár használatával a mérőszám és a termék rendelés naponta frissülnek.

Amikor ellenőrzi a költségeket az open időszakban árán alapul, költségek becslése. Nyissa meg a szolgáltatási időszak végén az utolsó zárolt ár a nyitott időszak kihasználtsága vonatkozik.

A megnyitott időszakot jelenti aktuális szolgáltatás, a használat nem még értékelése a költségszámítás és a számlázással.

### <a name="view-invoice-by-billing-profile-prices"></a>Számlázási profil díjak szerint számla megtekintése

Azure Consumption árával, amelyek számlázása az adott számlán számlázási profilok tekintheti meg. Az API-val árai csak a felsorolt szolgáltatások megtekintéséhez a számlán. A számla invoice száma azonosítja.

- CSV-formátum
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`

- JSON-formátumban
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

A `{invoiceId}` felsorolt korábban pedig ugyanaz, mint a **számlaszám** a számla összegzése PDF-fájl látható. Íme egy példa.

![A példában a kép a számla számát, amely megfelel a InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

## <a name="pricesheet-api-asynchronous-response"></a>Árlista API aszinkron válasz

Az API-k támogatják a [Azure REST aszinkron](../azure-resource-manager/resource-manager-async-operations.md) formátumban. Miután hívja az API-k használatával GET, a következő válasz jelenik meg:

```
            No Response Body
            HTTP Status 202 Accepted
```

 A kimeneti helye a következő fejlécek lesznek elküldve.

```
Location: https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
Azure-AsyncOperation: https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
   Retry-After: 10
 OData-EntityId: {operationId}
```

Az ügyfél teheti egy GET hívást kell a helyet. A GET-hívásra adott válasz megegyezik a korábban az látható, amíg a művelet sikeres vagy sikertelen állapotba példa információkat. Amikor elkészült, a hívást a GET helye határozza meg a választ a, ha a művelet végre lett hajtva egy időben ugyanazt a választ adja vissza. Íme egy példa.

```
HTTP Status 200
i.e.
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

Emellett az ügyfél is hívással GET a `Azure-AsyncOperation`. A végpont a művelet állapotát adja vissza. Például:

```
HTTP Status 200
               {
"id": “providers/Microsoft.Consumption/operationStatus/{operationId}",
"name": "operationId",
"status" : "Succeeded | Failed | Canceled",
"startTime": "<DateLiteral per ISO8601>",
"endTime": "<DateLiteral per ISO8601>",
"percentComplete": <double between 0 and 100>
"properties": {
    /\* The resource provider can choose the values here, but it should only be
       returned on a successful operation (status being "Succeeded"). \*/
},
"error" : {
    /\* This is the OData v4 format, used by the RPC and will go into the
     v2.2 Azure REST API guidelines \*/
    "code": "BadArgument",
    "message": "The provided database &#39;foo&#39; has an invalid username."
}
}
```

### <a name="pricesheet-fields"></a>Árlista mezők

Tekintse át a [a feltételek az árlista megismerheti a Microsoft vevői szerződés](../billing/billing-mca-understand-pricesheet.md) összes Árlista mezőt és a hozzájuk tartozó leírások megtekintéséhez.

A díjszabás a használat alapján, a használati díj időszak kezdete óta a szolgáltatás idejének zárolva van. Szolgáltatás ára alkalmazva, és a vásárlás időpontjában zárolva van. A szolgáltatás idő elsősorban az Azure-szolgáltatások általában egy adott naptári hónapban.

A termék sorrend és a mérőszám egyedi szolgáltatás díja az az Azure-szolgáltatások használati szolgáltatás a Microsoft ügyfél-szerződés.

A mezők segítségével egyedi módon azonosítja az Azure-szolgáltatások használati szolgáltatások díjszabása a következők:

- productOrderName
- meterId (emellett meterName)

Az ár a Microsoft ügyfél-megállapodások esetén működnek, mint a nagyvállalati szerződések, ahol a beléptetés a szolgáltatások árát esetében egyedi legyen van definiálva:

- Product
- Cikkszám
- Fogyasztásmérő
- az ajánlat

### <a name="pricesheet-support"></a>Árlista támogatása

Jelenleg az árlista API csak támogatja a Microsoft Ügyfélszerződéséhez árak az Azure belső és külső fogyasztás szolgáltatások. Árak az Azure hibrid előnyöket (AHUB), a Marketplace-en szolgáltatások és a fenntartott példányok nem támogatott.

Az API-t csak itt azt az Egységár, amely a szolgáltatás lesznek terhelve. A fizetendő díj kisebb vagy egyenlő, mint az egyeztetett ára.

## <a name="query-for-usage-and-costs"></a>Használat és költségek lekérdezése

A használati részletek API hozzáférést biztosít az Azure és a külső piactér szolgáltatás használat és a költségek. Az árak a szerződés egyeztetett árai alapulnak. Nagyvállalati szerződéssel rendelkező ügyfeleink egy Ügyfélszerződéséhez áthelyezése a használat részleteiről kell használnia a számlázási fiók vagy a számlázási profil hatókör. Meg lehet lekérdezést minden hatókörben, a következő kivételekkel:

|   | Ügyfél-szerződés | Nagyvállalati szerződés | Az egyes megállapodás (PAYG) |
| --- | --- | --- | --- |
| Számlázási fiók | Igen | Igen | – |
| A számlázás profilok (számla) | Igen | N/A. Számlázási fiók megtekintéséhez. | N/A. Tekintse meg az előfizetések. |
| Számlaszakaszok | Nem. Számlázási profilok használata a szűrőt. | – | – |
| Nagyvállalati szerződéssel rendelkező szervezeti egységek | – | Igen | – |
| EA regisztrációs fiókok | – | Igen | N/A. Tekintse meg az előfizetések. |
| Felügyeleti csoportok | Nem. Számlázási fiók használata. | Igen | Igen |
| Előfizetések | Igen | Igen | Igen |
| Erőforráscsoportok | Nem. Előfizetések szűrőt használhatja. | Nem. Előfizetések szűrőt használhatja. | Nem. Előfizetések szűrőt használhatja. |

A szerződés típusonként UsageDetails által visszaadott tulajdonságait eltérőek. Lásd: [UsageDetails API-dokumentáció](/rest/api/consumption/usagedetails/) teljes listáját.

### <a name="power-bi-integration"></a>Power BI-integráció

Nagyvállalati szerződéssel rendelkező ügyfeleink a Microsoft Ügyfélszerződéséhez áthelyezése álljon le, bármely meglévő integrációjának használata a Power BI használatával a nagyvállalati szerződésre vonatkozó adatokat a Microsoft Azure Consumption Insights. Ehelyett használhatja:

- Power BI Desktop –, hozzon létre új Power BI-jelentések a Microsoft Customer szerződés és a [Azure Cost Management-összekötő](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights).
- A Power BI szolgáltatás – való áttérés a [a Microsoft Azure Consumption Insights](https://docs.microsoft.com/power-bi/service-connect-to-azure-consumption-insights) érhető el az Azure Cost Management (Ügyfélszerződéséhez) alkalmazás-tartalomcsomaggal [AppSource](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview).

## <a name="unsupported-apis"></a>Nem támogatott API-k

A nagyvállalati szerződés következő API-k a Microsoft ügyfél-szerződések által nem támogatott. Nem támogatott API-k alternatívái ismerteti.

[Árlista által előfizetés](/rest/api/consumption/pricesheet/get/) nem támogatott. Egy előfizetés-azonosító hatóköre árai listáját. Ehelyett használhat **árlista-profillal számlázási**. A hatókör előfizetés a szolgáltatás ára megegyezik az ár hatókörébe tartozó számlázási profilja. Az API meghívása felhasználó szükséges engedélyek.

**Ár lap - lekérése a számlázási időszak** nem támogatott. Előfizetés-azonosító és a számlázási időszak hatókör kap az árlista ismertetését. Ehelyett használhat **árlista-profillal számlázási**. A szolgáltatás ára egy előfizetési hatókört egyenlő a szolgáltatási díj egy számlázási profil hatókörébe. Az API meghívása felhasználó szükséges engedélyek.

[Regisztráció a nagyvállalati vásárlók számára árlista](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) nem támogatott. És [árlista a vállalati ügyfelek által adott számlázási időszakban regisztrációs](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) nem támogatott. A megadott regisztrációs és számlázási időszak a alkalmazni arány felbontáson is biztosítanak. Ehelyett használhat **árlista-profillal számlázási**. Az árlista a regisztrációs hatókörben érhető el a számlázási profil árlista. Az API meghívása felhasználó szükséges engedélyek.

## <a name="see-also"></a>Lásd még
- Tudjon meg többet [Azure Consumption REST API-k](/rest/api/consumption/).
