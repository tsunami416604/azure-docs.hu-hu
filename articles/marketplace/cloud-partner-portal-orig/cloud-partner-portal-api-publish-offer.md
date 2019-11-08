---
title: Ajánlat közzététele | Azure piactér
description: API a megadott ajánlat közzétételéhez.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819645"
---
<a name="publish-an-offer"></a>Ajánlat közzététele
================

Elindítja a közzétételi folyamatot a megadott ajánlathoz. Ez a hívás hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Közzétevő azonosítója, például `contoso`      |   Sztring       |
|  OfferId       | Ajánlat azonosítója                                 |   Sztring       |
|  API-Version   | Az API legújabb verziója                        |   Dátum         |
|  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**        |    **Érték**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Engedélyezés   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="request"></a>Kérés

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Kérelem törzsének tulajdonságai

|  **Name (Név)**               |   **Leírás**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  értesítés – e-mailek    | Az e-mail-címek vesszővel tagolt listája, amelyekről értesíteni kell a közzétételi művelet állapotát. |
|  |  |


### <a name="response"></a>Válasz

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |    **Érték**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Művelet – hely    | A művelet aktuális állapotának meghatározására szolgáló URL-cím.    |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |  **Leírás**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – a kérés elfogadása sikeres volt. A válasz olyan helyet tartalmaz, amely az elindított művelet nyomon követésére használható. |
| 400   | `Bad/Malformed request` – a hiba-válasz törzse további információkat is biztosíthat.                                                               |
| 422   | `Un-processable entity` – azt jelzi, hogy a közzétenni kívánt entitás ellenőrzése sikertelen volt.                                                        |
| 404   | `Not found` – az ügyfél által megadott entitás nem létezik.                                                                              |
|  |  |
