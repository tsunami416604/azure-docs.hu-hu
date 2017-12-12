---
title: "Az Azure AD Connect: Áteresztő hitelesítés - frissítési előzetes hitelesítés ügynökök |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan lehet frissíteni az Azure Active Directory (Azure AD) áteresztő hitelesítés konfigurációját."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.openlocfilehash: 96d4e8e240d845be5f04bf0e724d91d740ad9cdf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Az Azure Active Directory áteresztő hitelesítés: Frissítési előzetes hitelesítés ügynökök

## <a name="overview"></a>Áttekintés

Ez a cikk az előzetes Azure AD átmenő hitelesítést használó ügyfelek számára. A Microsoft nemrég frissítése (és rebranded) hitelesítési ügynökszoftver. Kell _manuálisan_ frissítési előzetes hitelesítés ügynökök telepítése a helyszíni kiszolgálókon. A kézi frissítés egy egyszeri művelet. Minden jövőbeni frissítése hitelesítési ügynökök automatikus. Frissítése az okok a következők:

- A hitelesítési ügynökök előzetes verziói nem kapják meg semmilyen további biztonsági vagy hibajavításokat tartalmaz.
-   Hitelesítési ügynökök előzetes verziói nem telepíthető a további kiszolgálók, a magas rendelkezésre állás.

## <a name="check-versions-of-your-authentication-agents"></a>Ellenőrizze a hitelesítési ügynökök verziói

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>1. lépés: Ellenőrizze, amelyen telepítve van-e a hitelesítés ügynök

Kövesse az alábbi lépéseket, ahol telepítve van-e a hitelesítés ügynök kereséséhez:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő számára a globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs.
3. Válassza ki **az Azure AD Connect**. 
4. Válassza ki **áteresztő hitelesítés**. Ezen a panelen megjeleníti a kiszolgálók, ahol telepítve van-e a hitelesítés ügynök.

![Az Azure Active Directory felügyeleti központ – áteresztő hitelesítés panel](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>2. lépés: Ellenőrizze a hitelesítési ügynökök verziói

Azon verzióit, minden kiszolgálón, az előző lépésben azonosított a hitelesítési ügynököt ellenőrzéséhez kövesse az alábbi utasításokat:

1. Ugrás a **Vezérlőpult -> Programok -> Programok és szolgáltatások** a helyi kiszolgálón.
2. Ha a bejegyzést "**Microsoft Azure AD Connect hitelesítési ügynök**", nem kell tennie semmit ezen a kiszolgálón.
3. Ha a bejegyzést "**Microsoft Azure AD alkalmazásproxy-összekötő**", verzió 1.5.132.0 vagy korábbi, frissítenie kell manuálisan ezen a kiszolgálón.

![Hitelesítési ügynök előzetes verzióját](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Ajánlott eljárásokat a frissítés megkezdése előtt

A frissítés előtt ellenőrizze, hogy a következő elemek helyen:

1. **Csak felhőalapú globális rendszergazdai fiók létrehozása**: nem a meglévő anélkül, hogy egy kizárólag felhőalapú globális rendszergazdai fiókot vészhelyzeti olyan esetekben, ahol az áteresztő hitelesítés ügynökök nem megfelelően működnek-e. További tudnivalók [csak felhőalapú globális rendszergazdai fiókot hozzá](../active-directory-users-create-azure-portal.md). Ez a lépés elvégzése alapvető fontosságú, és biztosítja, hogy Ön nem ártatlan tévedéssel a bérlő.
2.  **Magas rendelkezésre állásának biztosításához**: Ha korábban nem sikerült befejezni, telepítse a második önálló hitelesítési ügynök a bejelentkezési kérelmek ezeket a magas rendelkezésre állás biztosításához [utasításokat](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>A hitelesítési ügynök a Azure AD Connect-kiszolgáló frissítése

A hitelesítési ügynök ugyanarra a kiszolgálóra a frissítés előtt frissítenie kell az Azure AD Connect. Az elsődleges és az Azure AD Connect-kiszolgálók átmeneti kövesse az alábbi lépéseket:

1. **Az Azure AD Connect frissítése**: folytassa a [cikk](./active-directory-aadconnect-upgrade-previous-version.md) , frissítsen az Azure AD Connect legújabb verziójára.
2. **Távolítsa el az előzetes verziót, a hitelesítési ügynök**: letöltése [a PowerShell parancsfájl](https://aka.ms/rmpreviewagent) , és futtassa rendszergazdaként a kiszolgálón.
3. **A hitelesítési ügynök a legújabb verzió letöltéséhez (1.5.193.0 verzió vagy újabb)**: Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza ki **Azure Active Directory -> az Azure AD Connect -> áteresztő hitelesítés -> Letöltés ügynök**. Fogadja el a [szolgáltatási feltételek](https://aka.ms/authagenteula) és a hitelesítési ügynök a legújabb verzió letöltéséhez. Emellett letöltheti a hitelesítési ügynök a [Itt](https://aka.ms/getauthagent).
4. **Telepítse a legújabb verzióját a hitelesítési ügynök**: a 3. lépésben letöltött végrehajtható fájl futtatásához. Adja meg a bérlő globális rendszergazda hitelesítő adatait, amikor a rendszer kéri.
5. **Győződjön meg arról, hogy a legújabb verzió telepítve van-e**: előtt ahogy Ugrás **Vezérlőpult -> Programok -> Programok és szolgáltatások** , és ellenőrizze, hogy van-e bejegyzés "**Microsoft Azure AD Connect Hitelesítési ügynök**".

>[!NOTE]
>Ha bejelöli az áteresztő hitelesítés panel a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) után befejezése az előző lépések, látni fogja a két hitelesítési ügynök bejegyzés egy kiszolgálón – a hitelesítési megjelenítő egy bejegyzés Ügynök **aktív** és az egyéb, **inaktív**. Ez az _várt_. A **inaktív** néhány nap múlva automatikusan eltávolítja a bejegyzést.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>A hitelesítési ügynök a többi kiszolgáló frissítése

Kövesse az alábbi lépéseket a többi kiszolgáló hitelesítési-ügynökök frissítése (amelyen nincs telepítve az Azure AD Connect):

1. **Távolítsa el az előzetes verziót, a hitelesítési ügynök**: letöltése [a PowerShell parancsfájl](https://aka.ms/rmpreviewagent) , és futtassa rendszergazdaként a kiszolgálón.
2. **A hitelesítési ügynök a legújabb verzió letöltéséhez (1.5.193.0 verzió vagy újabb)**: Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza ki **Azure Active Directory -> az Azure AD Connect -> áteresztő hitelesítés -> Letöltés ügynök**. Fogadja el a szolgáltatási feltételeket, és a legújabb verzió letöltéséhez.
3. **Telepítse a legújabb verzióját a hitelesítési ügynök**: a 2. lépésben letöltött végrehajtható fájl futtatásához. Adja meg a bérlő globális rendszergazda hitelesítő adatait, amikor a rendszer kéri.
4. **Győződjön meg arról, hogy a legújabb verzió telepítve van-e**: előtt ahogy Ugrás **Vezérlőpult -> Programok -> Programok és szolgáltatások** , és ellenőrizze, hogy van-e nevezett bejegyzés **Microsoft Azure AD Connect Hitelesítési ügynök**.

>[!NOTE]
>Ha bejelöli az áteresztő hitelesítés panel a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) után befejezése az előző lépések, látni fogja a két hitelesítési ügynök bejegyzés egy kiszolgálón – a hitelesítési megjelenítő egy bejegyzés Ügynök **aktív** és az egyéb, **inaktív**. Ez az _várt_. A **inaktív** néhány nap múlva automatikusan eltávolítja a bejegyzést.

## <a name="next-steps"></a>Következő lépések
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
