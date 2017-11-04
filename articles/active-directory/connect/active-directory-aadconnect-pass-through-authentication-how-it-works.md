---
title: "Az Azure AD Connect: Áteresztő hitelesítés – hogyan működik? | Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Active Directory áteresztő hitelesítés működéséről."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: c7863e38671349b6424ee08330da8aaa49cb2a70
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Az Azure Active Directory átmenő hitelesítést: Műszaki részletes bemutatója
A következő cikk egy Azure AD áteresztő hitelesítés működéséről áttekintése.  Mélyhivatkozással műszaki és biztonsági információ: a [ **biztonsági mélyreható** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) cikk.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hogyan működik az Azure Active Directory átmenő hitelesítést?

Amikor egy felhasználó próbál bejelentkezni az Azure Active Directory (Azure AD) által védett alkalmazáshoz, és az áteresztő hitelesítés engedélyezve van a bérlő, a következő lépések következnek:

1. A felhasználó megpróbál hozzáférni az alkalmazáshoz (például az Outlook Web App - https://outlook.office365.com/owa/).
2. Ha a felhasználó nem jelentkezett, a felhasználót a rendszer átirányítja az Azure AD bejelentkezési oldalára.
3. A felhasználó felhasználónevét és jelszavát köt az Azure AD bejelentkezési oldal, és a "Bejelentkezés" gombra kattint.
4. Az Azure Active Directory, a bejelentkezési kérelem fogadása helyez el a felhasználónevet és jelszót (a nyilvános kulccsal titkosított) várólista.
5. Egy helyszíni hitelesítési ügynök a felhasználónévvel és jelszóval titkosított lekéri az üzenetsorból.
6. Az ügynök visszafejti a jelszót a titkos kulccsal.
7. Az ügynök szerint hitelesíti a felhasználónevet és jelszót normál Windows API-k (hasonló eljárást, amely Active Directory összevonási szolgáltatások által használttól) Active Directoryban. A felhasználónév vagy a helyszíni alapértelmezett felhasználónév lehet (általában `userPrincipalName`) vagy az Azure AD Connect konfigurált egy másik attribútum (úgynevezett `Alternate ID`).
8. A helyszíni Active Directory tartományvezérlőn (DC) ezután kiértékeli a kérelmet, és a megfelelő választ ad vissza (sikeres, sikertelen, a jelszó lejárt, vagy felhasználói zárolása) ügynökkel.
9. A hitelesítési ügynök visszaadó ezt a választ az Azure AD vissza.
10. Az Azure AD a válasz kiértékeli, és válaszol-e a felhasználó megfelelő – például azt vagy a felhasználó jelentkezik be azonnal, vagy kérelmeket a multi-factor Authentication (MFA).
11. Ha a felhasználói bejelentkezés sikeres, a felhasználó nem tud hozzáférni az alkalmazáshoz.

A következő ábra összetevőit és a lépéseit mutatja be.

![Átmenő hitelesítés](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Következő lépések
- [**Aktuális korlátozások** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [**Gyors üzembe helyezési** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - létrehozásához, és fut az Azure AD áteresztő hitelesítés.
- [**Intelligens zárolás** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -intelligens zárolás konfigurálása képességet a bérlő a felhasználói fiókok védelme.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-pass-through-authentication-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**Biztonsági mélyreható** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -részletes műszaki további információ a szolgáltatásról.
- [**Az Azure AD zökkenőmentes SSO** ](active-directory-aadconnect-sso.md) -további információ a kiegészítő funkció.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
