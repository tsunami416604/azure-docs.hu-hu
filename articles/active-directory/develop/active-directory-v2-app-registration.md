---
title: Az alkalmazás regisztrálása az Azure AD v2.0-végpontra a portál használatával |} Microsoft Docs
description: Alkalmazás regisztrálása a Microsoft bejelentkezés engedélyezése és használata a Microsoft-szolgáltatásokat a v2.0-végpontra segítségével
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157843"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Alkalmazás regisztrálása a v2.0-végponttal
Hozza létre az alkalmazását, amely fogadja a személyes Microsoft-fiók (msa-t) & munkahelyi vagy iskolai fiókja (az Azure AD) bejelentkezés, hogy először regisztrálja az alkalmazást a Microsoft számára. Most, akkor használhatja a meglévő alkalmazásokat, előfordulhat, hogy az Azure ad-vel vagy MSA - szüksége lesz egy teljesen új létrehozásához.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól. Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el a [v2.0 korlátozások](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Látogasson el a Microsoft app-regisztrálási portál
Első lépésként nyissa meg a Microsoft app-regisztrálási portál következő [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Jelentkezzen be vagy egy személyes vagy a munkahelyi vagy iskolai Microsoft-fiókkal. Ha még nem rendelkezik, vagy egy új személyes fiók.

Tenni? Meg kell most megtekintik a Microsoft-alkalmazások listája olyan valószínűleg üres. Módosítsuk, amely.

Kattintson a **hozzáadhat egy alkalmazást**, és adjon neki egy nevet. A portál rendeli az alkalmazás egy globálisan egyedi azonosítója, amelyet később a kódjában fog használni. Ha az alkalmazás olyan kiszolgálóoldali összetevőt tartalmaz, hogy kell-e hozzáférési jogkivonatok hívó API-k (gondolja, hogy: Office, az Azure vagy a saját webes API-t), érdemes létrehozni egy **Alkalmazáskulcsot** itt is.

Ezután adja hozzá a **platformok** , amelyet az alkalmazás használni fog.

* Webalapú alkalmazásokhoz, adja meg a **átirányítási URI-** ahol bejelentkezési üzenetek elküldhetők.
* Mobilalkalmazások másolja le az alapértelmezett átirányítási URI automatikusan létrejön.
* Webes API-k a Web API eléréséhez alapértelmezett hatókör automatikusan létrejön. Ha szeretné, további hatókörök hozzáadása a **hatókör hozzáadása** gombra. Olyan alkalmazásokat, amelyek jogosultak előre használatára, a Web API használatával is hozzáadhat a **előre engedélyezett alkalmazások** űrlap. 

Másik lehetőségként testre szabhatja a bejelentkezési oldalának megjelenését és működését a **profil** szakasz. Győződjön meg arról, hogy kattintson **mentése** lépés előtt.

> [!NOTE]
> Amikor hoz létre egy alkalmazást, amely [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), az alkalmazás regisztrálva lesz, a home bérlői fiók használatával jelentkezzen be a portálra. Ez azt jelenti, hogy nem regisztrálhatja az alkalmazás az Azure AD-bérlő személyes Microsoft-fiókkal. Ha explicit módon szeretne egy alkalmazást regisztrálni kell az egy adott bérlő, olyan fiókkal jelentkezzen be, hogy a bérlő eredetileg létrehozott.


## <a name="build-a-quickstart-app"></a>A gyors üzembe helyezés alkalmazás létrehozása
Most, hogy a Microsoft app, befejezheti az v2.0 gyors üzembe helyezési oktatóprogramok valamelyikét. Az alábbiakban néhány javaslatot olvashat:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

