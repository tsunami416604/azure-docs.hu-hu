---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a LockPath Keylight-tal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a LockPath Keylight között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148c2c46a911088d01ab83fe2d16e8ca81d272ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098778"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Oktatóanyag: Az Azure Active Directory integrációja a LockPath Keylight szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a LockPath Keylight szolgáltatást az Azure Active Directoryval (Azure AD).
A LockPath Keylight integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a LockPath Keylight szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve lockpath keylight (single sign-on) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció lockpath keylight-el való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* LockPath Keylight egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A LockPath Keylight támogatja az **SP** által kezdeményezett SSO-t
* A LockPath Keylight támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-lockpath-keylight-from-the-gallery"></a>LockPath billentyűfény hozzáadása a galériából

A LockPath Keylight azure-as AD-be való integrálásának konfigurálásához hozzá kell adnia a LockPath Keylight-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a LockPath keylight-ot a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LockPath Keylight parancsot,** válassza a **LockPath Keylight elemet** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![LockPath Keylight az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a LockPath Keylight szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a LockPath Keylight létre kell hozni a kapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a LockPath Keylight segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a LockPath Keylight Single Sign-On](#configure-lockpath-keylight-single-sign-on)** --t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[LockPath Keylight tesztfelhasználó](#create-lockpath-keylight-test-user)** létrehozása – a LockPath Keylight-ban lévő Britta Simon megfelelője, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének lockpath-i keylight-mal való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **LockPath Keylight** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![A LockPath keylight tartománya és URL-címei egyszeri bejelentkezési információi](common/sp-identifier-reply.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<company name>.keylightgrc.com/`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<company name>.keylightgrc.com`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel, az azonosítóval és a válasz URL-címmel. Lépjen kapcsolatba [a LockPath Keylight ügyféltámogatási csapatával,](https://www.lockpath.com/contact/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **Tanúsítvány (Raw)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **LockPath kulcsjelző beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-lockpath-keylight-single-sign-on"></a>LockPath keylight egyszeri bejelentkezéskonfigurálása

1. Az SSO engedélyezéséhez a LockPath Keylight alkalmazásban hajtsa végre az alábbi lépéseket:

    a. Jelentkezzen be a LockPath Keylight-fiókjába rendszergazdaként.

    b. A felső menüben kattintson a **Személy**gombra, és válassza a **Kulcsfény beállítása parancsot.**

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/401.png)

    c. A bal oldali treeview-ban kattintson az **SAML gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/402.png)

    d. Az **SAML beállítások** párbeszédpanelen kattintson a **Szerkesztés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/404.png)

1. Az **SAML-beállítások szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/keylight-tutorial/405.png)

    a. Állítsa **az SAML-hitelesítést** **Aktív**ra.

    b. Az **identitásszolgáltató bejelentkezési URL-címmezőjébe** illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    c. Az **identitásszolgáltató kijelentkezési URL-címének** szövege illessze be a **kijelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    d. Kattintson **a Fájl kiválasztása gombra** a letöltött LockPath Keylight tanúsítvány kiválasztásához, majd a **Megnyitás** gombra kattintva töltse fel a tanúsítványt.

    e. Állítsa **az SAML felhasználói azonosító helyét** a **tárgyutasítás NameIdentifier elemére.**

    f. Adja meg a **Kulcsfény szolgáltatót** a következő minta szerint: `https://<CompanyName>.keylightgrc.com`.

    g. Állítsa **az automatikus kiépítési felhasználókat** **Aktív**beállításra.

    h. Állítsa **az automatikus kiépítési fiók típusát** Teljes felhasználó **ra.**

    i. Állítsa be **az automatikus kiépítési biztonsági szerepkört,** válassza **az Általános jogú felhasználó SAML-lel**lehetőséget.

    j. Állítsa be **az automatikus kiépítési biztonsági konfigurációt,** és válassza **az Általános felhasználó konfigurációja lehetőséget.**

    k. Az **E-mail attribútum** mezőbe `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`írja be a következőt:

    l. A **First name attribútum** szövegmezőjébe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    m. A **Vezetéknév attribútum** szövegmezőjébe `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`írja be a következőt:

    n. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a LockPath Keylight hozzáférést biztosít.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **LockPath Keylight**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **LockPath Keylight**lehetőséget.

    ![A LockPath billentyűjelző hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-lockpath-keylight-test-user"></a>LockPath Keylight tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a LockPath Keylight alkalmazásban. A LockPath Keylight támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a LockPath Keylight, egy új jön létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a [LockPath Keylight ügyféltámogatási csapatával.](https://www.lockpath.com/contact/)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a LockPath kulcsjelző csempéjére kattint, automatikusan be kell jelentkeznie a LockPath kulcsjelzőbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)