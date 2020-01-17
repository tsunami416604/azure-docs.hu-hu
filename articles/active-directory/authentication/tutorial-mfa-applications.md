---
title: Az Azure Multi-Factor Authentication próbaverziójának engedélyezése
description: Az oktatóanyagban az Azure AD Multi-Factor Authenticationt engedélyezzük a felhasználók egy tesztcsoportja számára.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68474738aabde1b14752aa33789d7e40c3831908
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154839"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Oktatóanyag: Az Azure Multi-Factor Authentication próbaverziójának bevezetése

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, amely lehetővé teszi az Azure Multi-Factor Authentication (Azure MFA) használatát a Azure Portalba való bejelentkezéskor. A szabályzat bevezetése és tesztelése tesztfelhasználók egy adott csoportján történik. Az Azure MFA feltételes hozzáférés használatával történő üzembe helyezése jelentős rugalmasságot biztosít a szervezetek és a rendszergazdák számára a hagyományosan kényszerített módszerhez képest.

> [!div class="checklist"]
> * Az Azure Multi-Factor Authentication engedélyezése
> * Az Azure Multi-Factor Authentication tesztelése

## <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
* Egy globális rendszergazdai jogosultsággal rendelkező fiók.
* Egy nem rendszergazdai szintű tesztfelhasználó ismert jelszóval a teszteléshez. Ha létre kell hoznia a felhasználót, tekintse meg az [új felhasználók az Azure Active Directoryhoz történő hozzáadását ismertető rövid útmutatót](../add-users-azure-active-directory.md).
* Egy tesztcsoport a teszteléshez, amelynek a nem rendszergazdai szintű felhasználó a tagja. Ha létre kell hoznia a csoportot, tekintse meg a [csoport létrehozását és a tagok hozzáadását az Azure Active Directoryban](../active-directory-groups-create-azure-portal.md) ismertető cikket.

## <a name="enable-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés** keresése
1. Válassza az **Új szabályzat** lehetőséget.
1. Adja az **MFA teszt** nevet a szabályzatnak.
1. A **felhasználók és csoportok**területen válassza a **felhasználók és csoportok kiválasztása** választógombot.
    * Válassza ki a cikk előfeltételeket ismertető szakaszában létrehozott tesztcsoportot.
    * Kattintson a **Kész** gombra.
1. A **Felhőalkalmazások** területen válassza az **Alkalmazások kiválasztása** választógombot.
    * Az Azure Portal felhőalkalmazása a **Microsoft Azure Management** alkalmazás.
    * Kattintson a **Kiválasztás** gombra.
    * Kattintson a **Kész** gombra.
1. Hagyja ki a **Feltételek** szakaszt.
1. A **Hozzáférés** területen győződjön meg róla, hogy a **Hozzáférés biztosítása** választógomb van kiválasztva.
    * Jelölje be a **Többtényezős hitelesítés megkövetelése** jelölőnégyzetet.
    * Kattintson a **Kiválasztás** gombra.
1. Hagyja ki a **Munkamenet** szakaszt.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Kattintson a  **Create** (Létrehozás) gombra

## <a name="test-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication tesztelése

A feltételes hozzáférési szabályzat működésének bizonyításához tesztelje a bejelentkezést olyan erőforrásra, amely nem igényel MFA-t, majd az MFA-t igénylő Azure Portal.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Jelentkezzen be a cikk előfeltételeket ismertető szakaszában létrehozott tesztfelhasználóval. Ekkor a rendszer nem igényel többtényezős hitelesítést.
   * Zárja be a böngészőablakot.
2. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://portal.azure.com](https://portal.azure.com).
   * Jelentkezzen be a cikk előfeltételeket ismertető szakaszában létrehozott tesztfelhasználóval. Most regisztrálnia kell az Azure Multi-Factor Authenticationre, és használnia kell azt.
   * Zárja be a böngészőablakot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót a továbbiakban már nem szeretné használni, végezze el a következő módosításokat.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza ki a létrehozott feltételes hozzáférési szabályzatot.
1. Kattintson a **Delete** (Törlés) gombra.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban engedélyezte az Azure Multi-Factor Authenticationt. A következő oktatóanyagból megtudhatja, hogyan integrálható az Azure Identity Protection az új jelszó önkiszolgáló kérésével és a többtényezős hitelesítéssel.

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)
