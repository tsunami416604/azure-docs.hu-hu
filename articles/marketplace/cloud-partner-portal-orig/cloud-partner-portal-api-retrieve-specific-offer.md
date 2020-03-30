---
title: Adott ajánlat lekérése API | Azure Piactér
description: Az API lekéri a megadott ajánlatot a közzétevő névterén belül.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: a83b664bb770a88f3c4c13a672655e736a46ca75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280456"
---
<a name="retrieve-a-specific-offer"></a>Adott ajánlat lekérése
=========================

A megadott ajánlat lekérése a közzétevő névterén belül.  

Az ajánlat egy adott verzióját is lekérheti, vagy lekérheti az ajánlatot vázlat, nézet vagy éles tárolóhelyeken. Ha nincs megadva tárolóhely, `draft`az alapértelmezett érték a . A nem előzetesverzióban vagy közzénem tett ajánlat lekérésével `404 Not Found` hiba fog bekövetkezni.

> [!WARNING]
> Ez az API nem olvassa be a titkos típusmezők titkos értékeit.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-paraméterek
--------------


| **Név**    | **Leírás**                                                                          | **Adattípus** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Például a Contoso                                                        | Sztring        |
| offerId     | Guid, amely egyedileg azonosítja az ajánlatot.                                                 | Sztring        |
| version     | A beolvasott ajánlat verziója. Alapértelmezés szerint a rendszer a legújabb ajánlat verzióját olvassa be. | Egész szám       |
| slotId      | A nyílás, amelyből az ajánlatot le kell kérni, a következők egyike lehet:      <br/>  - `Draft`(alapértelmezett) lekéri a jelenleg vázlatban lévő ajánlatverziót.  <br/>  -  `Preview`lekéri az előzetes verzióban jelenleg elérhető ajánlatverziót.     <br/>  -  `Production`lekéri a jelenleg éles környezetben lévő ajánlatverziót.          |      Enum |
| api-verzió | Az API legújabb verziója                                                                    | Dátum          |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Név**          |   **Érték**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Engedélyezés     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Példa törzsre
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


### <a name="response-body-properties"></a>Választörzs tulajdonságai

|  **Név**       |   **Leírás**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Az ajánlat típusát azonosítja                                                                                                    |
|  publisherId    | A közzétevő egyedi azonosítója                                                                                              |
|  status         | Az ajánlat állapota. A lehetséges értékek listáját lásd alább [Az Ajánlat állapota.](#offer-status)                                  |
|  Azonosító             | Az ajánlatot egyedileg azonosító GUID                                                                                         |
|  version        | Az ajánlat aktuális verziója. A verziótulajdonságot az ügyfél nem tudja módosítani. Minden kiadás után növekszik.    |
|  definíció     | A munkaterhelés tényleges meghatározása                                                                                               |
|  módosítottIdő    | AZ ajánlat utolsó módosításának dátuma                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód**  | **Leírás**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- A kérés feldolgozása sikeresen megtörtént, és a közzétevő alatt lévő összes ajánlat visszakerült az ügyfélhez.               |
|  400      | `Bad/Malformed request`- A hibaválasz törzse további információkat tartalmazhat.                                                 |
|  403      | `Forbidden`- Az ügyfél nem fér hozzá a megadott névtérhez.                                                        |
|  404      | `Not found`- A megadott entitás nem létezik. Az ügyfélnek ellenőriznie kell a publisherAzonosítót, az offerId-et és a verziót (ha meg van adva).      |
|  |  |


### <a name="offer-status"></a>Ajánlat állapota

|  **Név**                   |   **Leírás**                             |
| --------------------------- |  -------------------------------------------- |
|  Soha nem lett közzétéve             | Az ajánlatot soha nem tették közzé.               |
|  Nincs beindítva                 | Az ajánlat új, de nem indult el.              |
|  VárakozásA PublisherReview-ra  | Az ajánlat a közzétevő jóváhagyására vár.      |
|  Fut                    | Az ajánlat beküldése feldolgozása folyamatban van.          |
|  Sikeres                  | Az ajánlat beküldése befejeződött a feldolgozás.    |
|  Megszakítva                   | Az ajánlat beküldését törölték.                |
|  Sikertelen                     | Az ajánlat beküldése nem sikerült.                      |
|  |  |
