---
title: A fiókok lekérése & a tokengyorsítótárból (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként tekintheti meg és távolíthatja el a fiókokat a tokengyorsítótárból a Microsoft Java hitelesítési könyvtára használatával.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696197"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Fiókok beolvasása és eltávolítása a tokengyorsítótárból az MSAL for Java használatával

Az MSAL for Java alapértelmezés szerint memórián belüli tokengyorsítótárat biztosít. A memórián belüli jogkivonat-gyorsítótár az alkalmazáspéldány időtartama tart.

## <a name="see-which-accounts-are-in-the-cache"></a>A gyorsítótárban lévő fiókok megtekintése

A gyorsítótárban lévő fiókokat a `PublicClientApplication.getAccounts()` következő példában látható módon ellenőrizheti:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Fiókok eltávolítása a gyorsítótárból

Ha el szeretne távolítani egy fiókot a gyorsítótárból, `PublicClientApplicatoin.removeAccount()` keresse meg az eltávolítandó fiókot, majd hívja meg az alábbi példában látható módon:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Részletek

Ha Az MSAL java-hoz, ismerje meg az [egyéni tokengyorsítótár-szerializálást az MSAL for Java alkalmazásban.](msal-java-token-cache-serialization.md)
