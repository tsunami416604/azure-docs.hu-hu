---
title: Alapvető házirendet az Azure Active Directory-rendszergazdák – a többtényezős hitelesítés megkövetelése
description: Feltételes hozzáférési szabályzat segítségével a rendszergazdák számára a többtényezős hitelesítés megkövetelése
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1ce48126c3e8867ac7f2696d8cf7db992a9a60a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003281"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Alapvető házirendet: MFA megkövetelése rendszergazdáktól

Kiemelt jogosultságú fiókok hozzáféréssel rendelkező felhasználó ugyanolyan korlátlan hozzáféréssel rendelkezik a környezetben. A teljesítmény, a fiókokra, mert kezelje őket gondosan. Egy közös módszerrel történő kiemelt jogosultságú fiókok védelméhez, hogy egy fiókhitelesítés erősebb formája kérése, amikor használhassák jelentkezhetnek be. Az Azure Active Directoryban azzal, hogy a többtényezős hitelesítés (MFA) egy erősebb fiókhitelesítés kaphat.

**Többtényezős hitelesítés a rendszergazdák számára** van egy [alapvető házirendet](concept-baseline-protection.md) , hogy a többtényezős Hitelesítést igényel, és minden alkalommal jelentkezik be a következő emelt szintű rendszergazdai szerepkörök egyike:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférésű rendszergazda
* Biztonsági rendszergazda
* Ügyfélszolgálati adminisztrátor / rendszergazdai jelszó
* Számlázási adminisztrátor
* Felhasználói adminisztrátor

Követően a rendszergazdák házirend szükséges többtényezős hitelesítés engedélyezése, a fenti kilenc rendszergazdai szerepköröket kell regisztrálni a multi-factor Authentication az Authenticator alkalmazás használatával. MFA-regisztráció befejezése után a rendszergazdák hajthatok végre MFA minden egyetlen alkalommal be kell.

![Többtényezős hitelesítés megkövetelése a rendszergazdák alapvető házirendet](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Telepítési szempontok

Mivel a **többtényezős hitelesítés megkövetelése a rendszergazdák számára** házirend vonatkozik az összes kritikus rendszergazdák, különböző szempontok lesz szükség ahhoz, hogy egy zökkenőmentes üzembe helyezési. Ezeket a szempontok közé tartoznak, felhasználók és az Azure AD nem tudja vagy nem kell végrehajtani, az MFA, valamint az alkalmazások és az ügyfelek a szervezet által használt, modern hitelesítést nem támogató szolgáltatás alapelvek azonosító.

### <a name="legacy-protocols"></a>Örökölt protokollok

E-mail az örökölt hitelesítési protokollok (IMAP, SMTP, POP3, stb.) segítségével hitelesítési kéréseket. Ezeket a protokollokat nem támogatja a többtényezős hitelesítés. A Microsoft által látott fiók értelmeznie a legtöbb örökölt protokollok MFA kerülni próbál elleni támadások végrehajtása kártékony elemek okozzák. Győződjön meg arról, hogy többtényezős hitelesítés szükséges egy rendszergazdai fiókkal való bejelentkezéskor és kártékony elemek nincs MFA kerülheti el, ez a szabályzat blokkolja a rendszergazdai fiókokhoz készült örökölt protokollok minden hitelesítési kérelemre.

> [!WARNING]
> Mielőtt engedélyezné ezt a házirendet, győződjön meg arról, a rendszergazdák nem használja az örökölt hitelesítési protokollok. Tekintse meg a cikket [hogyan: Az Azure AD feltételes hozzáférés letiltása a régebbi hitelesítési](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) további információt.

### <a name="user-exclusions"></a>Felhasználói szerepkör kivételei

Ez a alapvető házirend kizárhat felhasználókat lehetőséget biztosít. Mielőtt engedélyezné a szabályzat a bérlőhöz tartozó, javasoljuk, kivéve a következő fiókokat:

* **A vészelérési** vagy **break üvegből** fiókok bérlői szintű fiókzárolás elkerülése érdekében. A rendszergazdák kizárása a bérlő nem valószínű esetben a válságkezelési hozzáférés rendszergazdai fiók segítségével jelentkezzen be a bérlő hajtsa végre a megfelelő lépéseket szereznie.
   * További információ a cikkben található [vészelérési fiókok kezelése az Azure ad-ben](../users-groups-roles/directory-emergency-access.md).
* **Szolgáltatásfiókok** és **alapelvek szolgáltatás**, például az Azure AD Connect szinkronizálási-fiók. Szolgáltatásfiókok olyan nem interaktív fiókokat, amelyek nem kapcsolódnak egy konkrét felhasználóhoz. Ezek a háttér-szolgáltatások által általában használt, és az alkalmazások programozott hozzáférés engedélyezése. Szolgáltatásfiókok ki kell zárni, mivel az MFA programozott módon nem lehet végrehajtani.
   * Ha a szervezete ezeket a fiókokat a parancsfájlokban vagy a kódot használja, fontolja meg, és cserélje le őket az [felügyelt identitások](../managed-identities-azure-resources/overview.md). Ideiglenes Áthidaló megoldásként ezeket a konkrét fiókokat is kizárása az alapvető házirendet.
* Felhasználók, akik nem rendelkeznek, vagy nem fogja tudni használni a okostelefonja.
   * Ez a szabályzat a rendszergazdák számára, hogy a multi-factor Authentication használata a Microsoft Authenticator alkalmazás regisztrálása igényel.

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció-házirend engedélyezése

A szabályzat **alapvető házirendet: Többtényezős hitelesítés a rendszergazdák számára** előre konfigurálva, és jelennek meg az oldal tetején a feltételes hozzáférés paneljén, az Azure Portalon lépve.

A szabályzat engedélyezéséhez, és a rendszergazdák védelmére:

1. Jelentkezzen be a **az Azure portal** globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.
1. Keresse meg a **az Azure Active Directory** > **feltételes hozzáférési**.
1. A házirendek listájából válassza ki **alapvető házirendet: Többtényezős hitelesítés a rendszergazdák számára**.
1. Állítsa be **házirend engedélyezése** való **a házirend azonnal használható**.
1. Bármely felhasználó kizárások hozzáadása kattintva **felhasználók** > **kizárt felhasználók kiválasztása** majd ki kell zárni a felhasználóknak. Kattintson a **kiválasztása** majd **kész**.
1. Kattintson a **mentése**.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Feltételes hozzáférés alapkonfiguráció alkalmazásvédelmi szabályzatok](concept-baseline-protection.md)
* [Öt lépést a személyazonosság-infrastruktúra védelme](../../security/azure-ad-secure-steps.md)
* [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)
