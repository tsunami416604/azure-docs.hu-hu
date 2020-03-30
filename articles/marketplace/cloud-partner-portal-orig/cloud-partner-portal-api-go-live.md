---
title: Élő adás | Azure Piactér
description: A Go Live API elindítja az ajánlat élő tőzsdei eljárását.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288580"
---
<a name="go-live"></a>Élő adás
=======

Ez az API elindítja az alkalmazás éles környezetbe való lenyomásának folyamatát. Ez a művelet általában hosszú ideig fut. Ez a hívás az [API-közzétételi](./cloud-partner-portal-api-publish-offer.md) műveletértesítési e-mail listáját használja.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | A lekérni ajánlott közzétevői azonosítója, például`contoso`       |  Sztring       |
| offerId        | A lekérni ajánlott ajánlat azonosítója                                   |  Sztring       |
| api-verzió    | Az API legújabb verziója                                                   |  Dátum         |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Név**       |     **Érték**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Engedélyezés   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Példa törzsre
------------

### <a name="response"></a>Válasz

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Válaszfejléc

|  **Név**             |      **Érték**                                                            |
|  --------             |      ----------                                                           |
| Művelet-hely    |  A művelet aktuális állapotának meghatározásához lekérdező URL-cím            |
|  |  |


### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód** |  **Leírás**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- A kérés sikeresen elfogadásra került. A válasz tartalmaz egy helyet a művelet állapotának nyomon követéséhez. |
|  400     | `Bad/Malformed request`- További hibainformáció található a válaszszervezetben. |
|  404     |  `Not found`- A megadott entitás nem létezik.                                       |
|  |  |
