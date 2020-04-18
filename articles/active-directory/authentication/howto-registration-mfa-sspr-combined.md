---
title: Első lépések a kombinált regisztrációval - Azure Active Directory
description: Kombinált Azure AD többtényezős hitelesítés és önkiszolgáló jelszó-alaphelyzetbe állítás regisztrációengedélyezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639676"
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

A feltételes hozzáférési szabályzat felhasználói lépéseivel biztosíthatja, hogy mikor és hogyan regisztrálhassanak a felhasználók az Azure többtényezős hitelesítésre és az önkiszolgáló jelszó-visszaállításra. Ez a funkció azon szervezetek számára érhető el, amelyek engedélyezték a [kombinált regisztrációs funkciót.](../authentication/concept-registration-mfa-sspr-combined.md) Ez a funkció engedélyezhető olyan szervezetekben, ahol azt szeretnék, hogy a felhasználók regisztráljanak az Azure többtényezős hitelesítésre és az SSPR-re egy központi helyről, például egy megbízható hálózati helyről a HR-bevezetés során.

A feltételes hozzáférésben a megbízható helyek létrehozásáról további információt a [Mi a helyfeltétel az Azure Active Directory feltételes hozzáférésében?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Házirend létrehozása, amely megköveteli a regisztrációt egy megbízható helyről

A következő házirend minden olyan kiválasztott felhasználóra vonatkozik, aki a kombinált regisztrációs felület használatával próbál regisztrálni, és letiltja a hozzáférést, kivéve, ha megbízható hálózatként megjelölt helyről csatlakozik.

1. Az **Azure Portalon**keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését**
1. Válassza **ki a + Új házirendet**
1. Adja meg a házirend nevét, például *a Kombinált biztonsági adatok regisztrációját a megbízható hálózatokon.*
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget. Válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné ezt a házirendet, majd válassza a **Kész gombot.**

   > [!WARNING]
   > A felhasználókat engedélyezni kell a kombinált regisztrációhoz.

1. A **Felhőalapú alkalmazások és műveletek csoportban**válassza a **Felhasználói műveletek lehetőséget.** Jelölje **be a Biztonsági információk regisztrálása**jelölőnégyzetet, majd válassza a **Kész**lehetőséget.

    ![Feltételes hozzáférési házirend létrehozása a biztonsági adatok regisztrációjának szabályozásához](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. A **Feltételek** > **helyek csoportban**adja meg a következő beállításokat:
   1. **Igen** konfigurálása
   1. **Bármilyen hely belefoglalása**
   1. **Az összes megbízható hely kizárása**
1. Válassza a *Helyek* ablak **Kész** lehetőséget, majd a Feltételek ablakban válassza a **Kész** *lehetőséget.*
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférés blokkolása**lehetőséget, majd a Kijelölés **lehetőséget.**
1. Házirend **engedélyezése** **be**
1. A házirend véglegesítéséhez válassza a **Létrehozás gombot.**

## <a name="next-steps"></a>További lépések

Ha segítségre van szüksége, olvassa el, hogyan [háríthatja el a kombinált biztonsági adatok regisztrációját,](howto-registration-mfa-sspr-combined-troubleshoot.md) vagy olvassa el [A helyfeltétel az Azure Active Directory feltételes hozzáférésében?](../conditional-access/location-condition.md)

Az Azure AD-bérlő funkcióinak engedélyezéséhez tekintse meg az [önkiszolgáló jelszó-visszaállítás engedélyezésével](tutorial-enable-sspr.md) és az [Azure többtényezős hitelesítés engedélyezésével](tutorial-enable-azure-mfa.md)kapcsolatos oktatóanyagokat.

Megtudhatja, hogy [miként engedélyezheti a kombinált regisztrációt a bérlőben,](howto-registration-mfa-sspr-combined.md) illetve [hogyan kényszerítheti a felhasználókat a hitelesítési módszerek újbóli regisztrálására.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

Az [Azure többtényezős hitelesítéshez és az SSPR-hez rendelkezésre álló módszereket](concept-authentication-methods.md)is áttekintheti.
