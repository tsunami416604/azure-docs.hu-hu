---
title: Alkalmazás regisztrálása az az Azure AD v2.0-végpont a portál használatával |} A Microsoft Docs
description: Alkalmazás regisztrálása a Microsoft bejelentkezési és a v2.0-végpont használatával a Microsoft-szolgáltatásokhoz
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
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506548"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Alkalmazás regisztrálása a v2.0-végponttal
Létrehozhat egy alkalmazást, amely egyaránt személyes Microsoft-fiókkal (MSA) & munkahelyi vagy iskolai fiókkal (Azure AD) jelentkezzen be, először szüksége az alkalmazás regisztrálása a Microsoft. Jelenleg nem kell minden meglévő alkalmazást, előfordulhat, hogy az Azure ad-vel használni, vagy az MSA - kell egy új létrehozásához.

> [!NOTE]
> Nem minden Azure Active Directory-forgatókönyvet és funkciót támogatja a v2.0-végpontra. Annak megállapításához, ha a v2.0-végpont használja, olvassa el a [v2.0 korlátozások](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Látogasson el a Microsoft app-regisztrációs portál
A Microsoft app-regisztrálási portál, először lépjen [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Jelentkezzen be vagy személyes vagy munkahelyi vagy iskolai Microsoft-fiókjával. Ha nem rendelkezik, vagy regisztrálhat egy új személyes fiókot.

Kész? Meg kell most megtekintik a Microsoft-alkalmazások listája Ez valószínűleg üres. Módosítsuk, amely.

Kattintson a **alkalmazás hozzáadása**, és adjon meg egy nevet. A portál rendeli az alkalmazást a globálisan egyedi Azonosítót, amely a kód későbbi részében fogja használni. Ha az alkalmazás olyan kiszolgálóoldali összetevőt tartalmaz, amely kell-e hozzáférési jogkivonatok az API-k (úgy gondolja, hogy: Office, Azure, vagy saját webes API-t), érdemes létrehozni egy **Application Secret** itt is.

Ezután adja hozzá a **platformok** , amely az alkalmazás fogja használni.

* A webes alkalmazások esetén adja meg egy **átirányítási URI-t** ahol bejelentkezési üzenetek elküldhetők.
* A mobile apps másolja le az alapértelmezett átirányítási URI-t automatikusan létrejön.
* A webes API-k a webes API eléréséhez alapértelmezett hatókör automatikusan létrejön az Ön számára. Dönthet úgy, hogy használatával további hatókört is felvehet a **hatókör hozzáadása** gombra. Olyan alkalmazásokat, amelyek jogosultak előre használatára, a webes API használatával is hozzáadhat a **az előre engedélyezett alkalmazások** űrlap. 

Igény szerint testre szabhatja a megjelenését és működését a bejelentkezési oldalon a a **profil** szakaszban. Győződjön meg arról, hogy kattintson **mentése** lépés előtt.

> [!NOTE]
> Ha létrehozott egy alkalmazást a [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), az alkalmazás regisztrálva lesz az otthoni bérlőben való a portálra való bejelentkezéshez használt fiók. Ez azt jelenti, hogy is nem regisztrálhat egy alkalmazást az Azure AD-bérlő személyes Microsoft-fiókkal. Ha explicit módon szeretne regisztrálni egy alkalmazást egy adott bérlő, jelentkezzen be az adott bérlőn belüli eredetileg létrehozott fiók.


## <a name="build-a-quickstart-app"></a>A rövid útmutató alkalmazás készítése
Most, hogy egy Microsoft-alkalmazást, a 2.0-s verziójú gyorsútmutatók egyik elvégezhető. Az alábbiakban néhány javaslatot olvashat:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

