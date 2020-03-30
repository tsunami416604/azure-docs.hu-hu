---
title: Felhasználói élmény az Azure AD Identity Protection szolgáltatással
description: Az Azure AD-identitásvédelem felhasználói élménye
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886999"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Felhasználói élmény az Azure AD Identity Protection szolgáltatással

Az Azure Active Directory identity Protection segítségével a következőket teheti:

* A felhasználók regisztrációjának megkövetelése az Azure többtényezős hitelesítéséhez (MFA)
* A kockázatos bejelentkezések és a sérült felhasználók helyreállításának automatizálása

Az identitásvédelem minden szabályzata hatással van a felhasználók bejelentkezési élményére. Ha lehetővé teszi a felhasználók számára, hogy regisztráljanak és használjanak olyan eszközöket, mint az Azure MFA és az önkiszolgáló jelszó-visszaállítás, csökkentheti a hatást. Ezek az eszközök a megfelelő házirend-választásokkal együtt önszervizelési lehetőséget biztosítanak a felhasználóknak, amikor szükségük van rá.

## <a name="multi-factor-authentication-registration"></a>Többtényezős hitelesítés regisztrálása

A többtényezős hitelesítésregisztrációt igénylő és az összes felhasználó célzását igénylő identitásvédelmi szabályzat engedélyezése biztosítja, hogy a jövőben az Azure MFA használatával is használhatók legyenek. A házirend konfigurálása 14 napos időszakot biztosít a felhasználóknak, ahol választhatnak a regisztrációt, és a végén kénytelenek regisztrálni. A felhasználók élményét az alábbiakban ismertetjük. További információ a végfelhasználói dokumentációban található a cikkben, a [Kétfaktoros ellenőrzés áttekintése és a munkahelyi vagy iskolai fiók.](../user-help/user-help-two-step-verification-overview.md)

### <a name="registration-interrupt"></a>Regisztrációs megszakítás

1. Bármely Azure AD-integrált alkalmazásba való bejelentkezéskor a felhasználó értesítést kap a többtényezős hitelesítéshez szükséges fiók beállításának követelményéről. Ez a házirend a Windows 10 in-box élményben is megjelenik az új eszközzel rendelkező új felhasználók számára.
   
    ![További információ szükséges](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Hajtsa végre az Azure többtényezős hitelesítésre való regisztrációhoz és a bejelentkezés befejezéséhez szükséges interaktív lépéseket.

## <a name="risky-sign-in-remediation"></a>Kockázatos bejelentkezési szervizelés

Ha a rendszergazda házirendet konfigurált a bejelentkezési kockázatokhoz, az érintett felhasználók értesítést kapnak, amikor megpróbálnak bejelentkezni, és elindítják a házirendek kockázati szintjét. 

### <a name="risky-sign-in-self-remediation"></a>Kockázatos bejelentkezési önszervizelés

1. A felhasználó értesítést kap arról, hogy valami szokatlant észlelt a bejelentkezéssel kapcsolatban, például egy új helyről, eszközről vagy alkalmazásból való bejelentkezést.
   
    ![Valami szokatlan gyors](./media/concept-identity-protection-user-experience/120.png)

1. A felhasználónak igazolnia kell identitását az Azure MFA kitöltésével a korábban regisztrált módszerek egyikével. 

### <a name="risky-sign-in-administrator-unblock"></a>Kockázatos bejelentkezési rendszergazda feloldása

A rendszergazdák dönthetnek úgy, hogy letiltják a felhasználókat a bejelentkezéskor a kockázati szintjüktől függően. A zárolás feloldásához a végfelhasználóknak kapcsolatba kell lépniük az informatikai személyzettel, vagy megpróbálhatnak bejelentkezni egy ismerős helyről vagy eszközről. Ebben az esetben a többtényezős hitelesítés sel végzett önjavítás nem lehetséges.

![A bejelentkezési kockázati szabályzat blokkolja](./media/concept-identity-protection-user-experience/200.png)

Az informatikai személyzet a [Felhasználók blokkolásának feloldása](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) című szakaszutasításait követve engedélyezheti a felhasználók számára a bejelentkezést.

## <a name="risky-user-remediation"></a>Kockázatos felhasználói szervizelés

Ha egy felhasználói kockázati házirend van konfigurálva, a felhasználók, akik megfelelnek a felhasználói kockázati szint valószínűsége a biztonság a biztonság érdekében kell keresztülmennie a felhasználó által feltört helyreállítási folyamat, mielőtt azok bejelentkezéshez. 

### <a name="risky-user-self-remediation"></a>Kockázatos felhasználói önszervizelés

1. A felhasználó értesítést kap arról, hogy a fiók biztonsága veszélyben van a gyanús tevékenység vagy a kiszivárgott hitelesítő adatok miatt.
   
    ![Kockázatcsökkentés](./media/concept-identity-protection-user-experience/101.png)

1. A felhasználónak igazolnia kell identitását az Azure MFA kitöltésével a korábban regisztrált módszerek egyikével. 
1. Végül a felhasználó kénytelen megváltoztatni a jelszavát az önkiszolgáló jelszó-visszaállítás sal, mivel valaki más hozzáférhetett a fiókjához.

## <a name="risky-sign-in-administrator-unblock"></a>Kockázatos bejelentkezési rendszergazda feloldása

A rendszergazdák dönthetnek úgy, hogy letiltják a felhasználókat a bejelentkezéskor a kockázati szintjüktől függően. A zárolás feloldásához a végfelhasználóknak kapcsolatba kell lépniük informatikai személyzetükkel. Ebben az esetben a többtényezős hitelesítés és az önkiszolgáló jelszó-visszaállítás végrehajtásával végzett önjavítás nem lehetséges.

![Felhasználói kockázati házirend blokkolja](./media/concept-identity-protection-user-experience/104.png)

Az informatikai személyzet a [Felhasználók blokkolásának feloldása](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) című szakaszutasításait követve engedélyezheti a felhasználók számára a bejelentkezést.

## <a name="see-also"></a>Lásd még

- [Kockázatok elhárítása és a felhasználók blokkolásának feloldása](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory identitásvédelem](../active-directory-identityprotection.md) 
