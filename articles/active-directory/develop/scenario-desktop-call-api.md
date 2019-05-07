---
title: Asztali alkalmazás, hogy a hívások webes API-k (webes API hívása) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy asztali alkalmazás, amely meghívja a webes API-k (webes API hívása)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075925"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Asztali alkalmazás, amely meghívja a webes API-k – webes API-hívás

Most, hogy megkapta a jogkivonatot, meghívhatja egy védett webes API-t.

## <a name="calling-a-web-api-from-net"></a>Webes API-k hívása a .NET használatával

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Több API-k hívásával - növekményes beleegyezése és a feltételes hozzáférés

Több API-t hívja meg a ugyanahhoz a felhasználóhoz, miután az első API-hoz egy tokent kapott van szüksége, ha csak hívása `AcquireTokenSilent`, és megkapja a jogkivonat a más API-k csendes legtöbb esetben.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Az olyan esetekben, ahol beavatkozásra szükség a következő esetekben:

- A felhasználó hagyta jóvá az első API-hoz, de most van szükség több hatókört (növekményes hozzájárulás) vonatkozó beleegyezés
- Az első API több többtényezős hitelesítés nem szükséges, de a következő nem.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-desktop-production.md)
