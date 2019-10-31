---
title: Feltételes hozzáférés – MFA megkövetelése minden felhasználó számára – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása, amely megköveteli, hogy minden felhasználó végrehajtsa a többtényezős hitelesítést
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 892171f3e275ebc405bc7a228185ebfabc61acfb
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164834"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Feltételes hozzáférés: MFA megkövetelése minden felhasználó számára

Alex Weinert, a Microsoft Identity Security könyvtára, amely a [PA $ $Word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)blogbejegyzésében említi, nem számít:

> A jelszó nem számít, de az MFA-t is! Tanulmányunk alapján a fiókja több mint 99,9%-kal kevésbé valószínű, ha MFA-t használ.

A cikk útmutatása alapján a szervezet létrehoz egy kiegyensúlyozott MFA-szabályzatot a környezet számára.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, ezért javasoljuk, hogy a szabályzatból kizárja a következő fiókokat:

* **Vészhelyzeti hozzáférés** vagy **megszakítás-Glass** fiókok a bérlői szintű fiókok zárolásának megakadályozása érdekében. Abban az esetben, ha nem valószínű, hogy az összes rendszergazda ki van zárva a bérlőből, a vészhelyzeti hozzáférésű rendszergazdai fiók segítségével bejelentkezhet a bérlőnek a hozzáférés helyreállításához szükséges lépésekkel.
   * További információt a következő cikkben talál: [vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](../users-groups-roles/directory-emergency-access.md).
* **Szolgáltatásfiókok és** **szolgáltatási alapelvek**, például az Azure ad Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nincsenek egy adott felhasználóhoz kötve. Ezeket általában a háttér-szolgáltatások használják, és lehetővé teszik a programozott hozzáférést az alkalmazásokhoz. A szolgáltatási fiókokat ki kell zárni, mert az MFA nem hajtható végre programozott módon.
   * Ha a szervezete ezeket a fiókokat parancsfájlokban vagy kódban használja, érdemes lehet a [felügyelt identitásokkal](../managed-identities-azure-resources/overview.md)helyettesíteni őket. Ideiglenes megkerülő megoldásként kizárhatja ezeket a fiókokat az alapkonfiguráció házirendjéből.

## <a name="application-exclusions"></a>Alkalmazás-kizárások

A szervezeteknek sok Felhőbeli alkalmazása lehet. Előfordulhat, hogy nem mindegyik alkalmazásnak egyenlő a biztonsága. Előfordulhat például, hogy a bérszámfejtési és a részvételi alkalmazások MFA-t igényelnek, de a cafeteria valószínűleg nem. A rendszergazdák dönthetnek úgy, hogy bizonyos alkalmazásokat kizárnak a szabályzatból.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépésekkel létrehozhat egy feltételes hozzáférési szabályzatot, amely a többtényezős hitelesítés végrehajtásához a hozzárendelt rendszergazdai szerepköröket igényli.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory** > **feltételes hozzáférést**.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy a műveletek** > a **következők**közül válassza a **minden felhőalapú alkalmazás**lehetőséget.
   1. A **kizárás**területen válassza ki azokat az alkalmazásokat, amelyek nem igénylik a többtényezős hitelesítést.
1. A **hozzáférés-vezérlés** > a **támogatás**területen válassza a **hozzáférés biztosítása**, a **többtényezős hitelesítés megkövetelése**és a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
