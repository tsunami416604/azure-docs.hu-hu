---
title: API-szempontok | Azure Piactér
description: Verziószámozási, hibakezelési és engedélyezési problémák a piactér API-k használatakor.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4e04f521ed2023dfb9cd562549cb2e1bcd319b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288631"
---
# <a name="api-considerations"></a>API-szempontok


<a name="api-versioning"></a>API-verziószámozás
--------------

Előfordulhat, hogy az API-nak több verziója is elérhető egyszerre. Az ügyfeleknek meg kell adniuk, hogy `api-version` melyik verziót kívánják meghívni a lekérdezési karakterlánc részeként.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Az ismeretlen vagy érvénytelen API-verzióval rendelkező kérelemre adott válasz egy 400-as HTTP-kód. Ez a hiba az ismert API-verziók gyűjteményét adja vissza a választörzsben.

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

Az API válaszol a megfelelő HTTP-állapotkódokat és adott esetben további információkat a válasz ban szerializált JSON.
Ha hibaüzenetet, különösen 400-as osztályú hibát kap, ne próbálkozzon újra a kéréssel a kiváltó ok kijavítása előtt. Például a fenti mintaválaszban javítsa ki az API-verzió paramétert a kérelem újraküldése előtt.

<a name="authorization-header"></a>Engedélyezési fejléc
--------------------

Az összes API-k ebben a hivatkozásban, át kell adnia az engedélyezési fejléc et az Azure Active Directoryból (Azure AD) kapott tulajdonosi jogkivonattal együtt. Ez a fejléc szükséges ahhoz, hogy érvényes választ kapjunk; ha nincs jelen, a `401 Unauthorized` rendszer hibát ad vissza. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
