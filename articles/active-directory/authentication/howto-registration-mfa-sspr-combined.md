---
title: Ismerkedés a kombinált regisztrációval – Azure Active Directory
description: A kombinált Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási regisztráció engedélyezése
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639676"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>A kombinált biztonsági információk regisztrációjának engedélyezése a Azure Active Directoryban

A kombinált regisztráció előtt a felhasználók az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit külön-külön regisztrálták. Az emberek zavarosak voltak, hogy hasonló módszerek voltak használatban Multi-Factor Authentication és SSPR, de mindkét szolgáltatáshoz regisztrálniuk kellett őket. A kombinált regisztráció révén a felhasználók egyszer regisztrálhatnak, és a Multi-Factor Authentication és a SSPR előnyeit is igénybe vehetik.

Az új felhasználói felület engedélyezése előtt tekintse át a [biztonsági információkkal kapcsolatos részletes regisztrációt](concept-registration-mfa-sspr-combined.md) , hogy megértse a funkció funkcióit és hatásait.

![Összetett biztonsági információk regisztrációjának továbbfejlesztett felhasználói felülete](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

A következő lépések végrehajtásával engedélyezheti a kombinált regisztrációt:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként vagy globális rendszergazdaként.
2. Nyissa meg **Azure Active Directory** > **felhasználói beállítások** > **kezelése felhasználói szolgáltatás előzetes**verziójának beállításait.
3. A **felhasználók a biztonsági adatok regisztrálásához és kezeléséhez**használhatják az előzetes verziójú funkciókat, így a **kiválasztott** felhasználók vagy az **összes** felhasználó számára engedélyezhető.

   ![A kombinált biztonsági információk előzetes verziójának használatának engedélyezése az összes felhasználó számára](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> A kombinált regisztráció engedélyezése után azok a felhasználók, akik az új felhasználói felületen regisztrálják vagy megerősítik a telefonszámot vagy a mobil alkalmazást, használhatják Multi-Factor Authentication és SSPR, ha ezek a módszerek engedélyezve vannak a Multi-Factor Authentication és a SSPR házirendben. Ha ezt követően letiltja ezt a felhasználói élményt, a többtényezős hitelesítés elvégzéséhez `https://aka.ms/ssprsetup` a (z) oldalon az előző SSPR regisztrációs oldalra kell lépnie, mielőtt hozzá tudnak férni a laphoz.

Ha az Internet Explorerben konfigurálta a hely-hozzárendelési listát, a következő helyeknek ugyanabban a zónában kell lenniük:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Feltételes hozzáférési szabályzatok a kombinált regisztrációhoz

Az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználó általi regisztrálásának engedélyezése a felhasználói műveletekhez a feltételes hozzáférési házirendben. Ez a funkció olyan szervezetek számára érhető el, akik engedélyezték a [kombinált regisztráció funkciót](../authentication/concept-registration-mfa-sspr-combined.md). Ez a funkció olyan szervezeteknél engedélyezhető, amelyekben a felhasználóknak regisztrálniuk kell az Azure Multi-Factor Authentication és SSPR egy központi helyről, például egy megbízható hálózati helyről az HR bevezetése során.

A megbízható helyek feltételes hozzáférésben való létrehozásával kapcsolatos további információkért tekintse meg a következő cikket: [Mi a hely feltétele a feltételes hozzáférés Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása megbízható helyről való regisztráció megköveteléséhez

A következő házirend az összes olyan kijelölt felhasználóra vonatkozik, amely az egyesített regisztrációs élmény használatával próbálkozik, és blokkolja a hozzáférést, kivéve, ha egy megbízható hálózatként megjelölt helyről csatlakozik.

1. A **Azure Portal**keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés** lehetőséget.
1. Válassza az **+ új szabályzat** lehetőséget
1. Adja meg a szabályzat nevét, például: *kombinált biztonsági adatok regisztrálása megbízható hálózatokon*.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget. Válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot, majd válassza a **kész**lehetőséget.

   > [!WARNING]
   > A felhasználók számára engedélyezni kell a kombinált regisztrációt.

1. A **Cloud apps vagy műveletek**területen válassza a **felhasználói műveletek**lehetőséget. Jelölje be a **biztonsági információk regisztrálása**jelölőnégyzetet, majd kattintson a **kész**gombra.

    ![Feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrálásának vezérléséhez](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. A **feltételek** > **helye**területen adja meg a következő beállításokat:
   1. Az **Igen** beállítása
   1. **Bármely hely** belefoglalása
   1. **Az összes megbízható helyszín** kizárása
1. Válassza a **kész** lehetőséget a *helyszínek* ablakban, majd a *feltételek* ablakban válassza a **kész** lehetőséget.
1. A **hozzáférés-vezérlés** > **megadása**területen válassza a **hozzáférés letiltása**lehetőséget, majd **válassza** a
1. **Házirend engedélyezése** **bekapcsolva** értékre
1. A szabályzat véglegesítéséhez válassza a **Létrehozás** lehetőséget.

## <a name="next-steps"></a>További lépések

Ha segítségre van szüksége, olvassa el a következő témakört: a [kombinált biztonsági adatok regisztrációjának hibaelhárítása](howto-registration-mfa-sspr-combined-troubleshoot.md) vagy [a Azure Active Directory feltételes hozzáférésre vonatkozó hely feltételének](../conditional-access/location-condition.md) megismerése

Az Azure AD-bérlő szolgáltatásainak engedélyezéséhez tekintse meg az oktatóanyagokat az [önkiszolgáló jelszó-visszaállítás engedélyezéséhez](tutorial-enable-sspr.md) és az [Azure-multi-Factor Authentication engedélyezéséhez](tutorial-enable-azure-mfa.md).

Megtudhatja, hogyan [engedélyezheti a kombinált regisztrációt a bérlőben](howto-registration-mfa-sspr-combined.md) , vagy [kényszerítheti a felhasználókat a hitelesítési módszerek ismételt regisztrálására](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Áttekintheti az [Azure multi-Factor Authentication és a SSPR elérhető metódusait](concept-authentication-methods.md)is.
