---
title: API-megfontolások | Azure piactér
description: Verziószámozás, hibakezelés és engedélyezési problémák a Marketplace API-k használatakor.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819729"
---
# <a name="api-considerations"></a>API-megfontolások


<a name="api-versioning"></a>API-verziószámozás
--------------

Előfordulhat, hogy az API több verziója is elérhető. Az ügyfeleknek jelezniük kell, hogy melyik verziót szeretnék használni, ha a lekérdezési karakterlánc részeként megadja a `api-version` paramétert.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Az ismeretlen vagy érvénytelen API-verzióra irányuló kérelemre adott válasz egy 400-es HTTP-kód. Ez a hiba a válasz törzsében lévő ismert API-verziók gyűjteményét adja vissza.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Hibák
------

Az API a hibákat a megfelelő HTTP-állapotkódok és opcionálisan a JSON-ként szerializált válaszban található további információk alapján válaszolja meg.
Ha hibaüzenetet kap, különösen a 400 osztályú hibát, ne próbálja megismételni a kérést az alapul szolgáló ok kijavítása előtt. A fenti mintában például javítsa ki az API Version paramétert a kérelem újraküldése előtt.

<a name="authorization-header"></a>Engedélyezési fejléc
--------------------

Az ebben a hivatkozásban található összes API esetében át kell adni az engedélyezési fejlécet a Azure Active Directory (Azure AD) által beszerzett tulajdonosi jogkivonattal együtt. Ez a fejléc érvényes válasz fogadásához szükséges. Ha nincs jelen, a rendszer `401 Unauthorized` hibát ad vissza. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
