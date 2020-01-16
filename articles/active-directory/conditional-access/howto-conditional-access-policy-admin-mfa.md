---
title: Feltételes hozzáférés – MFA megkövetelése rendszergazdák számára – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása, amely megköveteli a rendszergazdáktól a többtényezős hitelesítés végrehajtását
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e972228923654ca73063aad370d087e550138dbf
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043367"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Feltételes hozzáférés: MFA megkövetelése rendszergazdák számára

A hozzárendelt rendszergazdai jogosultságokkal rendelkező fiókokat a támadók célozzák meg. A többtényezős hitelesítés (MFA) ezen fiókoknál való megkövetelése egyszerűen csökkentheti annak kockázatát, hogy ezek a fiókok veszélyben vannak.

A Microsoft azt javasolja, hogy az MFA-t legalább a következő szerepkörökhöz írja elő:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférésű rendszergazda
* Biztonsági rendszergazda
* Segélyszolgálat (jelszó) rendszergazdája
* Jelszókezelő
* Számlázási rendszergazda
* Felhasználói rendszergazda

A szervezetek dönthetnek úgy, hogy belefoglalják vagy kizárják a szerepköröket.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, ezért javasoljuk, hogy a szabályzatból kizárja a következő fiókokat:

* **Vészhelyzeti hozzáférés** vagy **megszakítás-Glass** fiókok a bérlői szintű fiókok zárolásának megakadályozása érdekében. Abban az esetben, ha nem valószínű, hogy az összes rendszergazda ki van zárva a bérlőből, a vészhelyzeti hozzáférésű rendszergazdai fiók segítségével bejelentkezhet a bérlőnek a hozzáférés helyreállításához szükséges lépésekkel.
   * További információt a következő cikkben talál: [vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](../users-groups-roles/directory-emergency-access.md).
* **Szolgáltatásfiókok és** **egyszerű szolgáltatások**, például a Azure ad Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nincsenek egy adott felhasználóhoz kötve. Ezeket általában a háttér-szolgáltatások használják, és lehetővé teszik a programozott hozzáférést az alkalmazásokhoz. A szolgáltatási fiókokat ki kell zárni, mert az MFA nem hajtható végre programozott módon.
   * Ha a szervezete ezeket a fiókokat parancsfájlokban vagy kódban használja, érdemes lehet a [felügyelt identitásokkal](../managed-identities-azure-resources/overview.md)helyettesíteni őket. Ideiglenes megkerülő megoldásként kizárhatja ezeket a fiókokat az alapkonfiguráció házirendjéből.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépésekkel létrehozhat egy feltételes hozzáférési szabályzatot, amely a többtényezős hitelesítés végrehajtásához a hozzárendelt rendszergazdai szerepköröket igényli.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **címtárbeli szerepkörök (előzetes verzió)** lehetőséget, és legalább a következő szerepköröket válassza:
      * Globális rendszergazda
      * SharePoint-rendszergazda
      * Exchange-rendszergazda
      * Feltételes hozzáférésű rendszergazda
      * Biztonsági rendszergazda
      * Segélyszolgálat rendszergazdája
      * Jelszókezelő
      * Számlázási rendszergazda
      * Felhasználói rendszergazda
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **felhőalapú alkalmazások vagy műveletek** > a **következők**közül válassza a **minden felhőalapú alkalmazás**lehetőséget, majd válassza a **kész**lehetőséget.
1. A **hozzáférés-vezérlés** > a **támogatás**területen válassza a **hozzáférés biztosítása**, a **többtényezős hitelesítés megkövetelése**és a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
