---
title: API-szempontok | Azure Piactér
description: Verziószámozási, hibakezelési és engedélyezési problémák a piactér API-k használatakor.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256331"
---
# <a name="api-considerations"></a>API-szempontok

<a name="api-versioning"></a>API-verziószámozás
--------------

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

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
