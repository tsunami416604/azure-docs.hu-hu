---
title: "Az alkalmazás regisztrálása az Azure AD v2.0-végpontra a portál használatával |} Microsoft Docs"
description: "Alkalmazás regisztrálása a Microsoft bejelentkezés engedélyezése és használata a Microsoft-szolgáltatásokat a v2.0-végpontra segítségével"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: e6202aa8665c906382666fe08a561421e50e0a8d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Alkalmazás regisztrálása a v2.0-végponttal
Msa-t és az Azure AD is elfogadó alkalmazás létrehozásához bejelentkezés, először regisztrálja az alkalmazást a Microsoft számára.  Most, akkor használhatja a meglévő alkalmazásokat, előfordulhat, hogy az Azure ad-vel vagy MSA - szüksége lesz egy teljesen új létrehozásához.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Látogasson el a Microsoft app-regisztrálási portál
Első lépések először - navigáljon [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Ez az egy új app regisztrációs portált, ahol kezelheti a Microsoft-alkalmazások.

Jelentkezzen be vagy egy személyes vagy a munkahelyi vagy iskolai Microsoft-fiókkal.  Ha még nem rendelkezik, vagy egy új személyes fiók. Lépjen tovább, nem tart sokáig - itt fogja várunk.

Tenni? Meg kell most megtekintik a Microsoft-alkalmazások listája olyan valószínűleg üres.  Módosítsuk, amely.

Kattintson a **hozzáadhat egy alkalmazást**, és adjon neki egy nevet.  A portál rendeli az alkalmazás egy globálisan egyedi azonosítója, amelyet később a kódjában fog használni.  Ha az alkalmazás olyan kiszolgálóoldali összetevőt tartalmaz, hogy kell-e hozzáférési jogkivonatok hívó API-k (gondolja, hogy: Office, az Azure vagy a saját webes API-t), érdemes létrehozni egy **Alkalmazáskulcsot** itt is.

Ezután adja hozzá a platformokat, amelyek az alkalmazás fogja használni.

* Webalapú alkalmazások esetén adja meg a **átirányítási URI-** ahol bejelentkezési üzenetek elküldhetők.
* Mobilalkalmazások másolja le az automatikusan létrehozott, alapértelmezett átirányítási uri.

Másik lehetőségként testre szabhatja a bejelentkezési oldal a profil szakaszban megjelenését és működését.  Győződjön meg arról, hogy kattintson **mentése** lépés előtt.

> [!NOTE]
> Amikor hoz létre egy alkalmazást, amely [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), az alkalmazás regisztrálva lesz, a home bérlői fiók használatával jelentkezzen be a portálra.  Ez azt jelenti, hogy nem regisztrálhatja az alkalmazás az Azure AD-bérlő személyes Microsoft-fiókkal.  Ha explicit módon szeretne egy alkalmazást regisztrálni kell az egy adott bérlő, olyan fiókkal jelentkezzen be, hogy a bérlő eredetileg létrehozott.
> 
> 

## <a name="build-a-quick-start-app"></a>A gyors üzembe helyezés alkalmazás létrehozása
Most, hogy a Microsoft app, befejezheti a v2.0 gyors üzembe helyezési oktatóprogramok valamelyikét.  Az alábbiakban néhány javaslatot olvashat:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

