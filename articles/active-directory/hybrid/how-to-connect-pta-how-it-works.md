---
title: 'Az Azure AD Connect: Az átmenő hitelesítés – hogyan működik |} A Microsoft Docs'
description: Ez a cikk bemutatja, hogyan működik, az Azure Active Directory átmenő hitelesítés
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
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b8a758923f0e3e287da4a377d140f1f659cef46f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312696"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Az Azure Active Directory átmenő hitelesítés: Részletes technikai bemutatása
Ez a cikk áttekintést az Azure Active directory (Azure AD) átmenő hitelesítés működéséről. Részletes technikai és biztonsági információk, tekintse meg a [biztonság részletes bemutatása](how-to-connect-pta-security-deep-dive.md) cikk.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hogyan működik az Azure Active Directory átmenő hitelesítés?

>[!NOTE]
>Előfeltételként az átmenő hitelesítés csak akkor működik felhasználók szükség lesz az Azure AD Connect használatával a helyszíni Active Directoryból az Azure AD-be. Az átmenő hitelesítés nem vonatkozik a csak felhőbeli felhasználók számára.

Ha a felhasználó megpróbál bejelentkezni az Azure AD által védett alkalmazáshoz való, és ha az átmenő hitelesítés engedélyezve van a bérlőn, a következő lépések történnek:

1. A felhasználó megpróbál hozzáférni egy alkalmazáshoz, például [Outlook Web App](https://outlook.office365.com/owa/).
2. Ha a felhasználó még nem jelentkezett be, a felhasználó az Azure ad átirányítási **felhasználói bejelentkezés** lapot.
3. A felhasználónak a felhasználóneve köt az Azure AD bejelentkezési oldal, majd kiválasztja a **tovább** gombra.
4. A felhasználó beírja a jelszavát a az Azure AD bejelentkezési lapján, és ezután kiválasztja a **jelentkezzen be a** gombra.
5. Az Azure AD-ben való bejelentkezéshez, a kérés érkezése egy üzenetsorban lévő helyezi el a felhasználónevet és jelszót (a hitelesítési ügynökök a nyilvános kulccsal titkosított).
6. A helyszíni hitelesítési ügynök lekéri a felhasználónévvel és jelszóval titkosított az üzenetsorból. Vegye figyelembe, hogy az ügynök nem gyakran lekérdezi az üzenetsor kéréseit, de kérdezi le a kérelmek egy előre meghatározott állandó kapcsolaton keresztül.
7. Az ügynök visszafejti a jelszót a titkos kulcs használatával.
8. Az ügynök ellenőrzi a felhasználónevet és jelszót Active Directorybeli szabványos Windows API-k használatával, amely hasonló mechanizmus, hogy mely Active Directory összevonási szolgáltatások (AD FS) használja. A felhasználónév vagy a helyszíni alapértelmezett felhasználónév, általában lehet `userPrincipalName`, vagy egy másik attribútum konfigurálva az Azure AD Connectben (más néven `Alternate ID`).
9. A helyszíni Active Directory tartományvezérlő (DC) értékeli ki a kérelmet, és a megfelelő választ ad vissza (sikeres, sikertelen, a jelszó lejárt, vagy felhasználói zárolva) az ügynök.
10. A hitelesítési ügynök, az Azure AD a választ adja vissza.
11. Azure ad-ben a válasz kiértékeli, és szükség szerint a felhasználó válaszol. Ha például az Azure AD vagy a felhasználó jelentkezik be azonnal, vagy az Azure multi-factor Authentication kér.
12. Ha a felhasználói bejelentkezés sikeres, a felhasználók elérhetik az alkalmazást.

A következő ábra szemlélteti az összes összetevő és a folyamat lépései:

![Átmenő hitelesítés](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>További lépések
- [Aktuális korlátozások](how-to-connect-pta-current-limitations.md): ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [Gyors üzembe helyezési](how-to-connect-pta-quick-start.md): első lépésekhez az Azure AD átmenő hitelesítés.
- [Az AD FS át az átmenő hitelesítés](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) – egy részletes útmutató, amellyel áttelepíteni az átmenő hitelesítés az Active Directory összevonási szolgáltatások (vagy más összevonási technológiákkal).
- [Az intelligens zárolási](../authentication/howto-password-smart-lockout.md): az intelligens zárolás funkciót konfigurálhatja a bérlő felhasználói fiókok védelmét.
- [Gyakran ismételt kérdések](how-to-connect-pta-faq.md): választ találhat a gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): ismerje meg az átmenő hitelesítés szolgáltatás szolgáltatással kapcsolatos gyakori problémák megoldásához.
- [A biztonság részletes bemutatása](how-to-connect-pta-security-deep-dive.md): ismerje meg az átmenő hitelesítés szolgáltatás technikai információit.
- [Az Azure AD közvetlen egyszeri bejelentkezés](how-to-connect-sso.md): További információ a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory-fórumon használatával új funkcióra vonatkozó javaslata fájlt.

