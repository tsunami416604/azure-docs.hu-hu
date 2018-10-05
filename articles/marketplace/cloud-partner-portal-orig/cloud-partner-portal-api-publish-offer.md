---
title: Ajánlat közzétételének |} A Microsoft Docs
description: API-t az adott ajánlat közzététele.
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
ms.openlocfilehash: cb1293a771a137f4df7e36a2b412f68b384f16ef
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810947"
---
<a name="publish-an-offer"></a>Ajánlat közzététele
================

A megadott ajánlat a közzétételi folyamat elindul. Ez a hívás egy olyan hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Például a közzétevő azonosítója `contoso`      |   Sztring       |
|  OfferId       | Ajánlat azonosítója                                 |   Sztring       |
|  API-verzió   | Az API legújabb verziója                        |   Dátum         |
|  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**        |    **Érték**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Engedélyezés   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Törzs példa
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

### <a name="request-body-properties"></a>Kérelem törzse tulajdonságai

|  **Name (Név)**               |   **Leírás**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  Értesítés-e-mailek    | A közzétételi művelet előrehaladását értesítést e-mail-címeket vesszővel tagolt listája. |
|  |  |


### <a name="response"></a>Válasz

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |    **Érték**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| A művelet-helye    | URL-címe, hogy lekérdezhetők legyenek a művelet aktuális állapotának meghatározásához.    |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód** |  **Leírás**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – A kérelem sikeresen elfogadva. A válasz tartalmaz olyan helyre, amely segítségével nyomon követheti, amely elindítja a műveletet. |
| 400   | `Bad/Malformed request` -A hiba-válasz törzsében több információt tartalmaznak.                                                               |
| 422   | `Un-processable entity` – Azt jelzi, hogy közzé kell tenni az entitás sikertelen ellenőrzés.                                                        |
| 404   | `Not found` – Az ügyfél által megadott entitás nem létezik.                                                                              |
|  |  |
