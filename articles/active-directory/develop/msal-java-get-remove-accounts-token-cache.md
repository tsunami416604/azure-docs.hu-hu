---
title: Fiókok beolvasása és eltávolítása a jogkivonat-gyorsítótárból a MSAL for Java (MSAL4j) használatával
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy miként tekintheti meg és távolíthatja el a fiókokat a jogkivonat-gyorsítótárból a Javához készült Microsoft Authentication Library használatával.
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
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905510"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>Fiókok beolvasása és eltávolítása a jogkivonat-gyorsítótárból a MSAL for Java (MSAL4j) használatával

A MSAL4J alapértelmezés szerint memóriabeli jogkivonat-gyorsítótárat biztosít. A memóriában tárolt jogkivonat-gyorsítótár az alkalmazás példányának időtartamát is megtartja.

## <a name="see-which-accounts-are-in-the-cache"></a>A gyorsítótárban lévő fiókok megtekintése

Az alábbi példában látható módon megtekintheti, hogy mely fiókok vannak a gyorsítótárban `PublicClientApplication.getAccounts()` meghívásával:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Fiókok eltávolítása a gyorsítótárból

Ha el szeretne távolítani egy fiókot a gyorsítótárból, keresse meg azt a fiókot, amelyet el kell távolítania, majd hívja meg a `PublicClientApplicatoin.removeAccount()` az alábbi példában látható módon:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Részletek

Ha Java-MSAL használ, ismerkedjen meg az [Egyéni jogkivonat-gyorsítótár szerializálásával a MSAL for javában](msal-java-token-cache-serialization.md).
