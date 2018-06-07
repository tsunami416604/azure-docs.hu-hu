---
title: 'Az Azure AD Connect: Áteresztő hitelesítés – hogyan működik |} Microsoft Docs'
description: Ez a cikk ismerteti az Azure Active Directory áteresztő hitelesítés működése
services: active-directory
keywords: Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést.
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ca501bb3ad37353f00ffe5d46f72822c7c5487bf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591395"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Az Azure Active Directory átmenő hitelesítést: Műszaki részletes bemutatója
Ez a cikk áttekintése az Azure Active directory (Azure AD) áteresztő hitelesítés működéséről. Mélyhivatkozással műszaki és biztonsági adatokat, lásd: a [biztonsági mélyreható](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) cikk.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hogyan működik az Azure Active Directory átmenő hitelesítést?

Ha a felhasználó megpróbál bejelentkezni az Azure AD által védett alkalmazáshoz, és az áteresztő hitelesítés engedélyezve van a bérlő, a következő lépések következnek:

1. A felhasználó megpróbál csatlakozni egy alkalmazáshoz, például [Outlook Web App](https://outlook.office365.com/owa/).
2. Ha a felhasználó nem jelentkezett, a felhasználó a rendszer átirányítja az Azure AD **felhasználói bejelentkezés** lap.
3. A felhasználó felhasználónevét és jelszavát az Azure AD bejelentkezési oldalára lép, és ezután kiválasztja a **bejelentkezés** gombra.
4. Az Azure Active Directory, a kéréssel bejelentkezni, a felhasználónevét és jelszavát (nyilvános kulccsal titkosított) helyezi a sorhoz.
5. Egy helyszíni hitelesítési ügynök a felhasználónévvel és jelszóval titkosított lekéri az üzenetsorból. Megjegyzendő, hogy az ügynök nem gyakran kérdezze le a kéréseket a sorból, lekéri a kérelmek egy előre meghatározott állandó kapcsolaton keresztül.
6. Az ügynök visszafejti a jelszót a titkos kulcs használatával.
7. Az ügynök ellenőrzi a felhasználónév és jelszó Active Directory általi szabványos Windows API-k használatával, amely egy hasonló mechanizmus milyen Active Directory összevonási szolgáltatások (AD FS) használja. A felhasználónév vagy a helyszíni alapértelmezett felhasználóneve, általában lehet `userPrincipalName`, vagy az Azure AD Connect konfigurált egy másik attribútum (úgynevezett `Alternate ID`).
8. A helyszíni Active Directory tartományvezérlőn (DC) értékeli ki a kérelem és a megfelelő választ ad vissza (sikeres, sikertelen, a jelszó lejárt, vagy felhasználói zárolása) ügynökkel.
9. A hitelesítési ügynök visszaadó ezt a választ az Azure AD vissza.
10. Az Azure AD a válasz kiértékeli, és válaszol-e a megfelelő felhasználó. Például az Azure AD vagy a felhasználó jelentkezik be azonnal, vagy az Azure multi-factor Authentication kér.
11. Ha a felhasználói bejelentkezés sikeres, a felhasználó érhetik el az alkalmazást.

A következő diagram azt ábrázolja, összetevőit és a szükséges lépések:

![Átmenő hitelesítés](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>További lépések
- [Aktuális korlátozások](active-directory-aadconnect-pass-through-authentication-current-limitations.md): megtudhatja, mely forgatókönyvek is támogatottak, és melyek nem.
- [Gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md):, amelyekből megismerheti az Azure AD áteresztő hitelesítés.
- [Intelligens zárolás](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): az intelligens zárolás funkció konfigurálásához a bérlő felhasználói fiókok védelme.
- [Gyakran ismételt kérdések](active-directory-aadconnect-pass-through-authentication-faq.md): gyakran feltett kérdésekre adott válaszok.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Útmutató: az áteresztő hitelesítés szolgáltatás kapcsolatos gyakori problémák megoldásához.
- [Biztonsági mélyreható](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): részletes műszaki információért az áteresztő hitelesítés szolgáltatást.
- [Az Azure AD zökkenőmentes SSO](active-directory-aadconnect-sso.md): további információk a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory fórumán használja a következő fájl új frissítéseiről.

