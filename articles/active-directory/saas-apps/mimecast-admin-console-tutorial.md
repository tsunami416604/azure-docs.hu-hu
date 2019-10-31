---
title: 'Oktatóanyag: Azure Active Directory integráció a Mimecast felügyeleti konzollal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Mimecast felügyeleti konzolja között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160654"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Oktatóanyag: Azure Active Directory integráció a Mimecast felügyeleti konzollal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Mimecast felügyeleti konzolt Azure Active Directory (Azure AD) használatával.
A Mimecast felügyeleti konzol Azure AD-vel való integrálása a következő előnyöket biztosítja:

* A Mimecast felügyeleti konzolhoz hozzáférő Azure AD-ben is szabályozható.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Mimecast felügyeleti konzolba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Mimecast felügyeleti konzollal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Mimecast felügyeleti konzol egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Mimecast felügyeleti konzol támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Mimecast felügyeleti konzol hozzáadása a gyűjteményből

A Mimecast felügyeleti konzol Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mimecast felügyeleti konzolt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Mimecast felügyeleti konzoljának a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Mimecast felügyeleti konzol**kifejezést, válassza a **Mimecast felügyeleti konzol** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Mimecast felügyeleti konzol az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Mimecast felügyeleti konzollal konfigurálhatja és tesztelheti a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Mimecast felügyeleti konzolon található kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Mimecast felügyeleti konzollal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Mimecast felügyeleti konzol egyszeri bejelentkezésének konfigurálása](#configure-mimecast-admin-console-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[Mimecast felügyeleti konzoljának tesztelése felhasználó](#create-mimecast-admin-console-test-user)** – a Mimecast felügyeleti konzolján található, a felhasználó Azure ad-Britta összekapcsolt.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Mimecast felügyeleti konzollal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Mimecast felügyeleti konzol** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Mimecast felügyeleti konzol tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet:
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > A bejelentkezési URL-cím a régióra jellemző.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Mimecast felügyeleti konzoljának beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-mimecast-admin-console-single-sign-on"></a>A Mimecast felügyeleti konzol egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Mimecast felügyeleti konzolba.

2. Lépjen a **szolgáltatások \> alkalmazáshoz**.

    ![Szolgáltatások](./media/mimecast-admin-console-tutorial/ic794998.png "Szolgáltatások")

3. Kattintson a **hitelesítési profilok**elemre.

    ![Hitelesítési profilok](./media/mimecast-admin-console-tutorial/ic794999.png "Hitelesítési profilok")
    
4. Kattintson az **új hitelesítési profil**elemre.

    ![Új hitelesítési profilok](./media/mimecast-admin-console-tutorial/ic795000.png "Új hitelesítési profilok")

5. A **hitelesítési profil** szakaszban hajtsa végre a következő lépéseket:

    ![Hitelesítési profil](./media/mimecast-admin-console-tutorial/ic795015.png "Hitelesítési profil")
    
    a. A **Leírás** szövegmezőbe írja be a konfiguráció nevét.
    
    b. Válassza **az SAML-hitelesítés kikényszerítve a Mimecast felügyeleti konzolon**lehetőséget.
    
    c. **Szolgáltatóként**válassza a **Azure Active Directory**lehetőséget.
    
    d. Illessze be az **Azure ad-azonosítót**, amelyet a Azure Portal a **kiállító URL** szövegmezőbe másolt.
    
    e. Illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portal a **bejelentkezési URL** szövegmezőbe másolt.

    f. Illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portal a **kijelentkezési URL-** szövegmezőbe másolt.
    
    >[!NOTE]
    >A bejelentkezési URL-cím és a kijelentkezési URL-cím értéke megegyezik a Mimecast felügyeleti konzoljának értékével.
    
    g. Nyissa meg Azure Portal a Jegyzettömbből letöltött Base-64 tanúsítványt, távolítsa el az első sort (" *--* ") és az utolsó sort (" *--* "), másolja a vágólapra a többi tartalmat, majd illessze be az **identitás-szolgáltatóba. Tanúsítvány (metaadatok)** szövegmező.
    
    h. Jelölje be **az egyszeri bejelentkezés engedélyezése**jelölőnégyzetet.
    
    i. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Mimecast felügyeleti konzolhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Mimecast felügyeleti konzol**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **Mimecast felügyeleti konzol**elemet.

    ![A Mimecast felügyeleti konzoljának hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-mimecast-admin-console-test-user"></a>A Mimecast felügyeleti konzol tesztelési felhasználójának létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Mimecast felügyeleti konzolba, a Mimecast felügyeleti konzolon kell kiépíteni őket. A Mimecast felügyeleti konzol esetében a kiépítés manuális feladat.

* A felhasználók létrehozása előtt regisztrálnia kell egy tartományt.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a **Mimecast felügyeleti konzolra** .

2. Lépjen a **címtárak \> belső**gombra.
   
    ![Címtárak](./media/mimecast-admin-console-tutorial/ic795003.png "Könyvtárak")

3. Kattintson az **új tartomány regisztrálása**elemre.
   
    ![Új tartomány regisztrálása](./media/mimecast-admin-console-tutorial/ic795004.png "Új tartomány regisztrálása")

4. Az új tartomány létrehozása után kattintson az **új címek**elemre.
   
    ![Új címe](./media/mimecast-admin-console-tutorial/ic795005.png "Új címe")

5. Az új címek párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Mentés](./media/mimecast-admin-console-tutorial/ic795006.png "Mentés")
   
    a. Írja be az **e-mail-címet**, a **globális nevet**, a **jelszót**és a **Jelszó megerősítése** attribútumot egy érvényes Azure ad-fiókhoz, amelyet a kapcsolódó szövegdobozokban szeretne kiépíteni.

    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Mimecast felügyeleti konzol által biztosított Mimecast-felügyeleti konzol felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Mimecast felügyeleti konzol csempére kattint, automatikusan be kell jelentkeznie a Mimecast felügyeleti konzolba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

