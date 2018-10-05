---
title: Az élő esemény indításra |} A Microsoft Docs
description: A Go Live API elindítja az ajánlat élő folyamat listázása.
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
ms.openlocfilehash: c7d643f0c7885e64636a107d22ce332b1ba9371c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809712"
---
<a name="go-live"></a>Go Live
=======

Ez az API elindítja a folyamatot az éles alkalmazások leküldésének. Ez a művelet van általában hosszú ideig futó. Ez a hívás használja az értesítési e-mail lista a [közzététel](./cloud-partner-portal-api-publish-offer.md) API-művelet.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Közzétevő-azonosító lekéréséhez, például az ajánlat `contoso`       |  Sztring       |
| OfferId        | Ajánlat azonosítója, az ajánlat lekéréséhez                                   |  Sztring       |
| API-verzió    | Az API legújabb verziója                                                   |  Dátum         |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**       |     **Érték**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Engedélyezés   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Törzs példa
------------

### <a name="response"></a>Válasz

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |      **Érték**                                                            |
|  --------             |      ----------                                                           |
| A művelet-helye    |  A művelet a jelenlegi állapotának meghatározása lekérdezési URL-címe            |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód** |  ** Leírás **                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – A kérelem sikeresen elfogadva. A válasz tartalmazza azt a helyet, a művelet állapotának nyomon követését. |
|  400     | `Bad/Malformed request` – A válasz törzsében található további információ a hibáról. |
|  404     |  `Not found` – A megadott entitás nem létezik.                                       |
|  |  |
