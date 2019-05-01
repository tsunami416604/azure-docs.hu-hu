---
title: Ajánlat közzétételének |} Az Azure Marketplace-en
description: API-t az adott ajánlat közzététele.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934979"
---
<a name="publish-an-offer"></a>Ajánlat közzététele
================

A megadott ajánlat a közzétételi folyamat elindul. Ez a hívás egy olyan hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Például a közzétevő azonosítója `contoso`      |   String       |
|  ajánlatazonosító       | Ajánlat azonosítója                                 |   String       |
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
