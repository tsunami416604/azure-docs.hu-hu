---
title: API megfontolások |} A Microsoft Docs
description: Verziókezelés, hibakezelési és engedélyezési problémák esetén a Marketplace-en API-k használatával.
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
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c6cfb41cb6254145821ab3fef662e9a5e54f6298
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810659"
---
<a name="api-considerations"></a>API kapcsolatos szempontok
=================

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
