---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az xMatters OnDemand - Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az xMatters OnDemand között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086506"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Oktatóanyag: Az Azure Active Directory integrációja az xMatters OnDemand szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az xMatters OnDemand szolgáltatást az Azure Active Directoryval (Azure AD).
Az xMatters OnDemand integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az xMatters OnDemand.You can control in Azure AD who has access to xMatters OnDemand.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az xMatters OnDemand (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az xMatters OnDemand szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* xMatters OnDemand egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* az xMatters OnDemand támogatja az **IDP** által kezdeményezett SSO-t

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>XMatters OnDemand hozzáadása a galériából

Az xMatters OnDemand azure AD-be való integrálásának konfigurálásához hozzá kell adnia az xMatters OnDemand-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az xMatters OnDemand-ot a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **xMatters OnDemand**parancsot , válassza az **xMatters OnDemand** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![xMatters OnDemand az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést az xMatters OnDemand szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó xMatters OnDemand létre kell hozni a kapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az xMatters OnDemand szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az xMatters OnDemand single sign-on](#configure-xmatters-ondemand-single-sign-on)** beállítást az alkalmazás oldalon az egyszeri bejelentkezés beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre xMatters OnDemand teszt felhasználó](#create-xmatters-ondemand-test-user)** - egy megfelelője Britta Simon xMatters OnDemand, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az xMatters OnDemand szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **xMatters OnDemand alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![xMatters OnDemand domain és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az xMatters OnDemand ügyféltámogatási csapatával,](https://www.xmatters.com/company/contact-us/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

    > [!IMPORTANT]
    > Tovább kell továbbítanod a tanúsítványt az [xMatters OnDemand támogatási csapatának.](https://www.xmatters.com/company/contact-us/) Az egyszeri bejelentkezési konfiguráció véglegesítése előtt az xMatters támogatási csapatnak fel kell töltenie a tanúsítványt.

6. A **Set up xMatters OnDemand (Igény)** beállítása szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-xmatters-ondemand-single-sign-on"></a>XMatters OnDemand egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az XMatters OnDemand vállalati webhelyére rendszergazdaként.

2. A felső eszköztáron kattintson a **Rendszergazda**gombra, majd a bal oldali navigációs sávon a **Vállalat részletei** parancsra.

    ![Felügyelet](./media/xmatters-ondemand-tutorial/IC776795.png "Rendszergazda")

3. Az **SAML konfigurációja** lapon hajtsa végre az alábbi lépéseket:

    ![SAML konfiguráció](./media/xmatters-ondemand-tutorial/IC776796.png "SAML konfiguráció")

    a. Válassza **az SAML engedélyezése**lehetőséget.

    b. Az **identitásszolgáltató azonosítószövege** szövegmezőbe illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Az **egyszeri bejelentkezés URL-cím** beírt szövegmezőbe illessze be **a bejelentkezési URL-cím,** amely az Azure Portalról másolt.

    d. Az **egy kijelentkezési URL-cím** beillesztése a **kijelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    e. A Vállalat adatai lapon, a tetején kattintson a **Módosítások mentése gombra.**

    ![Vállalat adatai](./media/xmatters-ondemand-tutorial/IC776797.png "Vállalat adatai")

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t xMatters OnDemand használatával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **xMatters OnDemand**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **xMatters OnDemand**lehetőséget.

    ![Az xMatters OnDemand hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-xmatters-ondemand-test-user"></a>XMatters OnDemand tesztfelhasználó létrehozása

A cél ebben a szakaszban az, hogy hozzon létre egy felhasználó nevű Britta Simon xMatters OnDemand.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az **XMatters OnDemand** bérlőbe.

2. Kattintson **a Felhasználók** fülre, majd a **Felhasználó hozzáadása**gombra.

    ![Felhasználók](./media/xmatters-ondemand-tutorial/IC781048.png "Felhasználók")

3. A **Felhasználó hozzáadása** csoportban hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/xmatters-ondemand-tutorial/IC781049.png "Felhasználó hozzáadása")

    a. Válassza az **Aktív**lehetőséget.

    b. A **Felhasználói azonosító** mezőbe írja be a felhasználó Brittasimon@contoso.comazonosítóját, például .

    c. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például Britta.

    d. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simon.

    e. A **Webhely** szövegmezőben adja meg a kiépíteni kívánt érvényes Azure AD-fiók érvényes webhelyét.

    f. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen az xMatters OnDemand csempére kattint, automatikusan be kell jelentkeznie az xMatters OnDemand-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

