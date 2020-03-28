---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Syncplicity programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Syncplicity között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233287"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Oktatóanyag: A szinkronizálás integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Syncplicity szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Syncplicity-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Syncplicity.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve syncplicity az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Syncplicity egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Syncplicity támogatja **SP** kezdeményezett SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Szinkronizálás hozzáadása a gyűjteményből

A Syncplicity azure AD-be való integrálásának konfigurálásához hozzá kell adnia a syncplicity-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Szinkronizálás kifejezést** a keresőmezőbe.
1. Válassza **a Szinkronizálás** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD-sSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t szinkronizálással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó syncplicity között.

Az Azure AD SSO szinkronizálási beállításokkal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a szinkronizálási egyszeri bejelentkezés egyszeri bejelentkezési](#configure-syncplicity-sso)** beállításait az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Syncplicity teszt felhasználó](#create-syncplicity-test-user)** létrehozása – a B.Simon syncplicity megfelelőjének létrehozásához, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Szinkronizálási** alkalmazások integrációja lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.syncplicity.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Szinkronizálás ügyféltámogatási csapatával,](https://www.syncplicity.com/contact-us) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Szinkronizálás beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Szinkronizálási sso konfigurálása

1. Jelentkezzen be a **Syncplicity bérlőbe.**

1. A felső menüben kattintson a **Rendszergazda menüben**a **Beállítások gombra,** majd az **Egyéni tartomány parancsra, majd az egyszeri bejelentkezés parancsra.**

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Az **Egyszeri bejelentkezés (SSO)** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezésegyszeri \(bejelentkezés\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Az **Egyéni tartomány** mezőbe írja be a tartomány nevét.
  
    b. Válassza **az Engedélyezve** **egyszeri bejelentkezési állapot lehetőséget.**

    c. Az **entitásazonosító** szövegmezőjébe illessze be az **azonosító (entitásazonosító)** értékét, amelyet az Azure Portal **alapSAML-konfigurációjában** használt.

    d. A **bejelentkezési lap URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    e. A **Kijelentkezési lap URL-címmezőjébe** illessze be az Azure Portalról másolt **kijelentkezési URL-címet.**

    f. Az **Identitásszolgáltatói tanúsítvány ban**kattintson a Fájl **kiválasztása**elemre, majd töltse fel az Azure Portalról letöltött tanúsítványt.

    g. Kattintson **a Módosítások mentése gombra.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, a Syncplicity hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Szinkronizálás**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-syncplicity-test-user"></a>Szinkronizálási tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudjanak jelentkezni, ki kell őket építeni a Syncplicity alkalmazásba. Ez a szakasz ismerteti, hogyan hozhat létre Azure AD felhasználói fiókok syncplicity.

**Ha egy felhasználói fiókot a Syncplicity rendszerbe szeretne kiépíteni, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Syncplicity bérlőbe** (például: `https://company.Syncplicity.com`).

1. Kattintson **a rendszergazda** elemre, válassza ki a **felhasználói fiókokat,** majd kattintson **a FELHASZNÁLÓ HOZZÁADÁSA gombra.**

    ![Felhasználók kezelése](./media/syncplicity-tutorial/ic769764.png "Felhasználók kezelése")

1. Írja be a kiépíteni kívánt Azure AD-fiók **e-mail címét,** válassza a **Felhasználó** **szerepkörként**lehetőséget, majd kattintson a **TOVÁBB**gombra.

    ![Fiók adatai](./media/syncplicity-tutorial/ic769765.png "Fiók adatai")

    > [!NOTE]
    > Az Azure AD-fiók tulajdonosa kap egy e-mailt, amely egy linket a fiók megerősítéséhez és aktiválásához.

1. Jelöljön ki egy csoportot a vállalaton, amelynek az új felhasználónak tagja kell, hogy legyen, majd kattintson a **TOVÁBB**gombra.

    ![Csoporttagság](./media/syncplicity-tutorial/ic769772.png "Csoporttagság")

    > [!NOTE]
    > Ha nincsenek felsorolt csoportok, kattintson **a TOVÁBB gombra.**

1. Jelölje ki azokat a mappákat, amelyeket a Szinkronizálás vezérlője csoportban szeretne elhelyezni a felhasználó számítógépén, majd kattintson a **TOVÁBB**gombra.

    ![Szinkronizálási mappák](./media/syncplicity-tutorial/ic769773.png "Szinkronizálási mappák")

> [!NOTE]
> A Syncplicity felhasználói fiók létrehozásához használt bármely más, a Syncplicity által biztosított api-k at használhat az Azure AD felhasználói fiókok kiépítéséhez.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Szinkronizálás csempét, a rendszer automatikusan bejelentkezik a Szinkronizálási beállításba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)