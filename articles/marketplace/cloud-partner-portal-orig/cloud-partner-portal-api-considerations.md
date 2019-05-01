---
title: API megfontolások |} Az Azure Marketplace-en
description: Verziókezelés, hibakezelési és engedélyezési problémák esetén a Marketplace-en API-k használatával.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6bf27db27daee50f78552344ae1b2b116d48a5c0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935576"
---
# <a name="api-considerations"></a>API kapcsolatos szempontok


<a name="api-versioning"></a>API-verziószámozás
--------------

Előfordulhat, hogy az API több verziójának érhetők el egyszerre. Ügyfelek történő használatát indítása azáltal, hogy melyik verziót meg kell jelölnie a `api-version` paraméter a lekérdezési karakterlánc részeként.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

A válasz a kérésekre, és a egy ismeretlen vagy érvénytelen API-verzió egy 400-as HTTP-kód. Ez a hiba a válasz törzse ismert API-verziók gyűjteményét adja vissza.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Hibák
------

Hibák a megfelelő HTTP-állapotkódok és szükség esetén további információt a válaszban szerializált JSON-fájlként válaszol az API-t.
Hibaüzenet, különösen egy 400-as szintű hiba, amikor nem próbálja meg újra a kérelmet az alapul szolgáló ok javítása előtt. Ha például a fenti példa válaszban az API-version paramétert újraküldés előtt javítsa a kérelmet.

<a name="authorization-header"></a>Engedélyeztetési fejléc
--------------------

Minden az API-t a jelen útmutatóban akkor meg kell adnia az engedélyezési fejléc tulajdonosi jogkivonattal az Azure Active Directory (Azure AD) származó együtt. Ez a fejléc szükséges kapott érvényes választ; Ha nem található, egy `401 Unauthorized` hibát akkor adja vissza. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
