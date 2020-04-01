---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az OpsGenie-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OpsGenie között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095754"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Oktatóanyag: Az Azure Active Directory integrációja az OpsGenie-vel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az OpsGenie-t az Azure Active Directoryval (Azure AD).
Az OpsGenie integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az OpsGenie-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve OpsGenie (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az OpsGenie-vel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* OpsGenie egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az OpsGenie támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-opsgenie-from-the-gallery"></a>OpsGenie hozzáadása a galériából

Az OpsGenie azure AD-be való integrációjának konfigurálásához hozzá kell adnia az OpsGenie-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha opsgenie-t szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **OpsGenie**kifejezést, válassza az **OpsGenie** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![OpsGenie az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az OpsGenie-vel egy **B. Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó opsgenie közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az OpsGenie-vel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja opsgenie single sign-on](#configure-opsgenie-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b. Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi B. Simon az Azure AD egyszeri bejelentkezés.
5. **[Hozzon létre OpsGenie teszt felhasználó](#create-opsgenie-test-user)** - egy megfelelője B. Simon opsgenie, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **OpsGenie** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![OpsGenie tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://app.opsgenie.com/auth/login`

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

6. Az **OpsGenie beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-opsgenie-single-sign-on"></a>OpsGenie egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy másik böngészőpéldányt, majd jelentkezzen be rendszergazdaként az OpsGenie-be.

2. Kattintson a **Beállítások**gombra, majd az **Egyszeri bejelentkezés lapra.**
   
    ![OpsGenie egyszeri bejelentkezés](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Az SSO engedélyezéséhez válassza az **Engedélyezve**lehetőséget.
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. A **Szolgáltató** csoportban kattintson az **Azure Active Directory** fülre.
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Az Azure Active Directory párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Az **SAML 2.0 Végpont** szövegdobozába illessze be a **bejelentkezési URL-cím**értéket, amelyet az Azure Portalról másolt.
    
    b. A **Metaadat-url-cím:** szövegmezőbe illessze be **az Alkalmazásösszevonás metaadat-URL-címét,** amelyet az Azure Portalról másolt.
    
    c. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon B. Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be a **B. Simon**mezőt.
  
    b. A **Felhasználónév** mezőtípusban**bsimon@yourcompanydomain.extension**  
    Például: BSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi B. Simon azure egyszeri bejelentkezés használatával opsgenie hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **OpsGenie**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **OpsGenie**lehetőséget.

    ![Az OpsGenie hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-opsgenie-test-user"></a>OpsGenie tesztfelhasználó létrehozása

A szakasz célja, hogy hozzon létre egy felhasználó nevű B. Simon opsgenie. 

1. Egy webböngésző ablakban jelentkezzen be a OpsGenie bérlő, mint egy rendszergazda.

2. Keresse meg a Felhasználók listát a Bal oldali panel **Felhasználók** elemére kattintva.
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

4. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Az **E-mail** szövegmezőbe írja be az Azure Active Directoryban címzett B. Simon e-mail címét.
   
    b. A **Teljes név** mezőbe írja be a **B. Simon**nevet.
   
    c. Kattintson a **Mentés** gombra. 

>[!NOTE]
>B. Simon kap egy e-mailt a profil beállítására vonatkozó utasításokkal.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor az OpsGenie csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az OpsGenie-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

