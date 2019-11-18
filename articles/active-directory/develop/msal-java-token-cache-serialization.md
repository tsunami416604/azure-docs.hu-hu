---
title: Egyéni jogkivonat-gyorsítótár szerializálás a MSAL-ben Javához
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan szerializálhatja a MSAL for Java jogkivonat-gyorsítótárát
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7274a1b6b5ebf5b55c2cad4b52dfe4c997e26314
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145902"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Egyéni jogkivonat-gyorsítótár szerializálása a MSAL for Java (MSAL4J) szolgáltatásban

Ha meg szeretné őrizni a jogkivonat-gyorsítótárat az alkalmazás példányai között, testre kell szabnia a szerializálást. A jogkivonat-gyorsítótár szerializálásában résztvevő Java-osztályok és-felületek a következők:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): a biztonsági jogkivonat-gyorsítótárat jelképező csatoló.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): a hozzáférés előtt és után a kód végrehajtásának műveletét jelképező illesztő. A *beforeCacheAccess* és a *afterCacheAccess* a gyorsítótár szerializálásához és deszerializálásához felelős logikával kell @Override.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): az interfészt jelképező környezet, amelyben a jogkivonat-gyorsítótár elérhető. 

Az alábbiakban a jogkivonat-gyorsítótár szerializálási/deszerializálása egyéni szerializálásának naiv implementációja látható. Ne másolja és illessze be ezt éles környezetbe.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Részletek

Ismerje meg [, hogyan kérhet le és távolíthat el fiókokat a jogkivonat-gyorsítótárból a MSAL for Java használatával](msal-java-get-remove-accounts-token-cache.md).
