---
title: Az Azure API verem használható |} Microsoft Docs
description: Ismerje meg, hogyan lehet lekérni a hitelesítés az Azure verem API-kérések végrehajtásához Azure-ból.
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 3523f86791a3bf437cbd21ba4df5afc0cd1955fd
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
<!--  cblackuk and charliejllewellyn -->

# <a name="use-the-azure-stack-api"></a>Az Azure verem API használata

Az Azure-verem API segítségével automatizálhatja a műveletek, például a Piactéri elemek syndicating.

A folyamat megköveteli, hogy az ügyfél hitelesítése a Microsoft Azure bejelentkezési végpont ellen. A végpont egy jogkivonatot ad vissza. Adja meg a jogkivonatot az Azure-verem API-nak küldött kérelmek fejlécében. Azure Oauth2.0 használja.

Ez a cikk példákat egyszerű adott Azure verem a curl segédprogrammal. Ez a témakör végigvezeti az Azure-verem API eléréséhez jogkivonat lekérésének folyamatát. A legtöbb nyelveket adja meg tárak Oauth 2.0 token robusztus felügyeletet és a kezelési feladatok ilyen a jogkivonat frissítését.

A teljes folyamatot az Azure verem REST API használatával általános REST-ügyféllel megnézi például curl azt segítenek megérteni az alapul szolgáló kérelem, és hogy mire számíthat a válasz tartalomban fogadására.

Ez a cikk nem megismerkedhet a jogkivonatokat, például az interaktív bejelentkezési beolvasása, vagy hozzon létre dedikált Alkalmazásazonosítók elérhető lehetőségekről. További információkért lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>A jogkivonat beszerzése az Azure-ból

Hozzon létre egy kérést *törzs* formázva a tartalomtípus x-www-form-urlencoded egy hozzáférési jogkivonat beszerzése. A kérés a Azure REST-hitelesítés és bejelentkezés végpontra utáni.
```
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**A bérlői azonosító** vagy:

- Your tenant domain, such as fabrikam.onmicrosoft.com
- A bérlő azonosítója, például a 8eaed023-2b34-4da1-9baa-8bc8c9d6a491
- Alapértelmezett érték a bérlő független kulcsok: gyakori

### <a name="post-body"></a>POST törzse
```
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Értékek:

  **grant_type**
  
  Hitelesítési séma használatával fogja típusa. Ebben a példában a érték:
  ```
  password
  ```

  **resource**

  Az erőforrás fér hozzá a jogkivonatot. Az Azure-verem felügyeleti metaadat-végpontjához lekérdezésével erőforrás is található. Tekintse meg a **célközönség** szakasz

  Az Azure verem felügyeleti végpontja:
  ```
  https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
  ```
 > [!NOTE]  
 > Ha megpróbál hozzáférni a bérlői API rendszergazdája, akkor meg kell győződnie arról, használja a tenant végpont, például a "https://adminmanagement.{region}.{Azure verem tartományi} / metaadatok/végpontok? api-version = 2015-01-011
  
  Például az Azure verem szoftverfejlesztői készlet:
  ```
  curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
  ```
 
  Válasz:
  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>Példa
  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Ez az érték alapértelmezés szerint szoftveresen kötött:
  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternatív lehetőségek állnak rendelkezésre az adott forgatókönyveket:
  
  | Alkalmazás | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**
  
  Az Azure verem AAD-fiók, például:
  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Jelszó**

  A verem AAD Azure rendszergazdai jelszavát.
  
### <a name="example"></a>Példa

A kérelem:
```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Válasz:
```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API-lekérdezések

Ha a hozzáférési jogkivonat beszerzéséhez kell célútvonallal fejléc az egyes az API-kérelmek. A fejléc létrehozásához szükséges ahhoz, hogy ehhez **engedélyezési** értékű: `Bearer <access token>`. Példa:

A kérelem:
```
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Válasz:
```
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL-struktúra és a lekérdezés szintaxisa

Általános kérés URI, áll: {URI-séma} :// {URI-állomás} / {erőforrás-elérési út}? {query-string}

- **URI-séma**:  
Az URI azt jelzi, hogy a kérelem küldéséhez használt protokoll. Például `http` vagy `https`.
- **URI-állomás**:  
Az állomás tartománynév vagy IP-cím, amelyen a többi végpont található, mint például a kiszolgáló `graph.microsoft.com` vagy `adminmanagement.local.azurestack.external`.
- **Erőforrás elérési útja**:  
Az elérési utat határozza meg, az erőforrás vagy az erőforrás-gyűjteményt, amely több szegmens meghatározásának erőforrások kiválasztása a szolgáltatás által használt is. Például: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` lekérdezni a lista az alkalmazások gyűjteményen belül egy adott alkalmazás tulajdonosok használható.
- **Lekérdezési karakterlánc**:  
A karakterlánc további egyszerű paraméterek, például az API verziója vagy az erőforrás-kiválasztási feltételek biztosít.

## <a name="azure-stack-request-uri-construct"></a>Az Azure verem kérelem URI-szerkezet
```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version} 
```

### <a name="uri-syntax"></a>URI-szintaxis
```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version} 
```

### <a name="query-uri-example"></a>Lekérdezés URI – példa
```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>További lépések

Az Azure RESTful végpontok használatával kapcsolatos további információkért lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/).
