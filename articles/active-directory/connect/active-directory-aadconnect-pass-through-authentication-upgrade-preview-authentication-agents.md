---
title: Az Azure AD Connect - átmenő hitelesítés – hitelesítés frissítési ügynökök |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Active Directory (Azure AD) átmenő hitelesítés konfigurációjának frissítése.
services: active-directory
keywords: Az Azure AD Connect az átmenő hitelesítés, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: e955973008f1ba3900deb691426d0409440ad08a
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917727"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Az Azure Active Directory átmenő hitelesítés: Frissítési előzetes verziójú hitelesítési ügynökök

## <a name="overview"></a>Áttekintés

Ez a cikk az egyelőre csak előzetes verzióként az Azure AD átmenő hitelesítést használó ügyfelek számára. A Microsoft nemrég frissítve (és nevezve) a hitelesítési ügynök szoftver. Kell _manuálisan_ frissítési előzetes hitelesítési ügynökök telepítése a helyszíni kiszolgálókon. A kézi frissítés egy egyszeri művelet. Hitelesítési ügynökök az összes jövőbeli frissítés automatikus. Frissítése az okok a következők:

- Hitelesítési ügynökök előzetes verziók nem kap semmilyen további biztonsági vagy hibajavítások megjelenésekor.
-   Hitelesítési ügynökök előzetes verziók nem telepíthető más kiszolgálókon, a magas rendelkezésre állás érdekében.

## <a name="check-versions-of-your-authentication-agents"></a>Ellenőrizze a hitelesítési ügynökök verziói

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>1. lépés: Ellenőrizze, hogy ha a hitelesítési ügynökök telepítése

Kövesse az alábbi lépéseket, ellenőrizze, amelyen telepítve vannak-e az a hitelesítési ügynökök:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs.
3. Válassza ki **az Azure AD Connect**. 
4. Válassza ki **átmenő hitelesítés**. Ezen a panelen a kiszolgálók, amelyeken telepítve vannak-e az a hitelesítési ügynökök listája.

![Az Azure Active Directory felügyeleti központ – átmenő hitelesítés panel](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>2. lépés: A hitelesítési ügynökök verziójának ellenőrzéséhez.

A hitelesítési ügynökök minden kiszolgálón, az előző lépésben azonosított verziójának ellenőrzéséhez kövesse az alábbi utasításokat:

1. Lépjen a **Vezérlőpult -> Programok -> Programok és szolgáltatások** a helyi kiszolgálón.
2. Ha van olyan bejegyzés "**a Microsoft Azure AD Connect hitelesítési ügynökének**", nem kell semmit sem, ezen a kiszolgálón.
3. Ha van olyan bejegyzés "**Microsoft Azure AD alkalmazásproxy-összekötő**", verzió 1.5.132.0 vagy korábbi, ezen a kiszolgálón manuálisan frissítenie kell.

![A hitelesítési ügynök előzetes verziója](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Ajánlott eljárásokat, és kövesse a frissítés megkezdése előtt

A frissítés előtt ellenőrizze, hogy a következő elemek helyen:

1. **Csak felhőalapú globális rendszergazdai fiók létrehozása**: nem a meglévő anélkül, hogy egy csak felhőalapú globális rendszergazdai fiók használata a vészhelyzetekre, ahol az átmenő hitelesítés ügynökök nem működnek megfelelően. Ismerje meg [hozzáadása egy csak felhőalapú globális rendszergazdai fiókkal](../active-directory-users-create-azure-portal.md). Ehhez a lépéshez kritikus fontosságú, és biztosítja, hogy ne zárja ki a bérlő.
2.  **Magas rendelkezésre állásának biztosításához**: Ha korábban nem sikerült befejezni, telepítse a magas rendelkezésre állást biztosít a bejelentkezési kérelmek ezeket a hitelesítési ügynök második önálló [utasításokat](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Az Azure AD Connect-kiszolgálón a hitelesítési ügynök frissítése

A hitelesítési ügynök ugyanazon a kiszolgálón a frissítés előtt frissítenie kell az Azure AD Connect. Az elsődleges és az Azure AD Connect-kiszolgálók átmeneti kövesse az alábbi lépéseket:

1. **Azure AD Connect frissítése**: ezen [cikk](./active-directory-aadconnect-upgrade-previous-version.md) , frissítsen az Azure AD Connect legújabb verziójára.
2. **Távolítsa el az előzetes verzióját, a hitelesítési ügynök**: letöltése [a PowerShell-szkript](https://aka.ms/rmpreviewagent) , és futtassa rendszergazdaként a kiszolgálón.
3. **Töltse le a hitelesítési ügynök legújabb verzióját (1.5.193.0 verzió vagy újabb)**: Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza ki **Azure Active Directory -> az Azure AD Connect -> az átmenő hitelesítés ügynök letöltése ->**. Fogadja el a [szolgáltatási feltételeit](https://aka.ms/authagenteula) , és töltse le a hitelesítési ügynök legújabb verzióját. Is letöltheti a hitelesítési ügynök a [Itt](https://aka.ms/getauthagent).
4. **Telepítse a legújabb verzióját, a hitelesítési ügynök**: futtassa a végrehajtható fájlt a 3. lépésben letöltött. Adja meg a bérlő globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri.
5. **Győződjön meg arról, hogy a legújabb verziója telepítve van-e**: módon Ugrás **Vezérlőpult -> Programok -> Programok és szolgáltatások** , és ellenőrizze, hogy nincs-e egy bejegyzés "**Microsoft Azure AD Connect A hitelesítési ügynök**".

>[!NOTE]
>Ha az átmenő hitelesítés panelen ellenőrizheti a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) befejezése az előző lépések után látni fogja a két hitelesítési ügynök bejegyzés kiszolgálónként – egy bejegyzést a hitelesítés megjelenítése Ügynök **aktív** és az egyéb as **inaktív**. Ez a _várt_. A **inaktív** néhány nap elteltével automatikusan eltávolítja a bejegyzést.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>A hitelesítési ügynök, a többi kiszolgáló frissítése

Kövesse az alábbi lépéseket a többi kiszolgáló hitelesítési ügynökök frissítése (Ha nincs telepítve az Azure AD Connect):

1. **Távolítsa el az előzetes verzióját, a hitelesítési ügynök**: letöltése [a PowerShell-szkript](https://aka.ms/rmpreviewagent) , és futtassa rendszergazdaként a kiszolgálón.
2. **Töltse le a hitelesítési ügynök legújabb verzióját (1.5.193.0 verzió vagy újabb)**: Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza ki **Azure Active Directory -> az Azure AD Connect -> az átmenő hitelesítés ügynök letöltése ->**. Fogadja el a szolgáltatási feltételeket, és a legújabb verzió letöltéséhez.
3. **Telepítse a legújabb verzióját, a hitelesítési ügynök**: futtassa a 2. lépésben letöltött végrehajtható fájlt. Adja meg a bérlő globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri.
4. **Győződjön meg arról, hogy a legújabb verziója telepítve van-e**: módon Ugrás **Vezérlőpult -> Programok -> Programok és szolgáltatások** , és ellenőrizze, hogy nincs-e bejegyzés **Microsoft Azure AD Connect A hitelesítési ügynök**.

>[!NOTE]
>Ha az átmenő hitelesítés panelen ellenőrizheti a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) befejezése az előző lépések után látni fogja a két hitelesítési ügynök bejegyzés kiszolgálónként – egy bejegyzést a hitelesítés megjelenítése Ügynök **aktív** és az egyéb as **inaktív**. Ez a _várt_. A **inaktív** néhány nap elteltével automatikusan eltávolítja a bejegyzést.

## <a name="next-steps"></a>További lépések
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – ismerje meg, a szolgáltatással kapcsolatos gyakori problémák megoldása.
