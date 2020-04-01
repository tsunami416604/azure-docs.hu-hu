---
title: Az Azure Multi-Factor Authentication engedélyezése
description: Ebben az oktatóanyagban megtudhatja, hogyan engedélyezheti az Azure többtényezős hitelesítést egy felhasználói csoport számára, és hogyan tesztelheti a másodlagos tényezőparancs-kérdést egy bejelentkezési esemény során.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77154822"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Oktatóanyag: Biztonságos felhasználói bejelentkezési események az Azure többtényezős hitelesítésével

A többtényezős hitelesítés (MFA) egy olyan folyamat, amelynek során a felhasználó további azonosítási formákra vonatkozó bejelentkezési eseményre kéri a felhasználót. Ez a gyors lehet, hogy adjon meg egy kódot a mobiltelefonján, vagy hogy egy ujjlenyomat-vizsgálat. Ha a hitelesítés második formájára van szüksége, a biztonság megnő, mivel ez a további tényező nem olyan dolog, amelyet a támadó könnyen beszerezhet vagy lemásolhat.

Az Azure többtényezős hitelesítési és feltételes hozzáférési szabályzatok rugalmasságot biztosítanak az MFA-k számára a felhasználók számára adott bejelentkezési események során.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Feltételes hozzáférési szabályzat létrehozása az Azure többtényezős hitelesítésének engedélyezéséhez felhasználók egy csoportjához
> * Az MFA-t kérdező házirend-feltételek konfigurálása
> * Az MFA-folyamat tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Működő Azure AD-bérlő, amelyen engedélyezve van az Azure AD Premium vagy a próbalicenc.
    * Szükség esetén [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Nem rendszergazdai felhasználó, olyan jelszóval, mint például *a tesztfelhasználó.* Tesztelje a végfelhasználói Azure többtényezős hitelesítési élményt ebben a fiókban ebben az oktatóanyagban.
    * Ha felhasználót kell létrehoznia, olvassa [el a Rövid útmutató: Új felhasználók hozzáadása az Azure Active Directoryhoz című témakört.](../add-users-azure-active-directory.md)
* Olyan csoport, amelynek a nem rendszergazdai felhasználó tagja, például *az MFA-Test-Group*. Ebben a csoportban engedélyezi az Azure többtényezős hitelesítést ebben a csoportban.
    * Ha csoportot kell létrehoznia, olvassa el, hogyan [hozhat létre csoportot, és hogyan vehet fel tagokat az Azure Active Directoryban.](../active-directory-groups-create-azure-portal.md)

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

Az Azure többtényezős hitelesítés engedélyezésének és használatának ajánlott módja a feltételes hozzáférési szabályzatok. A Feltételes hozzáférés lehetővé teszi olyan házirendek létrehozását és definiálését, amelyek reagálnak a bejelentkezési eseményekre, és további műveleteket kérnek, mielőtt a felhasználó hozzáférést kapna egy alkalmazáshoz vagy szolgáltatáshoz.

![A feltételes hozzáférés működésének áttekintése a bejelentkezési folyamat biztonságossá tételéhez](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

A feltételes hozzáférési házirendek részletesek és egyediek lehetnek, azzal a céllal, hogy a felhasználók bárhol és bármikor hatékonyan dolgozhassanak, de a szervezet védelme is. Ebben az oktatóanyagban hozzon létre egy alapvető feltételes hozzáférési szabályzatot, amely rákérdez az MFA-ra, amikor egy felhasználó bejelentkezik az Azure Portalra. Ebben a sorozatban egy későbbi oktatóanyagban konfigurálhatja az Azure többtényezős hitelesítést egy kockázatalapú feltételes hozzáférési szabályzat használatával.

Először hozzon létre egy feltételes hozzáférési szabályzatot, és rendelje hozzá a tesztcsoport a következő:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) *egy globális rendszergazdai* engedélyekkel rendelkező fiókhasználatával.
1. Keresse meg, és válassza az **Azure Active Directory**, majd válassza a **biztonság** a menüben a bal oldalon.
1. Válassza **a Feltételes hozzáférés**lehetőséget, majd válassza a + Új házirend **lehetőséget.**
1. Adja meg a házirend nevét, például *az MFA Pilot*.
1. **A Hozzárendelések**csoportban válassza a **Felhasználók és csoportok**lehetőséget, majd a Felhasználók kiválasztása és a **csoportok** kiválasztása választógombot.
1. Jelölje be a **Felhasználók és csoportok**jelölőnégyzetet, majd válassza **ki** a rendelkezésre álló Azure AD-felhasználók és -csoportok tallózásához.
1. Keresse meg és válassza ki az Azure AD-csoportot, például *az MFA-test-csoportot,* majd válassza **a Kijelölés lehetőséget.**

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. A csoport feltételes hozzáférési házirendjének alkalmazásához válassza a **Kész gombot.**

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>A többtényezős hitelesítés feltételeinek konfigurálása

A feltételes hozzáférési szabályzat létrehozása és a felhasználók tesztcsoportja hozzárendelt, most határozza meg a felhőbeli alkalmazások vagy műveletek, amelyek a szabályzatot. Ezek a felhőalapú alkalmazások vagy műveletek azok a forgatókönyvek, amelyek et úgy dönt, hogy további feldolgozást igényelnek, például az MFA-t kéri. Dönthet például úgy, hogy egy pénzügyi alkalmazáshoz vagy a felügyeleti eszközök használatához további ellenőrző üzenet szükséges.

Ebben az oktatóanyagban konfigurálja a feltételes hozzáférési szabályzatot, hogy többfa-t igényeljen, amikor egy felhasználó bejelentkezik az Azure Portalra.

1. Válassza a **Felhőalkalmazások vagy műveletek** lehetőséget. Választhat, hogy a feltételes hozzáférés házirendet alkalmazza az *összes felhőalkalmazásra* vagy *az Alkalmazások kiválasztása parancsra.* A rugalmasság érdekében bizonyos alkalmazásokat is kizárhat a szabályzatból.

    Ehhez az oktatóanyaghoz a *Belefoglalás* lapon válassza az **Alkalmazások kiválasztása** választógombot.

1. Válassza **a Kijelölés**lehetőséget, majd tallózzon a használható bejelentkezési események listájában.

    Ebben az oktatóanyagban válassza a **Microsoft Azure Management** lehetőséget, hogy a szabályzat az Azure Portalra való bejelentkezési eseményekre vonatkozzon.

1. A kijelölt alkalmazások alkalmazásához válassza a **Kijelölés**lehetőséget, majd **a Kész**lehetőséget.

    ![Válassza ki a feltételes hozzáférési szabályzatba felvenni kívánt Microsoft Azure Management alkalmazást](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

A hozzáférés-vezérlésekkel meghatározhatja a hozzáférést biztosítandó felhasználó követelményeit, például egy jóváhagyott ügyfélalkalmazás megnevezését, vagy egy hibrid Azure AD-hez csatlakozott eszközt. Ebben az oktatóanyagban konfigurálja a hozzáférési vezérlőket, hogy az Azure Portalra való bejelentkezési esemény során többfa-t igényeljen.

1. Az *Access-vezérlők csoportban*válassza a **Grant**lehetőséget, majd győződjön meg arról, hogy a **Hozzáférés megadása** választógomb ki van jelölve.
1. Jelölje be a **Többtényezős hitelesítés megkövetelése**jelölőnégyzetet, majd válassza **a Kijelölés lehetőséget.**

A feltételes hozzáférési házirendek csak *jelentésre* állíthatók, ha meg szeretné tekinteni, hogy a konfiguráció milyen hatással lenne a felhasználókra, vagy *ki,* ha jelenleg nem szeretné használni a használati házirendet. A felhasználók tesztcsoportja volt a cél az oktatóanyag, lehetővé teszi, hogy engedélyezze a szabályzatot, majd tesztelje az Azure többtényezős hitelesítés.

1. Állítsa a *Házirend engedélyezése* kapcsolót **Be**beállításra.
1. A feltételes hozzáférési házirend alkalmazásához válassza a **Létrehozás lehetőséget.**

## <a name="test-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication tesztelése

Lássuk a feltételes hozzáférési szabályzatot és az Azure többtényezős hitelesítést működés közben. Először jelentkezzen be egy olyan erőforrásba, amelyhez nincs szükség többfa-t az alábbiak szerint:

1. Új böngészőablak megnyitása InPrivate vagy inkognitó módban, és tallózással[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Jelentkezzen be a nem rendszergazdai tesztfelhasználóval, például *a tesztfelhasználóval.* Nincs szükség az MFA kitöltésére.
1. Zárja be a böngészőablakot.

Most jelentkezzen be az Azure Portalon. Mivel az Azure Portal on a feltételes hozzáférési szabályzatban további ellenőrzést igényel, egy Azure többtényezős hitelesítési kérdés jelenik meg.

1. Nyisson meg egy új böngészőablakot InPrivate vagy [https://portal.azure.com](https://portal.azure.com)inkognitó módban, és keresse meg a tallózható tallózással.
1. Jelentkezzen be a nem rendszergazdai tesztfelhasználóval, például *a tesztfelhasználóval.* Az Azure többtényezős hitelesítésre regisztrálnia kell, és használnia kell azazure-t. Kövesse a utasításokat a folyamat befejezéséhez, és ellenőrizze, hogy sikeresen bejelentkezett-e az Azure Portalra.

    ![Kövesse a böngésző utasításait, majd a regisztrált többtényezős hitelesítési üzenetben jelentkezzen be](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Zárja be a böngészőablakot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a továbbiakban nem szeretné használni a feltételes hozzáférési szabályzatot az oktatóanyag részeként konfigurált Azure többtényezős hitelesítés engedélyezéséhez, törölje a szabályzatot a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg, és válassza az **Azure Active Directory**, majd válassza a **biztonság** a menüben a bal oldalon.
1. Válassza a **Feltételes hozzáférés**lehetőséget, majd válassza ki a létrehozott házirendet, például az *MFA Pilot ot.*
1. Válassza **a Törlés**lehetőséget, majd erősítse meg, hogy törölni szeretné a házirendet.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte az Azure többtényezős hitelesítést feltételes hozzáférési szabályzatok használatával a felhasználók egy kiválasztott csoportjához. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Feltételes hozzáférési szabályzat létrehozása az Azure Többtényezős hitelesítés engedélyezéséhez az Azure AD-felhasználók egy csoportjának
> * Az MFA-t kérdező házirend-feltételek konfigurálása
> * Az MFA-folyamat tesztelése felhasználóként

> [!div class="nextstepaction"]
> [Jelszó-visszaírás engedélyezése önkiszolgáló jelszó-visszaállításhoz (SSPR)](tutorial-enable-writeback.md)
