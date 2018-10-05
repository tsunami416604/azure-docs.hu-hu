---
title: Egy adott ajánlat API beolvasása |} A Microsoft Docs
description: API lekérdezi a megadott ajánlatot a kiadó névtéren belül.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9484cf0f549db94be8f1ac2363addca952a3cff3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810772"
---
<a name="retrieve-a-specific-offer"></a>Egy adott ajánlat beolvasása
=========================

Lekéri a megadott ajánlatot a kiadó névtéren belül.  

Is lekérni egy adott verzióját az ajánlatot, vagy a draft, nézetben vagy üzemelési pont felcserélése ajánlat beolvasása. Ha nincs megadva a tárolóhelyet, az alapértelmezett érték `draft`. Az ajánlat nem kinyomtatnak vagy közzétett beolvasására tett kísérlet eredményez olyan `404 Not Found` hiba.

> [!WARNING]
> Ez az API el fognak veszni a titkos titkos típusú mezők értékeit.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-paraméterek
--------------


| **Name (Név)**    | **Leírás**                                                                          | **Adattípus** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Például Contoso                                                        | Sztring        |
| OfferId     | GUID azonosítója, amely egyedileg azonosítja az ajánlatot.                                                 | Sztring        |
| version     | Az ajánlat lekérése folyamatban verziója. Alapértelmezés szerint a rendszer lekéri a legújabb ajánlat. | Egész szám       |
| Helyet      | A tárolóhely, amelyről a az ajánlatot, hogy a rendszer lekéri a egyike lehet:      <br/>  - `Draft` (alapértelmezett) az ajánlat verzió jelenleg a draft kérdezi le.  <br/>  -  `Preview` az ajánlat verzió jelenleg még előzetes verzióként kérdezi le.     <br/>  -  `Production` olvassa be az ajánlat verziója jelenleg éles környezetben.          |      Enum |
| API-verzió | API legújabb verziója                                                                    | Dátum          |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**          |   **Érték**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Engedélyezés     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Törzs példa
------------

### <a name="response"></a>Válasz

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>Válasz törzsében tulajdonságai

|  **Name (Név)**       |   **Leírás**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Az ajánlat típusát jelöli                                                                                                    |
|  publisherId    | A kiadó egyedi azonosítója                                                                                              |
|  status         | Az ajánlat állapotát. A lehetséges értékek listáját lásd: [stav Nabídky](#offer-status) alatt.                                  |
|  Azonosító             | GUID, amely egyedileg azonosítja az ajánlatot                                                                                         |
|  version        | Az ajánlat jelenlegi verziójával. A verzió tulajdonság nem lehet módosítani az ügyfél által. Azt minden egyes közzététel után.    |
|  definíció     | A számítási feladatok tényleges meghatározása                                                                                               |
|  changedTime    | Az ajánlat történt utolsó módosításának UTC dátum-idő                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód**  | **Leírás**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -A kérés feldolgozása sikeresen megtörtént, és a közzétevő alapján az ajánlatok az ügyfélnek visszaadott.               |
|  400      | `Bad/Malformed request` – A hiba választörzs tartalmazhat további információkat.                                                 |
|  403      | `Forbidden` – Az ügyfél nem rendelkezik hozzáféréssel a megadott névtér.                                                        |
|  404      | `Not found` – A megadott entitás nem létezik. Ügyfelet, hogy ellenőrizze a publisherId, offerId és verziója (ha az meg van adva).      |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Name (Név)**                   |   **Leírás**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Az ajánlat soha nem lett közzétéve.               |
|  Nincs elindítva                 | Az ajánlat új, de nem indult el.              |
|  WaitingForPublisherReview  | Ajánlat közzétevője jóváhagyásra vár.      |
|  Fut                    | Az ajánlat küldésének feldolgozása folyamatban van.          |
|  Sikeres                  | Az ajánlat küldésének feldolgozása befejeződött.    |
|  Megszakítva                   | Az ajánlat küldésének meg lett szakítva.                |
|  Meghiúsult                     | Az ajánlat elküldés meghiúsult.                      |
|  |  |
