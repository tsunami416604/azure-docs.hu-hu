---
title: Megadott ajánlati API beolvasása | Azure piactér
description: Az API lekérdezi a megadott ajánlatot a közzétevő névterén belül.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 030fb221b9227acf9c5dcda8797b106e51f56d64
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827345"
---
<a name="retrieve-a-specific-offer"></a>Adott ajánlat beolvasása
=========================

Lekéri a megadott ajánlatot a közzétevő névterén belül.  

Lekérheti az ajánlat egy adott verzióját is, vagy lekérheti az ajánlatot Piszkozat, megtekintés vagy éles tárolóhelyek használatával. Ha nincs megadva tárolóhely, az alapértelmezett érték `draft`. A nem előzetesen megtekintett vagy közzétett ajánlat beolvasására tett kísérlet `404 Not Found` hibát eredményez.

> [!WARNING]
> Ez az API nem kéri le a titkos típusok mezőinek titkos értékeit.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-paraméterek
--------------


| **Name (Név)**    | **Leírás**                                                                          | **Adattípus** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Például: contoso                                                        | Sztring        |
| OfferId     | Az ajánlatot egyedileg azonosító GUID.                                                 | Sztring        |
| version     | A beolvasott ajánlat verziója. Alapértelmezés szerint a rendszer beolvassa a legújabb ajánlat verzióját. | Egész szám       |
| SlotId      | Az a tárolóhely, amelyből az ajánlatot le kell kérni, az alábbiak egyike lehet:      <br/>  - `Draft` (alapértelmezett) – jelenleg a piszkozatban lekéri az ajánlat verzióját.  <br/>  a -  `Preview` jelenleg előzetes verzióban kérdezi le az ajánlat verzióját.     <br/>  -  `Production` az aktuálisan éles üzemben lévő ajánlat verzióját kérdezi le.          |      Enum |
| API-Version | Az API legújabb verziója                                                                    | Dátum          |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**          |   **Érték**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Engedélyezés     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
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


### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Name (Név)**       |   **Leírás**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Az ajánlat típusát azonosítja.                                                                                                    |
|  publisherId    | A közzétevő egyedi azonosítója                                                                                              |
|  status         | Az ajánlat állapota. A lehetséges értékek listájáért lásd az [ajánlat állapota](#offer-status) alább.                                  |
|  Azonosító             | Az ajánlatot egyedileg azonosító GUID                                                                                         |
|  version        | Az ajánlat aktuális verziója. Az ügyfél nem tudja módosítani a Version tulajdonságot. Minden közzététel után növekszik.    |
|  Definition     | A munkaterhelés tényleges definíciója                                                                                               |
|  changedTime    | Az ajánlat utolsó módosításának UTC szerinti dátuma                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  | **Leírás**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` – a kérés feldolgozása sikeres volt, és a közzétevő alatt lévő összes ajánlat vissza lett küldve az ügyfélnek.               |
|  400      | `Bad/Malformed request` – a hiba-válasz törzse több információt is tartalmazhat.                                                 |
|  403      | `Forbidden` – az ügyfél nem fér hozzá a megadott névtérhez.                                                        |
|  404      | `Not found` – a megadott entitás nem létezik. Az ügyfélnek ellenőriznie kell a publisherId, a offerId és a verziót (ha meg van adva).      |
|  |  |


### <a name="offer-status"></a>Ajánlat állapota

|  **Name (Név)**                   |   **Leírás**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Az ajánlatot még soha nem tették közzé.               |
|  NotStarted                 | Az ajánlat új, de nem indult el.              |
|  WaitingForPublisherReview  | Az ajánlat a közzétevő jóváhagyására vár.      |
|  Fut                    | Az ajánlatok beküldése folyamatban van.          |
|  Sikeres                  | Az ajánlat küldése befejeződött a feldolgozás során.    |
|  Megszakítva                   | Az ajánlat beküldését megszakították.                |
|  Meghiúsult                     | Az ajánlat küldése nem sikerült.                      |
|  |  |
