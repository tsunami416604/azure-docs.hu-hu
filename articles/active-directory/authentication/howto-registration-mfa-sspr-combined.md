---
title: Első lépések a kombinált regisztrációval - Azure Active Directory
description: Kombinált Azure AD többtényezős hitelesítés és önkiszolgáló jelszó-alaphelyzetbe állítás regisztrációengedélyezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d9544b1f4dd5ecbf66493f26c373c5502dce68a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451081"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Kombinált biztonsági adatok regisztrálásának engedélyezése az Azure Active Directoryban

A kombinált regisztráció előtt a felhasználók külön-külön regisztrálták az Azure többtényezős hitelesítésés az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit. Az emberek össze voltak zavarodva, hogy hasonló módszereket használtak a többtényezős hitelesítéshez és az SSPR-hez, de mindkét funkcióra regisztrálniuk kellett. Most, kombinált regisztráció, a felhasználók egyszer regisztrálhatnak, és kap az előnyeit mind a többtényezős hitelesítés és SSPR.

Az új felület engedélyezése előtt tekintse át a [Kombinált biztonsági adatok regisztrálását](concept-registration-mfa-sspr-combined.md) című cikket, hogy tisztában legyen a szolgáltatás működésével és hatásaival.

![Egyesített biztonsági adatok regisztrálása továbbfejlesztett tapasztalat](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

Hajtsa végre az alábbi lépéseket a kombinált regisztráció engedélyezéséhez:

1. Jelentkezzen be az Azure Portalon felhasználói vagy globális rendszergazdaként.
2. Nyissa meg az **Azure Active Directory** > **felhasználói beállításai** > **A felhasználói funkciók előnézeti beállításainak kezelése című lapot.**
3. A **Felhasználók csoportban a biztonsági adatok regisztrálására és kezelésére szolgáló előnézeti funkciókat használhat,** válassza a **kijelölt** felhasználói csoport vagy az **összes** felhasználó engedélyezését.

   ![A kombinált biztonsági adatok előnézeti verziójának engedélyezése minden felhasználó számára](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Miután engedélyezte a kombinált regisztrációt, azok a felhasználók, akik regisztrálják vagy megerősítik telefonszámukat vagy mobilalkalmazásukat az új felületen keresztül, használhatják őket a többtényezős hitelesítéshez és az SSPR-hez, ha ezek a módszerek engedélyezve vannak a többtényezős hitelesítés és az SSPR-házirendekben. Ha ezt az élményt letiltja, az előző SSPR regisztrációs oldalra `https://aka.ms/ssprsetup` lépő felhasználóknak többtényezős hitelesítést kell végrehajtaniuk, mielőtt hozzáférhetnének az oldalhoz.

Ha az Internet Explorer ben konfigurálta a Hely a zónához hozzárendelési listát, a következő helyeknek ugyanabban a zónában kell lenniük:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Feltételes hozzáférés házirendjei a kombinált regisztrációhoz

A feltételes hozzáférési szabályzat felhasználói lépéseivel biztosíthatja, hogy mikor és hogyan regisztrálhassanak a felhasználók az Azure többtényezős hitelesítésre és az önkiszolgáló jelszó-visszaállításra. Ez a funkció azon szervezetek számára érhető el, amelyek engedélyezték a [kombinált regisztrációs funkciót.](../authentication/concept-registration-mfa-sspr-combined.md) Ez a funkció engedélyezhető olyan szervezetekben, ahol azt szeretnék, hogy a felhasználók regisztráljanak az Azure többtényezős hitelesítésre és az SSPR-re egy központi helyről, például egy megbízható hálózati helyről a HR-bevezetés során. A feltételes hozzáférésben a megbízható helyek létrehozásáról további információt a [Mi a helyfeltétel az Azure Active Directory feltételes hozzáférésében?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Házirend létrehozása, amely megköveteli a regisztrációt egy megbízható helyről

A következő házirend minden kiválasztott felhasználóra vonatkozik, akik a kombinált regisztrációs felület használatával próbálnak regisztrálni, és letiltja a hozzáférést, kivéve, ha megbízható hálózatként megjelölt helyről csatlakoznak.

![Hitelesítésbiztosítási házirend létrehozása a biztonsági adatok regisztrációjának szabályozásához](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Az **Azure Portalon**keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését**
1. Válassza az **Új szabályzat** lehetőséget.
1. A Név mezőbe írja be a házirend nevét. Például **a kombinált biztonsági adatok regisztrálása megbízható hálózatokon**
1. A **Hozzárendelések**csoportban kattintson a **Felhasználók és csoportok**elemre, és jelölje ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné ezt a házirendet.

   > [!WARNING]
   > A felhasználókat engedélyezni kell a [kombinált regisztrációhoz.](../authentication/howto-registration-mfa-sspr-combined.md)

1. A **Felhőalapú alkalmazások vagy műveletek csoportban**válassza a **Felhasználói műveletek**lehetőséget, jelölje be a Biztonsági információk **regisztrálása (előzetes verzió) jelölőnégyzetet.**
1. **Feltételek mellett** > **helyszínek**
   1. **Igen** konfigurálása
   1. **Bármilyen hely belefoglalása**
   1. **Az összes megbízható hely kizárása**
   1. Kattintson a Helyek panelen a **Kész** gombra.
   1. Kattintson a **Kész** gombra a Feltételek panelen
1. A **Hozzáférés vezérlők csoportban** > **A támogatás**
   1. Kattintson **a Hozzáférés blokkolása gombra**
   1. Ezután kattintson **a Kijelölés gombra.**
1. Házirend **engedélyezése** **be**
1. Ezután kattintson a **Létrehozás gombra.**

## <a name="next-steps"></a>További lépések

Ha segítségre van szüksége, olvassa el, hogyan [háríthatja el a kombinált biztonsági adatok regisztrációját,](howto-registration-mfa-sspr-combined-troubleshoot.md) vagy olvassa el [A helyfeltétel az Azure Active Directory feltételes hozzáférésében?](../conditional-access/location-condition.md)

Az Azure AD-bérlő funkcióinak engedélyezéséhez tekintse meg az [önkiszolgáló jelszó-visszaállítás engedélyezésével](tutorial-enable-sspr.md) és az [Azure többtényezős hitelesítés engedélyezésével](tutorial-enable-azure-mfa.md)kapcsolatos oktatóanyagokat.

Megtudhatja, hogy [miként engedélyezheti a kombinált regisztrációt a bérlőben,](howto-registration-mfa-sspr-combined.md) illetve [hogyan kényszerítheti a felhasználókat a hitelesítési módszerek újbóli regisztrálására.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

Az [Azure többtényezős hitelesítéshez és az SSPR-hez rendelkezésre álló módszereket](concept-authentication-methods.md)is áttekintheti.
