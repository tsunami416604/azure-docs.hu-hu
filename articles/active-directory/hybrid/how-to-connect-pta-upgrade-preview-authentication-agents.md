---
title: Azure AD Connect – átmenő hitelesítés – Hitelesítési ügynökök frissítése | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan frissítheti az Azure Active Directory (Azure AD) áthaladási hitelesítési konfiguráció.
services: active-directory
keywords: Azure AD Connect áthaladási hitelesítés, active directory telepítése, szükséges összetevők az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés hez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 494ccc3b90b8c249ee935087dcf0f0b5264b02ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386771"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory átadó hitelesítés: Frissítés előzetes hitelesítési ügynökei

## <a name="overview"></a>Áttekintés

Ez a cikk az Azure AD áthaladási hitelesítést előzetes verzióban használó ügyfelek számára szól. Nemrég frissítettük (és átneveztük) a hitelesítési ügynök szoftverét. _Manuálisan_ kell frissítenie a helyszíni kiszolgálókon telepített előzetes hitelesítési ügynököket. Ez a manuális frissítés csak egyszeri művelet. A hitelesítési ügynökök minden jövőbeli frissítése automatikus. A frissítés okai a következők:

- A hitelesítési ügynökök előzetes verziói nem kapnak további biztonsági vagy hibajavításokat.
-   A hitelesítési ügynökök előzetes verziói nem telepíthetők további kiszolgálókra a magas rendelkezésre állás érdekében.

## <a name="check-versions-of-your-authentication-agents"></a>A hitelesítési megbízottak verzióinak ellenőrzése

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>1. lépés: Ellenőrizze, hogy hol vannak telepítve a hitelesítési megbízottak

A hitelesítési megbízottak telepítésének helyének ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza ki az **Azure Active Directory** a bal oldali navigációs.
3. Válassza **az Azure AD Connect lehetőséget.** 
4. Válassza **az áthaladási hitelesítés lehetőséget.** Ez a panel felsorolja azokat a kiszolgálókat, amelyeken a hitelesítési ügynökök telepítve vannak.

![Azure Active Directory felügyeleti központ – Áthaladási hitelesítés panel](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>2. lépés: A hitelesítési megbízottak verzióinak ellenőrzése

A hitelesítési megbízottak verzióinak ellenőrzéséhez az előző lépésben azonosított kiszolgálókon kövesse az alábbi utasításokat:

1. Nyissa meg a **Vezérlőpult -> programok -> programok és szolgáltatások** lapot a helyszíni kiszolgálón.
2. Ha van egy bejegyzés a "**Microsoft Azure AD Connect hitelesítési ügynök**", nem kell semmilyen műveletet ezen a kiszolgálón.
3. Ha van egy bejegyzés a "**Microsoft Azure AD alkalmazásproxy-összekötő**", manuálisan kell frissíteni ezen a kiszolgálón.

![A hitelesítési ügynök előzetes verziója](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>A frissítés megkezdése előtt követendő gyakorlati tanácsok

A frissítés előtt győződjön meg arról, hogy a következő elemek vannak a helyükön:

1. **Csak felhőalapú globális rendszergazdai fiók létrehozása:** Ne frissítsen anélkül, hogy csak felhőalapú globális rendszergazdai fiókkal rendelkezne olyan vészhelyzetekben, amikor az átmenő hitelesítési ügynökök nem működnek megfelelően. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról.](../active-directory-users-create-azure-portal.md) Ez a lépés kritikus fontosságú, és biztosítja, hogy nem kap kizárva a bérlő.
2.  **Magas rendelkezésre állás biztosítása:** Ha korábban nem fejeződött be, telepítsen egy második önálló hitelesítési ügynököt, hogy magas rendelkezésre állást biztosítson a bejelentkezési kérelmekhez az [alábbi utasítások használatával.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>A hitelesítési ügynök frissítése az Azure AD Connect-kiszolgálón

Az Azure AD Connect frissítéséhez frissítenie kell az Azure AD Connectet, mielőtt ugyanazon a kiszolgálón frissítene a hitelesítési ügynököt. Kövesse az alábbi lépéseket az elsődleges és az átmeneti Azure AD Connect-kiszolgálókon is:

1. **Az Azure AD Connect frissítése:** Kövesse ezt a [cikket,](how-to-upgrade-previous-version.md) és frissítsen a legújabb Azure AD Connect verzióra.
2. **Távolítsa el a hitelesítési ügynök előzetes verzióját:** Töltse le [ezt a PowerShell-parancsfájlt,](https://aka.ms/rmpreviewagent) és futtassa rendszergazdaként a kiszolgálón.
3. **Töltse le a hitelesítési ügynök (1.5.389.0-s vagy újabb verzió) legújabb verzióját:** Jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza az **Azure Active Directory -> Azure AD Connect -> átmenő hitelesítés -> letöltési ügynök lehetőséget.** Fogadja el a [szolgáltatási feltételeket,](https://aka.ms/authagenteula) és töltse le a hitelesítési ügynök legújabb verzióját. A hitelesítési ügynököt [innen](https://aka.ms/getauthagent)is letöltheti.
4. **Telepítse a hitelesítési ügynök legújabb verzióját:** Futtassa a 3. Adja meg a bérlő globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri.
5. **Ellenőrizze, hogy a legújabb verzió telepítve van-e:** Ahogy az előzőek is látható, nyissa meg **a Vezérlőpult > programok -> programok és szolgáltatások lapot,** és ellenőrizze, hogy van-e bejegyzés a "**Microsoft Azure AD Connect authentication agent**" számára.

>[!NOTE]
>Ha az előző lépések végrehajtása után ellenőrzi az átadó hitelesítés panelt az [Azure Active Directory felügyeleti központban,](https://aad.portal.azure.com) kiszolgálónként két hitelesítési ügynök-bejegyzés jelenik meg – az egyik bejegyzés ben a hitelesítési ügynök **aktív,** a másik **inaktív.** Ez _várható ._ Az **Inaktív** bejegyzés néhány nap elteltével automatikusan elesik.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>A hitelesítési ügynök frissítése más kiszolgálókon

A hitelesítési ügynökök más kiszolgálókon történő frissítéséhez kövesse az alábbi lépéseket (ahol nincs telepítve az Azure AD Connect):

1. **Távolítsa el a hitelesítési ügynök előzetes verzióját:** Töltse le [ezt a PowerShell-parancsfájlt,](https://aka.ms/rmpreviewagent) és futtassa rendszergazdaként a kiszolgálón.
2. **Töltse le a hitelesítési ügynök (1.5.389.0-s vagy újabb verzió) legújabb verzióját:** Jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza az **Azure Active Directory -> Azure AD Connect -> átmenő hitelesítés -> letöltési ügynök lehetőséget.** Fogadja el a szolgáltatási feltételeket, és töltse le a legújabb verziót.
3. **Telepítse a hitelesítési ügynök legújabb verzióját:** Futtassa a 2. Adja meg a bérlő globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri.
4. **Ellenőrizze, hogy a legújabb verzió telepítve van-e:** Akorábbi ahogy az előzőeket is látható, nyissa meg a **Vezérlőpult > programok -> programok és szolgáltatások** lapot, és ellenőrizze, hogy van-e **Microsoft Azure AD Connect authentication agent**nevű bejegyzés.

>[!NOTE]
>Ha az előző lépések végrehajtása után ellenőrzi az átadó hitelesítés panelt az [Azure Active Directory felügyeleti központban,](https://aad.portal.azure.com) kiszolgálónként két hitelesítési ügynök-bejegyzés jelenik meg – az egyik bejegyzés ben a hitelesítési ügynök **aktív,** a másik **inaktív.** Ez _várható ._ Az **Inaktív** bejegyzés néhány nap elteltével automatikusan elesik.

## <a name="next-steps"></a>További lépések
- [**Hibaelhárítás**](tshoot-connect-pass-through-authentication.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
