---
title: Ajánlat közzététele | Azure Piactér
description: API-t a megadott ajánlat közzétételéhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280524"
---
<a name="publish-an-offer"></a>Ajánlat közzététele
================

Elindítja a megadott ajánlat közzétételi folyamatát. Ez a hívás egy hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Közzétevő azonosítója, például`contoso`      |   Sztring       |
|  offerId       | Ajánlat azonosítója                                 |   Sztring       |
|  api-verzió   | Az API legújabb verziója                        |   Dátum         |
|  |  |


<a name="header"></a>Fejléc
------

|  **Név**        |    **Érték**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Engedélyezés   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Példa törzsre
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

### <a name="request-body-properties"></a>Törzstulajdonságainak kérése

|  **Név**               |   **Leírás**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  értesítési e-mailek    | A közzétételi művelet előrehaladásáról értesítendő e-mail címek vesszővel tagolt listája. |
|  |  |


### <a name="response"></a>Válasz

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Válaszfejléc

|  **Név**             |    **Érték**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Művelet-hely    | A művelet aktuális állapotának meghatározásához lekérdezhető URL-cím.    |
|  |  |


### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód** |  **Leírás**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- A kérés sikeresen elfogadásra került. A válasz tartalmaz egy helyet, amely az elindított művelet nyomon követésére használható. |
| 400   | `Bad/Malformed request`- A hibaelhárítási testület további információkat nyújthat.                                                               |
| 422   | `Un-processable entity`- Azt jelzi, hogy a közzétenni kívánt entitás nem sikerült az érvényesítés.                                                        |
| 404   | `Not found`- Az ügyfél által megadott entitás nem létezik.                                                                              |
|  |  |
