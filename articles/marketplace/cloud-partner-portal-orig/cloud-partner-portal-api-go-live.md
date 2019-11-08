---
title: Go élő | Azure piactér
description: A Go Live API elindítja az ajánlat élő listázási folyamatát.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819690"
---
<a name="go-live"></a>Élő indítás
=======

Ez az API elindítja az alkalmazás éles környezetben való továbbításának folyamatát. Ez a művelet általában hosszú ideig fut. Ez a hívás az értesítési e-mailek listáját használja a [közzétételi](./cloud-partner-portal-api-publish-offer.md) API-műveletből.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | A lekérdezni kívánt ajánlat közzétevő-azonosítója, például `contoso`       |  Sztring       |
| OfferId        | A lekérdezni kívánt ajánlat azonosítója                                   |  Sztring       |
| API-Version    | Az API legújabb verziója                                                   |  Dátum         |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**       |     **Érték**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Engedélyezés   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="response"></a>Válasz

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |      **Érték**                                                            |
|  --------             |      ----------                                                           |
| Művelet – hely    |  A művelet aktuális állapotának meghatározására szolgáló lekérdezési URL-cím            |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |  **Leírás**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – a kérés elfogadása sikeres volt. A válasz a művelet állapotának nyomon követésére szolgáló helyet tartalmaz. |
|  400     | `Bad/Malformed request` – további hibaüzenetek találhatók a válasz törzsében. |
|  404     |  `Not found` – a megadott entitás nem létezik.                                       |
|  |  |
