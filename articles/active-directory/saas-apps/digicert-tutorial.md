---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a DigiCerttel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a DigiCert között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 646f3129-aa67-4875-9073-1d0b6a3173d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df68c9289caed0c857434bf4a1f9a5eb28e71ab4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157322"
---
# <a name="tutorial-azure-active-directory-integration-with-digicert"></a>Oktatóanyag: Az Azure Active Directory integrációja a DigiCerttel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a DigiCert-et az Azure Active Directoryval (Azure AD).
A DigiCert integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a DigiCerthez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve digicert (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció digicerttel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* DigiCert egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A DigiCert támogatja az **IDP** által kezdeményezett SSO-t

## <a name="adding-digicert-from-the-gallery"></a>DigiCert hozzáadása a galériából

A DigiCert Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a DigiCert-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha digicertet szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **DigiCert**kifejezést, válassza a **DigiCert** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![DigiCert az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a DigiCert-tel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó digiCert közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a DigiCert-tel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a DigiCert Single Sign-On](#configure-digicert-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[DigiCert tesztfelhasználó létrehozása](#create-digicert-test-user)** – britta Simon megfelelője a DigiCertben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **DigiCert alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![DigiCert Tartomány és URL egyszeri bejelentkezési információk](common/idp-identifier.png)

    Az **Azonosító** mezőbe írjon be egy URL-címet:`https://www.digicert.com/sso`

5. DigiCert alkalmazás elvárja az SAML állításokat egy adott formátumban. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumoknak az értékeit az alkalmazásintegrációs lap **Felhasználói attribútumok** szakaszában kezelheti. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában a **Szerkesztés ikon** használatával szerkesztse a jogcímeket, vagy adja hozzá a jogcímeket az **Új jogcím hozzáadása** paragrafussal az SAML token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket: 

    | Név |  Forrás attribútuma|
    | ---------------| --------------- |
    | nameidentifier | user.userprincipalname |
    | cég | < vállalati kód > |
    | digicertrole között | CanAccessCertCentral |

    > [!Note]
    > A **vállalati** attribútum értéke nem valós. Frissítse ezt az értéket a tényleges vállalati kóddal. A **vállalati** attribútum értékének lefelvételéhez forduljon a [DigiCert támogatási csapatához.](mailto:support@digicert.com)

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

8. A **DigiCert beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-digicert-single-sign-on"></a>DigiCert egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés **digicert** i.konfigurálásához el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról a [DigiCert támogatási csapatának.](mailto:support@digicert.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést a DigiCert-hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **DigiCert**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **DigiCert**lehetőséget.

    ![A DigiCert hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-digicert-test-user"></a>DigiCert tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a DigiCert ben. Együttműködve [DigiCert támogatási csapatával](mailto:support@digicert.com) a felhasználók hozzáadása a DigiCert platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési paneldigiCert csempéjére kattint, automatikusan be kell jelentkeznie a DigiCertbe, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

