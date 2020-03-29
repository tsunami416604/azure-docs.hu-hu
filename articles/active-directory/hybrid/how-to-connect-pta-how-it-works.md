---
title: 'Azure AD Connect: Átmenő hitelesítés - Hogyan működik | Microsoft dokumentumok'
description: Ez a cikk az Azure Active Directory áteredési hitelesítésének működését ismerteti
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
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347778"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory átadó hitelesítés: Technikai mélymerülés
Ez a cikk áttekintést nyújt az Azure Active Directory (Azure AD) átmenő hitelesítés működéséről. A részletes technikai és biztonsági információkért tekintse meg a [Biztonsági mélymerülésről](how-to-connect-pta-security-deep-dive.md) szóló cikket.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hogyan működik az Azure Active Directory átadó hitelesítése?

>[!NOTE]
>Az átadó hitelesítés működéséhez szükséges előfeltételeként a felhasználókat az Azure AD-be kell kiépíteni a helyszíni Active Directoryból az Azure AD Connect használatával. Az átmenő hitelesítés nem vonatkozik a csak felhőalapú felhasználókra.

Amikor egy felhasználó megpróbál bejelentkezni egy Azure AD által védett alkalmazásba, és ha a bérlőn engedélyezve van az átmenő hitelesítés, a következő lépések lépnek be:

1. A felhasználó megpróbál hozzáférni egy alkalmazáshoz, például az [Outlook Web Apphoz.](https://outlook.office365.com/owa/)
2. Ha a felhasználó még nincs bejelentkezve, a felhasználó átirányítja az Azure AD **felhasználói bejelentkezési** lapra.
3. A felhasználó beírja a felhasználónevét az Azure AD bejelentkezési lapon, majd kiválasztja a **Tovább** gombot.
4. A felhasználó beírja a jelszavát az Azure AD bejelentkezési lapon, majd kiválasztja a **Bejelentkezés** gombot.
5. Az Azure AD a bejelentkezési kérelem fogadásakor várólistába helyezi a felhasználónevet és a jelszót (a hitelesítési ügynökök nyilvános kulcsával titkosítva).
6. A helyszíni hitelesítési ügynök lekéri a felhasználónevet és a titkosított jelszót a várólistából. Vegye figyelembe, hogy az ügynök nem gyakran lekérdezése a kérelmek a várólistából, de lekéri a kérelmeket egy előre létrehozott állandó kapcsolaton keresztül.
7. Az ügynök a személyes kulcsával visszafejti a jelszót.
8. Az ügynök a felhasználónevet és a jelszót az Active Directoryhoz képest ellenőrzi szabványos Windows API-k használatával, ami hasonló mechanizmus, mint amit az Active Directory összevonási szolgáltatások (AD FS) használ. A felhasználónév lehet a helyszíni alapértelmezett felhasználónév, `userPrincipalName`általában, vagy egy másik attribútum konfigurálva `Alternate ID`az Azure AD Connect (más néven).
9. A helyszíni Active Directory-tartományvezérlő (DC) kiértékeli a kérelmet, és visszaadja a megfelelő választ (sikeres, sikertelen, jelszó lejárt, vagy a felhasználó zárolt) az ügynöknek.
10. A hitelesítési ügynök, viszont, visszaküldi ezt a választ vissza az Azure AD.
11. Az Azure AD kiértékeli a választ, és szükség szerint válaszol a felhasználónak. Például az Azure AD vagy azonnal bejelentkezik a felhasználó, vagy az Azure többtényezős hitelesítésre vonatkozó kérelmeket.
12. Ha a felhasználó bejelentkezése sikeres, a felhasználó hozzáférhet az alkalmazáshoz.

Az alábbi ábra az összes összetevőt és a leírt lépéseket mutatja be:

![Átmenő hitelesítés](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>További lépések
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): Ismerje meg, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Rövid útmutató:](how-to-connect-pta-quick-start.md)Az Azure AD áthaladási hitelesítése.
- [Az AD FS-ről átmenő hitelesítésre való áttelepítés](https://aka.ms/adfstoPTADP) részletes útmutató az AD FS-ről (vagy más összevonási technológiákról) az átmenő hitelesítésre való áttelepítéshez.
- [Intelligens zárolás:](../authentication/howto-password-smart-lockout.md)Konfigurálja a bérlő intelligens zárolási képességét a felhasználói fiókok védelme érdekében.
- [Gyakori kérdések](how-to-connect-pta-faq.md): Válaszok a gyakori kérdésekre.
- [Hibaelhárítás:](tshoot-connect-pass-through-authentication.md)Ismerje meg, hogyan oldhatja meg az átmenő hitelesítés szolgáltatással kapcsolatos gyakori problémákat.
- [Biztonsági mélymerülés:](how-to-connect-pta-security-deep-dive.md)Részletes technikai információk beszerezhető az átmenő hitelesítési funkcióról.
- [Azure AD seamless egyszeri bejelentkezés:](how-to-connect-sso.md)További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az Azure Active Directory fórum használatával új szolgáltatáskérelmek et nyújthat be.

