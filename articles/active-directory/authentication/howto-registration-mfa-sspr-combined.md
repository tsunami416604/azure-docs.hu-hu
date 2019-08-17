---
title: Az Azure AD SSPR és a többtényezős hitelesítés (előzetes verzió) együttes regisztrációjának első lépései – Azure Active Directory
description: A kombinált Azure AD multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási regisztráció engedélyezése (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fc30c3273528b8cbc08681950e0bd5f03ec7890
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69561271"
---
# <a name="enable-combined-security-information-registration-preview"></a>A kombinált biztonsági információk regisztrálásának engedélyezése (előzetes verzió)

Az új felület engedélyezése előtt tekintse át a [kombinált biztonsági információ regisztrálása (előzetes verzió)](concept-registration-mfa-sspr-combined.md) című cikket a funkció működésének és hatásának megismerése érdekében.

![Összetett biztonsági információk regisztrációjának továbbfejlesztett felhasználói felülete](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Az Azure multi-Factor Authentication és a Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás szolgáltatásának együttes biztonsági információinak regisztrálása az Azure AD nyilvános előzetes verziója. Az előzetes verziókra vonatkozó további információért lásd: [Kiegészítő Használati Feltételek a Microsoft Azure Előzetesekhez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Azoknak a szervezeteknek, akik engedélyezték az előző előzetes verziót a biztonsági adatok regisztrálásához és kezeléséhez, az alábbi lépéseket kell elvégezniük a bővített előzetes verzió használatának engedélyezéséhez. Azon szervezetek esetében, akik nem teszik a kapcsolót, a Microsoft 2019. szeptember 25-én átváltja az előző előzetes verzió felhasználóit a biztonsági adatok továbbfejlesztett felhasználói felületre való regisztrálásához és kezeléséhez. 
> 
> Ha nem engedélyezte az előzetes verzió bármely verzióját, a szervezet nem lesz hatással.

## <a name="enable-combined-registration"></a>A kombinált regisztráció engedélyezése

A következő lépések végrehajtásával engedélyezheti a kombinált regisztrációt:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként vagy globális rendszergazdaként.
2. Nyissa meg **Azure Active Directory** > **felhasználói beállítások** > **kezelése beállítások a hozzáférési panel előzetes**verziójának funkcióit.
3. A **felhasználók a biztonsági adatok regisztrálásához és kezeléséhez használható előzetes funkciókat használhatják – a frissítés**lehetőséget választva engedélyezheti a felhasználók vagy az **összes** felhasználó csoportját.

   ![A kombinált biztonsági információk előzetes verziójának használatának engedélyezése az összes felhasználó számára](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A 2019-as naptól kezdve a telefonhívási lehetőségek nem lesznek elérhetők a többtényezős hitelesítés és a SSPR felhasználók számára ingyenes/próbaverziós Azure AD-bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonos hívási lehetőségek továbbra is elérhetők lesznek a fizetős Azure AD-bérlők felhasználói számára.

> [!NOTE]
> A kombinált regisztráció engedélyezése után azok a felhasználók, akik az új felhasználói felületen regisztrálják vagy megerősítik a telefonszámot vagy a mobil alkalmazást, használhatják a többtényezős hitelesítést és a SSPR, ha ezek a módszerek engedélyezve vannak a multi-Factor Authentication és a SSPR szabályzatok. Ha ezt követően letiltja ezt a felhasználói élményt, a többtényezős hitelesítés elvégzéséhez `https://aka.ms/ssprsetup` a (z) oldalon az előző SSPR regisztrációs oldalra kell lépnie, mielőtt hozzá tudnak férni a laphoz.

Ha az Internet Explorerben konfigurálta a hely-hozzárendelési listát, a következő helyeknek ugyanabban a zónában kell lenniük:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Feltételes hozzáférési szabályzatok a kombinált regisztrációhoz

Az Azure multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználó általi regisztrálásának engedélyezése a felhasználók számára a feltételes hozzáférési házirend felhasználói műveleteivel. Ez az előzetes verziójú funkció olyan szervezetek számára érhető el, akik engedélyezték a [közös regisztráció előzetes](../authentication/concept-registration-mfa-sspr-combined.md)verzióját. Ez a funkció olyan szervezeteknél engedélyezhető, amelyekben a felhasználóknak regisztrálniuk kell az Azure multi-Factor Authentication szolgáltatásban, illetve a SSPR egy központi helyről, például egy megbízható hálózati helyről a HR bevezetése során. A megbízható helyek feltételes hozzáférésben való létrehozásával kapcsolatos további információkért tekintse meg a következő cikket: [Mi a hely feltétele a feltételes hozzáférés Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása megbízható helyről való regisztráció megköveteléséhez

A következő házirend az összes kijelölt felhasználóra vonatkozik, akik a közös regisztrációs élmény használatával próbálnak regisztrálni, és blokkolja a hozzáférést, kivéve, ha egy megbízható hálózatként megjelölt helyről csatlakoznak.

![HITELESÍTÉSSZOLGÁLTATÓI szabályzat létrehozása a biztonsági adatok regisztrálásának vezérléséhez](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. A **Azure Portal**tallózással keresse meg **Azure Active Directory** > **feltételes hozzáférést**
1. Válassza az **Új szabályzat** lehetőséget.
1. A név mezőben adja meg a szabályzat nevét. Például **a megbízható hálózatokon található kombinált biztonsági adatok regisztrálása**
1. A **hozzárendelések**területen kattintson a **felhasználók és csoportok**elemre, és válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot

   > [!WARNING]
   > A felhasználók számára engedélyezni kell a [kombinált regisztráció előzetes](../authentication/howto-registration-mfa-sspr-combined.md)verzióját.

1. A **Cloud apps vagy műveletek**területen válassza a **felhasználói műveletek**lehetőséget, és jelölje be a **biztonsági adatok regisztrálása (előzetes verzió)** jelölőnégyzetet.
1. **Feltételek**helye > 
   1. Az **Igen** beállítása
   1. **Bármely hely** belefoglalása
   1. **Az összes megbízható helyszín** kizárása
   1. Kattintson a **kész** gombra a helyszínek panelen
   1. A feltételek panelen kattintson a **kész** gombra.
1. A **hozzáférés-vezérlés** > **megadása** területen
   1. Kattintson a **hozzáférés letiltása** elemre.
   1. Ezután kattintson a **kiválasztás** elemre.
1. **Házirend engedélyezése** bekapcsolva értékre
1. Ezután kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

[Elérhető módszerek a többtényezős hitelesítéshez és a SSPR](concept-authentication-methods.md)

[Önkiszolgáló jelszó-visszaállítás konfigurálása](howto-sspr-deployment.md)

[Az Azure multi-Factor Authentication konfigurálása](howto-mfa-getstarted.md)

[A kombinált biztonsági adatok regisztrálásának hibaelhárítása](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Mi a hely feltétele a feltételes hozzáférés Azure Active Directory?](../conditional-access/location-condition.md)
