---
title: Az Azure API verem használható |} Microsoft Docs
description: Ismerje meg, hogyan lehet lekérni a hitelesítés az Azure verem API-kérések végrehajtásához Azure-ból.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e8a9489a3f487a45303bac45f805381b41427b4b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Az Azure verem API használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem alkalmazásprogramozási felületet (API) segítségével automatizálhatja a műveletek, például a Piactéri elemek syndicating.

Az API megköveteli, hogy a Microsoft Azure bejelentkezési végpont hitelesítése az ügyfél. A végpont egy jogkivonatot használja az Azure-verem API-nak küldött kérelmek fejlécében adja vissza. A Microsoft Azure által használt Oauth 2.0-s.

Ez a cikk ismerteti, amelyek például a **cURL** segédprogram Azure verem kérelmek létrehozásához. Az alkalmazásra, a cURL, az adatok átvitele a könyvtárnak a parancssori eszköz. Ezek a példák ismerteti a folyamatot, amely az Azure-verem API eléréséhez jogkivonat beolvasása. A legtöbb programozási nyelveket adja meg az Oauth 2.0 szalagtárak, amelyeknek robusztus token felügyeleti és leíró feladatok ilyen a jogkivonat frissítését.

Tekintse át az Azure verem REST API használatával egy általános REST-ügyféllel, mint például a teljes folyamat **cURL**, segít megérteni az alapul szolgáló kér, és bemutatja, mi várható válasz payloadban fogadására.

Ez a cikk nem megismerkedhet a jogkivonatokat, például az interaktív bejelentkezési beolvasása, vagy hozzon létre dedikált Alkalmazásazonosítók elérhető lehetőségekről. Ezek a témakörök kapcsolatos információért lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>A jogkivonat beszerzése az Azure-ból

Hozzon létre egy kérelemtörzset formázva a tartalomtípus x-www-form-urlencoded egy hozzáférési jogkivonat beszerzése. A kérés a Azure REST-hitelesítés és bejelentkezés végpontra utáni.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**A bérlői azonosító** vagy:

 - A bérlő tartománya, mint `fabrikam.onmicrosoft.com`
 - A bérlő azonosítója, mint `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Alapértelmezett érték a bérlő független kulcsok: `common`

### <a name="post-body"></a>POST törzse

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Értékek:

 - **grant_type**  
    Hitelesítési séma használatával fogja típusa. Ebben a példában a értéke `password`

 - **resource**  
    Az erőforrás fér hozzá a jogkivonatot. Az Azure-verem felügyeleti metaadat-végpontjához lekérdezésével erőforrás is található. Tekintse meg a **célközönség** szakasz

 - **Az Azure verem felügyeleti végpont**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Ha a rendszergazda megpróbál hozzáférni a bérlői API-hoz majd meg kell győződnie arról tenant végpont, például használatára: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Például az az Azure verem szoftverfejlesztői készlet végpontjaként:

    ```bash
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

  **Felhasználónév**

  Ha például az Azure verem AAD-fiókba:

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

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Válasz:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL-struktúra és a lekérdezés szintaxisa

Általános kérés URI, áll: {URI-séma} :// {URI-állomás} / {erőforrás-elérési út}? {lekérdezés-karakterlánc}

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
