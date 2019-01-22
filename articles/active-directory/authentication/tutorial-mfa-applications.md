---
title: Az Azure Multi-Factor Authentication próbaverziójának engedélyezése
description: Az oktatóanyagban az Azure AD Multi-Factor Authenticationt engedélyezzük a felhasználók egy tesztcsoportja számára.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 5bd27ed71e93edc10fd86395121c524cb54c98e7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427610"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Oktatóanyag: Végezze el az Azure multi-factor Authentication kísérleti bevezetés

Az oktatóanyag végigvezeti egy olyan feltételes hozzáférési szabályzat konfigurálásán, amely engedélyezi az Azure Multi-Factor Authenticationt (Azure MFA-t) az Azure Portalra való bejelentkezéskor. A szabályzat bevezetése és tesztelése tesztfelhasználók egy adott csoportján történik. Az Azure MFA bevezetése a feltételes hozzáféréssel a hagyományos kikényszerítéses módszerrel szemben hatalmas rugalmasságot biztosít a vállalatok és a rendszergazdák számára.

> [!div class="checklist"]
> * Az Azure Multi-Factor Authentication engedélyezése
> * Az Azure Multi-Factor Authentication tesztelése

## <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
* Egy globális rendszergazdai jogosultsággal rendelkező fiók.
* Egy nem rendszergazdai tesztfelhasználó ismeri a tesztelésre, ha egy felhasználó létrehozásához szükséges jelszóval, tekintse meg a cikket [a rövid útmutató: Új felhasználók hozzáadása az Azure Active Directory](../add-users-azure-active-directory.md).
* Egy tesztcsoport a teszteléshez, amelynek a nem rendszergazdai szintű felhasználó a tagja. Ha létre kell hoznia a csoportot, tekintse meg a [csoport létrehozását és a tagok hozzáadását az Azure Active Directoryban](../active-directory-groups-create-azure-portal.md) ismertető cikket.

## <a name="enable-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. Lépjen az **Azure Active Directory**, **Feltételes hozzáférés** területre.
1. Válassza az **Új szabályzat** lehetőséget.
1. Adja az **MFA teszt** nevet a szabályzatnak.
1. A **Felhasználók és csoportok** területen válassza a **Felhasználók és csoportok kiválasztása** választógombot.
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
1. Kattintson a **Create** (Létrehozás) gombra

## <a name="test-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication tesztelése

Annak ellenőrzéséhez, hogy a feltételes hozzáférési szabályzat működik-e, próbáljon bejelentkezni egy olyan erőforrásba, amely nem igényel többtényezős hitelesítést, majd az Azure Portalra, amely azonban igen.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Jelentkezzen be a cikk előfeltételeket ismertető szakaszában létrehozott tesztfelhasználóval. Ekkor a rendszer nem igényel többtényezős hitelesítést.
   * Zárja be a böngészőablakot.
2. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://portal.azure.com](https://portal.azure.com).
   * Jelentkezzen be a cikk előfeltételeket ismertető szakaszában létrehozott tesztfelhasználóval. Most regisztrálnia kell az Azure Multi-Factor Authenticationre, és használnia kell azt.
   * Zárja be a böngészőablakot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót a továbbiakban már nem szeretné használni, végezze el a következő módosításokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen az **Azure Active Directory**, **Feltételes hozzáférés** területre.
1. Válassza ki a létrehozott feltételes hozzáférési szabályzatot.
1. Kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban engedélyezte az Azure Multi-Factor Authenticationt. A következő oktatóanyagból megtudhatja, hogyan integrálható az Azure Identity Protection az új jelszó önkiszolgáló kérésével és a többtényezős hitelesítéssel.

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)