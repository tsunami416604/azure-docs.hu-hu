---
title: Fiókok & eltávolítása a jogkivonat-gyorsítótárból (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy miként tekintheti meg és távolíthatja el a fiókokat a jogkivonat-gyorsítótárból a Javához készült Microsoft Authentication Library használatával.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 150708e7d54e456e1a4e35cfe381e5de5d0f1e8c
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85479351"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Fiókok beolvasása és eltávolítása a jogkivonat-gyorsítótárból a MSAL for Java használatával

A Java-MSAL alapértelmezés szerint memóriabeli jogkivonat-gyorsítótárat biztosít. A memóriában tárolt jogkivonat-gyorsítótár az alkalmazás példányának időtartamát is megtartja.

## <a name="see-which-accounts-are-in-the-cache"></a>A gyorsítótárban lévő fiókok megtekintése

Az alábbi példában látható módon megtekintheti, hogy mely fiókok vannak a gyorsítótárban `PublicClientApplication.getAccounts()` :

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Fiókok eltávolítása a gyorsítótárból

Ha el szeretne távolítani egy fiókot a gyorsítótárból, keresse meg azt a fiókot, amelyet el szeretne távolítani, majd hívja meg az `PublicClientApplicatoin.removeAccount()` alábbi példában látható módon:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Tudjon meg többet

Ha Java-MSAL használ, ismerkedjen meg az [Egyéni jogkivonat-gyorsítótár szerializálásával a MSAL for javában](msal-java-token-cache-serialization.md).
