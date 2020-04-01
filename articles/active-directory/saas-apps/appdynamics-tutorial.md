---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az AppDynamics programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az AppDynamics között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43245a88ee48b016e1947047d12349423cca6047
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73152887"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Oktatóanyag: Az Azure Active Directory integrációja az AppDynamics programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az AppDynamics-t az Azure Active Directoryval (Azure AD).
Az AppDynamics integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az AppDynamics-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az AppDynamics (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az AppDynamics alkalmazással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Az AppDynamics egyszeri bejelentkezésre szóló előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az AppDynamics támogatja az **SP** által kezdeményezett sso-t

* Az AppDynamics támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-appdynamics-from-the-gallery"></a>Az AppDynamics hozzáadása a gyűjteményből

Az AppDynamics azure AD-be való integrálásának konfigurálásához hozzá kell adnia az AppDynamics-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az AppDynamics alkalmazást a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **AppDynamics**( AppDynamics , **Alkalmazásdinamika** elemet az eredménypanelen ) elemre, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![AppDynamics az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az AppDynamics alkalmazással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az AppDynamics-ben.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az AppDynamics alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az AppDynamics Single Sign-On --t](#configure-appdynamics-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre AppDynamics tesztfelhasználót](#create-appdynamics-test-user)** – ha az AppDynamics-ben britta Simon megfelelője van, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **AppDynamics** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Az AppDynamics tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.saas.appdynamics.com?accountName=<companyname>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az AppDynamics ügyféltámogatási csapatával](https://www.appdynamics.com/support/) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **AppDynamics beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-appdynamics-single-sign-on"></a>Az AppDynamics egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az AppDynamics vállalati webhelyére.

1. A felső eszköztáron kattintson a **Beállítások**gombra, majd a **Felügyelet**gombra.

    ![Felügyelet](./media/appdynamics-tutorial/ic790216.png "Adminisztráció")

1. Kattintson a **Hitelesítésszolgáltató** fülre.

    ![Hitelesítésszolgáltató](./media/appdynamics-tutorial/ic790224.png "Hitelesítésszolgáltató")

1. A **Hitelesítésszolgáltató** szakaszban hajtsa végre az alábbi lépéseket:

   ![SAML konfiguráció](./media/appdynamics-tutorial/ic790225.png "SAML konfiguráció")

   a. **Hitelesítésszolgáltatóként**válassza az **SAML**lehetőséget.

   b. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

   c. A **Kijelentkezés URL-cím** beírt mezőjébe illessze be a **Kijelentkezés i URL-címét,** amelyet az Azure Portalról másolt.

   d. Nyissa meg az alap-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be a **Tanúsítvány** szövegdobozába

   e. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést az AppDynamics-hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd az **AppDynamics**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **AppDynamics**lehetőséget.

    ![Az Alkalmazások listában az AppDynamics hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-appdynamics-test-user"></a>AppDynamics-tesztfelhasználó létrehozása

Ez a szakasz célja egy Britta Simon nevű felhasználó létrehozása az AppDynamics-ben. Az AppDynamics támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Új felhasználó jön létre az AppDynamics elérésére tett kísérlet során, ha még nem létezik.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon [az AppDynamics ügyféltámogatási csoportjához.](https://www.appdynamics.com/support/)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az AppDynamics csempére kattint, automatikusan be kell jelentkeznie arra az AppDynamics-programba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

