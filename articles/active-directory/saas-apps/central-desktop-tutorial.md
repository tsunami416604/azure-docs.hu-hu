---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Central Desktop programmal | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Central Desktop között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda8e928b530001faeae34c364dfed91d7620f0a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157515"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Oktatóanyag: Az Azure Active Directory integrációja a Central Desktop asztallal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Központi asztalt az Azure Active Directoryval (Azure AD).
A Central Desktop integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Központi asztalhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Központi Asztalra (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Central Desktop alkalmazáshoz a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* A Central Desktop egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Central Desktop támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-central-desktop-from-the-gallery"></a>Központi asztal hozzáadása a galériából

A Központi Asztal Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a központi asztalt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a központi asztalt a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Központi asztal**kifejezést, válassza a **Központi asztal** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Központi asztal az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Central Desktop szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Központi Asztalon létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Central Desktop alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Central Desktop Single Sign-On --hoz](#configure-central-desktop-single-sign-on)** configure a Single Sign-On beállítások at alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre központi asztali teszt felhasználó](#create-central-desktop-test-user)** - egy megfelelője Britta Simon a Központi asztalon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Központi asztali** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Központi asztali tartomány és URL-címek egyszeri bejelentkezési információi](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.centraldesktop.com`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a Központi asztali ügyfél támogatási csapatával](https://imeetcentral.com/contact-us) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **Tanúsítvány (Raw)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **Központi asztal beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-central-desktop-single-sign-on"></a>Központi asztali egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a **központi asztal bérlőjéhez.**

2. Nyissa meg a **Beállítások lapot.** Válassza **a Speciális**lehetőséget, majd az Egyszeri bejelentkezés **lehetőséget.**

    ![Telepítő – speciális](./media/central-desktop-tutorial/ic769563.png "Telepítő – speciális")

3. Az **Egyszeri bejelentkezés beállításai** lapon tegye a következő lépéseket:

    ![Egyszeri bejelentkezési beállítások](./media/central-desktop-tutorial/ic769564.png "Egyszeri bejelentkezés beállításai")

    a. Válassza **az SAML v2 single sign on lehetőséget.**

    b. Az **SSO URL-címmezőbe** illessze be az **Azure Hirdetési azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Az **SSO bejelentkezési URL-címmezőbe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    d. Az **SSO-kijelentkezés URL-címe** mezőbe illessze be az Azure Portalról másolt **kijelentkezési URL-értéket.**

4. Az **Üzenetaláírás-ellenőrzési módszer csoportban** kövesse az alábbi lépéseket:

    ![Üzenetaláírás-ellenőrzési módszer](./media/central-desktop-tutorial/ic769565.png "Üzenet aláírás-ellenőrzési módja")
    
    a. Válassza a **Tanúsítvány** elemet.

    b. Az **SSO-tanúsítvány listában** válassza az **RSH SHA256 lehetőséget.**

    c. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben. Ezután másolja a tanúsítvány tartalmát, és illessze be az **SSO-tanúsítvány** mezőbe.

    d. Válassza **a SamLv2 bejelentkezési lapra mutató hivatkozás megjelenítése lehetőséget.**

    e. Válassza a **Frissítés** lehetőséget.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Központi Asztal hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a Központi **asztal**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Központi asztal**lehetőséget.

    ![A Központi asztal hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-central-desktop-test-user"></a>Központi asztali tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudjanak jelentkezni, ki kell építeni őket a Központi asztali alkalmazásban. Ez a szakasz ismerteti, hogyan hozhat létre Azure AD felhasználói fiókok a Központi asztalon.

> [!NOTE]
> Azure AD felhasználói fiókok kiépítése, használhatja bármely más központi asztali felhasználói fiók létrehozása eszközök vagy API-k, amelyek a Central Desktop által biztosított.

**Felhasználói fiókok kiépítése a Központi asztalra:**

1. Jelentkezzen be a központi asztal bérlőjéhez.

2. Válassza **a Személyek,** majd **a Belső tagok hozzáadása**lehetőséget.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. Az **Új tagok e-mail címe** mezőbe írja be a kiépíteni kívánt Azure AD-fiókot, majd válassza a **Tovább**gombot.

    ![Új tagok e-mail címei](./media/central-desktop-tutorial/ic781052.png "Új tagok e-mail címei")

4. Válassza **a Belső tag(ok hozzáadása)** lehetőséget.

    ![Belső tag hozzáadása](./media/central-desktop-tutorial/ic781053.png "Belső tag hozzáadása")
  
   > [!NOTE]
   > A hozzáadott felhasználók kapnak egy e-mailt, amely tartalmaz egy megerősítő hivatkozást a fiókjaik aktiválásához.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Központi asztal csempére kattint, automatikusan be kell jelentkeznie arra a központi asztalra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
