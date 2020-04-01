---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Panorama9-tel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Panorama9 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c88088b804f90769512d33a5e96fdedc5ce6fba8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094928"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Oktatóanyag: Az Azure Active Directory integrációja a Panorama9-el

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Panorama9-et az Azure Active Directoryval (Azure AD).
A Panorama9 integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Panorama9-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Panorama9-be (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Panorama9-tel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Panorama9 egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Panorama9 támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-panorama9-from-the-gallery"></a>Panorama9 hozzáadása a galériából

A Panorama9 Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Panorama9-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Katalógust a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Panorama9**kifejezést, válassza az eredménypanel **Panoráma9** elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Panorama9 az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a Panorama9-tel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a panorama9 kapcsolódó felhasználó közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Panorama9-tel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a Panorama9 Single Sign-On-t](#configure-panorama9-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Panorama9 tesztfelhasználót](#create-panorama9-test-user)** – ha a Panorama9-ben britta Simon megfelelője szeretne lenni, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Panorama9** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Panorama9 Tartomány és URL-címek egyszeri bejelentkezési információi](common/sp-identifier.png)

    a. A **Bejelentkezés az URL-cím** mezőbe írjon be egy URL-címet:`https://dashboard.panorama9.com/saml/access/3262`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Panorama9 ügyféltámogatási csapatával,](https://support.panorama9.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány szakaszban** másolja az **ujjlenyomatot,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

7. A **Panorámakép beállítása9** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-panorama9-single-sign-on"></a>A Panorama9 egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Panorama9 vállalati webhelyére rendszergazdaként.

2. A felső eszköztáron kattintson a **Kezelés**gombra, majd a **Bővítmények parancsra.**
   
    ![Bővítmények](./media/panorama9-tutorial/ic790023.png "Bővítmények")

3. A **Bővítmények** párbeszédpanelen kattintson az **Egyszeri bejelentkezés gombra.**
   
    ![Egyszeri bejelentkezés](./media/panorama9-tutorial/ic790024.png "Egyszeri bejelentkezés")

4. A **Beállítások** szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Beállítások](./media/panorama9-tutorial/ic790025.png "Beállítások")
   
    a. Az **Identitásszolgáltató URL-címmezőjébe** illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.
   
    b. A **Tanúsítvány ujjlenyomat** szövegdobozába illessze be a tanúsítvány **ujjlenyomat-értékét,** amelyet az Azure Portalról másolt.    
         
5. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Panorama9-hez való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Panorama9**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Panorama9**lehetőséget.

    ![A Panorama9 hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-panorama9-test-user"></a>Panorama9 tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Panorama9-be, ki kell építeni őket a Panorama9-be.  

A Panorama9 esetében a kiépítés manuális feladat.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Panorama9** vállalati webhelyére rendszergazdaként.

2. A felső menüben kattintson a **Kezelés**menü gombra, majd a **Felhasználók parancsra.**
   
    ![Felhasználók](./media/panorama9-tutorial/ic790027.png "Felhasználók")

3. A Felhasználók csoportban **+** kattintson ide új felhasználó hozzáadásához.

    ![Felhasználók](./media/panorama9-tutorial/ic790028.png "Felhasználók")

4. Nyissa meg a Felhasználói adatok szakaszt, írja be a kiépíteni kívánt érvényes Azure Active Directory-felhasználó e-mail címét az **E-mail** szövegmezőbe.

5. Jöjjön a Felhasználók szakaszba, kattintson a **Mentés gombra.**
   
    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Panorama9 csempére kattint, automatikusan be kell jelentkeznie arra a Panorama9-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

