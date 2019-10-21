---
title: A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegének nyomon követése
description: Megtudhatja, hogyan ellenőrizheti a Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegét.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375532"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegének nyomon követése

A Microsoft-ügyfélszerződés számlázási fiókjához tartozó Azure-kreditek egyenlegét az Azure Portalon ellenőrizheti. 

A krediteket az általuk fedezett díjak kifizetésére használhatja. Díjat számítunk fel, ha olyan terméket használ, amelyet nem fedeznek a kreditek, vagy ha a használata meghaladja a kreditegyenlegét. További információért lásd [az Azure-kreditek által nem fedezett termékeket](#products-that-arent-covered-by-azure-credits).

A Microsoft-ügyfélszerződéshez tartozó számlázási fiókok esetében a kreditek egy számlázási profilhoz vannak hozzárendelve. Mindegyik számlázási profil saját kreditekkel rendelkezik. Egy számlázási profil Azure-kreditegyenlegének megtekintéséhez tulajdonosi, közreműködői, olvasói, illetve számlakezelői szerepkörrel kell rendelkeznie a számlázási profilra, illetve tulajdonosi, közreműködői vagy olvasói szerepkörrel a számlázási fiókra vonatkozóan. Ha többet szeretne megtudni a szerepkörökről, tekintse meg[A Microsoft-ügyfélszerződéshez tartozó felügyeleti szerepkörök ismertetése az Azure-ban](billing-understand-mca-roles.md) című szakaszt.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Kreditegyenleg ellenőrzése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra]( https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  A bal oldalon válassza az **Azure-kredit** lehetőséget. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók vagy számlázási profil kiválasztása után tudja kiválasztani az **Azure-kredit** lehetőséget.

4. Az Azure-kredit lap az alábbi információkat jeleníti meg:

   ![Képernyőkép a számlázási profil kreditegyenlegéről és tranzakcióiról](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Időtartam               | Meghatározás                           |
   |--------------------|--------------------------------------------------------|
   | Becsült egyenleg  | A meglévő kreditek becsült mennyisége az összes számlázott és függőben lévő tranzakció figyelembevételével |
   | Aktuális egyenleg    | A kreditek mennyisége a legutóbbi számla óta. Ez nem tartalmazza a függő tranzakciókat |
   | Tranzakciók       | Az Azure-kredit egyenlegét befolyásoló számlázási tranzakciók |

   Ha a becsült egyenleg 0-ra csökken, a teljes használatért fizetnie kell, beleértve a kreditek által fedezett termékeket is.

6. A számlázási profilhoz tartozó kreditek listájának megtekintéséhez válassza a **Kreditek listája** lehetőséget. A kreditek listája az alábbi információkat jeleníti meg:

   ![Képernyőkép a számlázási profilhoz tartozó kreditek listájáról](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Időtartam | Meghatározás |
   |---|---|
   | Forrás | A kredit beszerzésének forrása |
   | Kezdő dátum | A kredit beszerzésének dátuma |
   | Lejárati dátum | A kredit lejáratának dátuma |
   | Aktuális egyenleg | A legutóbbi számla egyenlege |
   | Eredeti mennyiség | A kredit eredeti mennyisége |
   | status | A kredit aktuális állapota. Az Állapot lehet aktív, felhasznált, lejárt vagy lejáró |

## <a name="check-your-credit-balance-programmatically"></a>A kreditegyenleg programozott módon történő ellenőrzése

Használhatja az [Azure számlázási](https://docs.microsoft.com/rest/api/billing/) vagy [használati](https://docs.microsoft.com/rest/api/consumption/) API-jait a számlázási fiókhoz tartozó kreditegyenleg programozott módon való lekérdezéséhez.

Az alábbi példák REST API-kat használnak. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-billing-profiles-you-have-access-to"></a>Olyan számlázási profilok keresése, amelyekhez hozzáférése van

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
Az API-válasz visszaadja a számlázási fiókok és az azokhoz tartozó számlázási profilok listáját.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

A számlázási profil `displayName` tulajdonságával azonosíthatja azt a számlázási profilt, amelynél ellenőrizni szeretné a kreditegyenleget. Másolja a számlázási profil `id` elemét. Például ha a **Fejlesztés** számlázási profiljának kreditegyenlegét szeretné ellenőrizni, másolja a ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx``` értéket. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="get-azure-credit-balance"></a>Azure-kreditegyenleg lekérdezése 

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingProfileId>` értéket az előző lépésben kimásolt `id` értékével (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Az API-válasz visszaadja a számlázási profil becsült és aktuális kreditegyenlegét.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Elem neve  | Leírás                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | A meglévő kreditek becsült mennyisége az összes számlázott és függőben lévő tranzakció figyelembevételével. |
| `currentBalance`   | A kreditek mennyisége a legutóbbi számla óta. Ez nem tartalmazza a függő tranzakciókat.    |
| `pendingCreditAdjustments`      | A visszatérítésekhez hasonló módosító tényezők, amelyek még nincsenek számlázva.  |
| `expiredCredit`      |  Az utolsó számla óta lejárt kreditek.  |
| `pendingEligibleCharges`  | Azok a kreditre jogosult díjak, amelyek még nincsenek számlázva.   |

### <a name="get-list-of-credits"></a>Kreditek lekérdezése

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingProfileId>` értéket az előző lépésben kimásolt `id` értékével (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Az API-válasz visszaadja a számlázási profilhoz tartozó Azure-kreditek listáját.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Elem neve  | Leírás                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | A kredit eredeti mennyisége. |
| `closedBalance`   | A legutóbbi számla egyenlege.    |
| `source`      | A forrás, amely meghatározza, hogy ki hogyan tett szert a kreditre. |
| `startDate`      |  A kredit aktiválásának dátuma.  |
| `expirationDate`  | A kredit lejáratának dátuma.   |
| `poNumber`  | Annak a számlának a rendelési száma, amelyen a kredit számlázva lett.   |

### <a name="get-transactions-that-affected-credit-balance"></a>A kreditegyenleget befolyásoló tranzakciók lekérdezése

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingProfileId>` értéket az előző lépésben kimásolt `id` értékével (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Meg kell adnia egy **startDate** értéket és egy **endDate** értéket, hogy megkapja a kért időszak tranzakcióit.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Az API-válasz minden olyan tranzakciót visszaad, amely befolyásolta a számlázási profilhoz tartozó kreditegyenleget.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Elem neve  | Leírás                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | A tranzakció végbemenetelének dátuma. |
| `description` | A tranzakció leírása. |
| `adjustments`   | A tranzakcióra vonatkozó kreditmódosítások.    |
| `creditExpired`      | A lejárt kreditek összege. |
| `charges`      |  A tranzakció díjai.  |
| `closedBalance`  | A tranzakciót követő egyenleg.   |
| `eventType`  | A tranzakció típusa.   |
| `invoiceNumber`  | Annak a számlának a száma, amelyen a tranzakció számlázva lett. Függőben lévő tranzakció esetében üres lesz.   |

## <a name="how-credits-are-used"></a>A kreditek felhasználása

A Microsoft-ügyfélszerződésekhez tartozó számlázási fiókokban a számlázási profilokkal kezelheti a számlákat és a fizetési módokat. A rendszer havonta létrehoz egy számlát minden számlázási profil számára, a fizetési módok használatával pedig kifizetheti a számlát.

a megkeresett krediteket egy számlázási profilhoz rendeli. A számla számlázási profil számára történő létrehozásakor a rendszer automatikusan alkalmazza a krediteket az összköltségre a fizetendő összeg kiszámításához. A fennmaradó összeget egy fizetési mód segítségével, például csekkel/átutalással vagy bankkártyával fizetheti ki.

## <a name="products-that-arent-covered-by-azure-credits"></a>Az Azure-kreditek által nem fedezett termékek

 Az alábbi termékeket nem fedezi az Azure-kredit. E termékek használatáért a kreditegyenlegétől függetlenül fizetnie kell:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (havi)
- Visual Studio Enterprise (éves)
- Visual Studio Professional (havi)
- Visual Studio Professional (éves)
- Azure Marketplace-termékek
- Azure-támogatás ügyfeleknek

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [A Microsoft-ügyfélszerződéshez tartozó számlázási fiók ismertetése](billing-mca-overview.md)
- [A Microsoft-ügyfélszerződéshez tartozó számlán szereplő kifejezések értelmezése](billing-mca-understand-your-invoice.md)
