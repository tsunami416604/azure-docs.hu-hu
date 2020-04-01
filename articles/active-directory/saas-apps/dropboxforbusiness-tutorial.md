---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Dropbox for Businessszolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Dropbox for Business között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7fc366c5087e66c3022c212870397d77e6e34d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046764"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Oktatóanyag: A Dropbox vállalati verzió integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Dropbox vállalati verziót az Azure Active Directoryval (Azure AD). Ha integrálja a Dropbox for Businesst az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Dropbox for Business szolgáltatáshoz.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek a Dropbox for Business szolgáltatásba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Dropbox for Business egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

* Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Dropbox for Business támogatja az **SP** által kezdeményezett SSO-t

* A Dropbox for Business támogatja [az automatikus felhasználói kiépítést és megszüntetést](dropboxforbusiness-tutorial.md)
* A Dropbox konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox vállalati verzió hozzáadása a galériából

A Dropbox for Business Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Dropbox for Business-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írja be a **Dropbox for Business kifejezést** a keresőmezőbe.
1. Válassza a **Dropbox vállalati verzió** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Dropbox for Business segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Dropbox for Business-ben.

Az Azure AD SSO beállítása és tesztelése a Dropbox for Business szolgáltatással hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.    
    1. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Dropbox for Business Egyszeri bejelentkezést](#configure-dropbox-for-business-sso)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Dropbox for Business teszt felhasználó](#create-dropbox-for-business-test-user)** - egy megfelelője Britta Simon a Dropbox for Business, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg a **Kezelés szakaszt** a **Dropbox vállalati** verzióalkalmazás-integráció lapon, és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://www.dropbox.com/sso/<id>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy értéket:`Dropbox`

    > [!NOTE]
    > Az előző bejelentkezési URL-érték nem valós érték. Frissíteni fogja az értéket a tényleges bejelentkezési URL-címet, amely később az oktatóanyag ban ismertetjük.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Dropbox beállítása üzleti verzióhoz** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure-t a Dropbox for Business-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Dropbox for Business**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-dropbox-for-business-sso"></a>Dropbox vállalati sso konfigurálása

1. A Dropbox for Business konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup Dropbox for Business** irányítja, hogy a Dropbox vállalati verzió alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a Dropbox for Business-be való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-8.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Dropbox for Business-t, nyisson meg egy új böngészőablakot, és lépjen be a Dropbox for Business bérlői fiókjába, és jelentkezzen be a Dropboxba az üzleti bérlők számára. és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769509.png "Egyszeri bejelentkezés konfigurálása")

4. Kattintson a **Felhasználói ikonra,** és válassza a **Beállítások** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure1.png "Egyszeri bejelentkezés konfigurálása")

5. A bal oldali navigációs ablakban kattintson a **Felügyeleti konzol**elemre .

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure2.png "Egyszeri bejelentkezés konfigurálása")

6. A **Felügyeleti konzolon**kattintson a bal oldali navigációs ablak **Beállítások** gombjára.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure3.png "Egyszeri bejelentkezés konfigurálása")

7. Válassza **az Egyszeri bejelentkezés** lehetőséget a **Hitelesítés** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure4.png "Egyszeri bejelentkezés konfigurálása")

8. Az **Egyszeri bejelentkezés szakaszban** hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure5.png "Egyszeri bejelentkezés konfigurálása")

    a. Válassza a **Kötelező** beállítás lehetőséget az **Egyszeri bejelentkezés**legördülő menüből.

    b. Kattintson a **Bejelentkezési URL hozzáadása elemre,** majd az **Identitásszolgáltató bejelentkezési URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet,** és válassza a **Kész lehetőséget.**

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure6.png "Egyszeri bejelentkezés konfigurálása")

    c. Kattintson **a Tanúsítvány feltöltése gombra,** majd keresse meg a **Base64-alapú kódolt tanúsítványfájlt,** amelyet az Azure Portalról töltött le.

    d. Kattintson a **Másolás hivatkozásra,** és illessze be a másolt értéket az Azure Portal **Dropbox for Business Domain és URL-címei** **bealáíró URL-címmezőjébe.**

    e. Kattintson a **Mentés** gombra.

### <a name="create-dropbox-for-business-test-user"></a>Dropbox vállalati verziós tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Dropbox for Business alkalmazásban. A Dropbox for Business támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Dropbox for Business, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon [a Dropbox vállalati ügyfélhez támogatási csapathoz](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Dropbox for Business csempét, automatikusan be kell jelentkeznie abba a Dropbox for Business-be, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Dropbox védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-dropbox)