---
title: Egyéni tokengyorsítótár-szerializálás (MSAL4j)
titleSuffix: Microsoft identity platform
description: További információ az MSAL Java-hoz tokengyorsítótárának szerializálásáról
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696163"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Egyéni tokengyorsítótár-szerializálás az MSAL for Java-ban

A jogkivonat-gyorsítótár az alkalmazás példányai közötti megőrzése érdekében testre kell szabnia a szerializálást. A tokengyorsítótár-szerializálásban részt vevő Java-osztályok és felületek a következők:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): A biztonsági tokengyorsítótárat képviselő felület.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): A kód végrehajtása a hozzáférés előtti és utáni műveletet képviselő felület. @Override A *cacheaccess* és az *afterCacheAccess* előtt a gyorsítótár szerializálásáért és deszerializálásáért felelős logikával kell elférség előtt.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Olyan környezetet jelölő felület, amelyben a tokengyorsítótár elérhető. 

Az alábbiakban a tokengyorsítótár-szerializálás/deszerializálás egyéni szerializálásának naiv implementációja látható. Ne másolja és illessze be éles környezetbe.

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

További információ a [fiókok beolvasásáról és eltávolításáról a tokengyorsítótárból az MSAL for Java használatával.](msal-java-get-remove-accounts-token-cache.md)
