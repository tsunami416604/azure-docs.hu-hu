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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594678"
---
<a name="go-live"></a>Go Live
=======

Ez az API elindítja a folyamatot az éles alkalmazások leküldésének. Ez a művelet van általában hosszú ideig futó. Ez a hívás használja az értesítési e-mail lista a [közzététel](./cloud-partner-portal-api-publish-offer.md) API-művelet.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Közzétevő-azonosító lekéréséhez, például az ajánlat `contoso`       |  String       |
| offerId        | Ajánlat azonosítója, az ajánlat lekéréséhez                                   |  String       |
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

| **Kód** |  **Leírás**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – A kérelem sikeresen elfogadva. A válasz tartalmazza azt a helyet, a művelet állapotának nyomon követését. |
|  400     | `Bad/Malformed request` – A válasz törzsében található további információ a hibáról. |
|  404     |  `Not found` – A megadott entitás nem létezik.                                       |
|  |  |
