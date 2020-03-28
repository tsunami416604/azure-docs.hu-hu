---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Mimecast személyes portállal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Mimecast personal portal között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160630"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Oktatóanyag: Az Azure Active Directory integrációja a Mimecast személyes portállal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Mimecast személyes portált az Azure Active Directoryval (Azure AD).
A Mimecast személyes portál integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Mimecast személyes portálhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Mimecast personal portal (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció mimecast személyes portállal való konfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Mimecast Personal Portal egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Mimecast Personal Portal támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Mimecast személyes portál hozzáadása a galériából

A Mimecast personal portal Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mimecast személyes portált a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a mimecast személyes portált a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Mimecast Personal Portal (Mimecast Personal Portal**) kifejezést, válassza a **Mimecast Personal Portal** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Mimecast Személyes Portál az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Mimecast Personal Portal szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó mimecast személyes portálon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Mimecast személyes portállal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Mimecast Personal Portal Single Sign-On](#configure-mimecast-personal-portal-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Mimecast Personal Portal tesztfelhasználó létrehozása](#create-mimecast-personal-portal-test-user)** – britta Simon megfelelője a Mimecast személyes portálon, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Mimecast személyes portállal hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Mimecast Personal Portal** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Mimecast személyes portál tartomány és URL egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet: 

    | Régió  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | Régió  |  Érték | 
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Egyesült Államok   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Ausztrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. A **Válasz URL-címmezőjébe** írjon be egy URL-címet: 

    | Régió  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Az azonosító értéke nem valós. Frissítse az értéket a tényleges azonosítóval. Lépjen kapcsolatba [a Mimecast Personal Portal ügyféltámogatási csapatával](https://www.mimecast.com/customer-success/technical-support/) az érték értéséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Mimecast személyes portál beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>A Mimecast personal portal egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mimecast személyes portáljára rendszergazdaként.

2. Nyissa meg a **Szolgáltatások \> alkalmazások at.**
   
    ![Alkalmazások](./media/mimecast-personal-portal-tutorial/ic794998.png "Alkalmazások")

3. Kattintson **a Hitelesítési profilok gombra.**
   
    ![Hitelesítési profilok](./media/mimecast-personal-portal-tutorial/ic794999.png "Hitelesítési profilok")

4. Kattintson **az Új hitelesítési profil gombra.**
   
    ![Új hitelesítési profil](./media/mimecast-personal-portal-tutorial/ic795000.png "Új hitelesítési profil")

5. A **Hitelesítési profil szakaszban** hajtsa végre az alábbi lépéseket:
   
    ![Hitelesítési profil](./media/mimecast-personal-portal-tutorial/ic795001.png "Hitelesítési profil")
   
    a. A **Leírás** mezőbe írja be a konfiguráció nevét.
   
    b. Válassza **az SAML-hitelesítés kényszerítése a Mimecast személyes portálon**lehetőséget.
   
    c. **Szolgáltatóként**válassza az **Azure Active Directory**lehetőséget.
   
    d. A **Kiállító URL-címmezőjébe** illessze be az **Azure Hirdetési azonosító**értékét, amelyet az Azure Portalról másolt.
   
    e. A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.
   
    f. A **Kijelentkezés URL-cím** mezőjébe illessze be a **Kijelentkezés URL-címének**értékét, amelyet az Azure Portalról másolt.

    g. Nyissa meg az **alap-64** kódolású tanúsítványt az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **identitásszolgáltatói tanúsítvány (metaadatok)** szövegmezőjébe.

    h. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.
   
    i. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Mimecast személyes portálhoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Mimecast personal portal**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **Mimecast Personal Portal lehetőséget.**

    ![A Mimecast személyes portál hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-mimecast-personal-portal-test-user"></a>Mimecast Personal Portal tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Mimecast személyes portálra, ki kell építeni őket a Mimecast személyes portálra. A Mimecast személyes portál esetén a kiépítés manuális feladat.

A felhasználók létrehozása előtt regisztrálnia kell egy tartományt.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be **a Mimecast személyes portálra** rendszergazdaként.

2. Tovább a **könyvtárak \> belső**.
   
    ![Könyvtárak](./media/mimecast-personal-portal-tutorial/ic795003.png "Könyvtárak")

3. Kattintson **az Új tartomány regisztrálása gombra.**
   
    ![Új tartomány regisztrálása](./media/mimecast-personal-portal-tutorial/ic795004.png "Új tartomány regisztrálása")

4. Az új tartomány létrehozása után kattintson az **Új cím gombra.**
   
    ![Új cím](./media/mimecast-personal-portal-tutorial/ic795005.png "Új cím")

5. Az új cím párbeszédpanelen hajtsa végre a kiépíteni kívánt érvényes Azure AD-fiók következő lépéseit:
   
    ![Mentés](./media/mimecast-personal-portal-tutorial/ic795006.png "Mentés")
   
    a. Az **E-mail cím** mezőbe írja be a felhasználó **e-mail címét** **BrittaSimon\@contoso.com**.
    
    b. A **Globális név** mezőbe írja be a **felhasználónevet** **BrittaSimon ( BrittaSimon**) néven.

    c. A **Jelszó**és a **Jelszó megerősítése** mezőbe írja be a felhasználó **jelszavát.**
   
    b. Kattintson a **Mentés** gombra.

>[!NOTE]
>A Mimecast Személyes portál felhasználói fiókkészítő eszközeinek vagy API-inak a Mimecast Personal Portal által biztosított bármely más használatával azure AD felhasználói fiókokat hozhat létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Mimecast személyes portál csempére kattint, automatikusan be kell jelentkeznie a Mimecast személyes portálra, amelyhez az SSO-t állította be. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

