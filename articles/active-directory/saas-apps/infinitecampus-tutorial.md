---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Infinite Campusszal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Infinite Campus között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100367"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Oktatóanyag: Az Azure Active Directory integrációja az Infinite Campus szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Infinite Campust az Azure Active Directoryval (Azure AD).
Az Infinite Campus integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Infinite Campus.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az Infinite Campus (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció infinite campussal való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Infinite Campus egyszeri bejelentkezésre engedélyezett előfizetés
* Legalább az Azure Active Directory rendszergazdájának kell lennie, és a konfiguráció befejezéséhez a "Student Information System (SIS)" Campus termékbiztonsági szerepkörrel kell rendelkeznie.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Infinite Campus támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-infinite-campus-from-the-gallery"></a>Infinite Campus hozzáadása a galériából

Az Infinite Campus Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Infinite Campust a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az Infinite Campust a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **Új alkalmazás** gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Infinite Campus**kifejezést, válassza az **Infinite Campus** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Végtelen Campus az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Infinite Campus szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó között az Infinite Campus kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Infinite Campus szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa infinite campus egyszeri bejelentkezés](#configure-infinite-campus-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Infinite Campus teszt felhasználó](#create-infinite-campus-test-user)** - egy megfelelője Britta Simon végtelen campus, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Infinite Campus szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Infinite Campus** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az Egyszerű SAML konfiguráció szakaszban hajtsa végre a következő lépéseket (vegye figyelembe, hogy a tartomány a gazdamodelltől **függően** változik, de a TELJESEN MINŐSÍTETT TARTOMÁNY értéknek meg kell egyeznie az Infinite Campus telepítésével):

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinite Campus Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Infinite Campus egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Infinite Campus rendszerbe biztonsági rendszergazdaként.

2. A menü bal oldalán kattintson a **Rendszerfelügyelet gombra.**

    ![Az admin](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Keresse meg **a Felhasználóbiztonsági** > **SAML felügyeleti** > **szolgáltató szolgáltató konfigurációját.**

    ![A saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Az **SSO-szolgáltató konfigurációja** lapon hajtsa végre az alábbi lépéseket:

    ![Az sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Válassza **az SAML single sign on lehetőséget.**

    b. A **nem kötelező attribútumnév szerkesztése** **névként**

    c. A Válassza ki az **Identitásszolgáltató (IDP) kiszolgálóadatainak beolvasását** választó gombot, válassza a **Metaadatok URL-címét,** illessze be az **Alkalmazásösszevonás metaadat-url-címét,** amelyet az Azure Portalról másolt a mezőben, majd kattintson a **Szinkronizálás gombra.**

    d. A **Szinkronizálás** gombra kattintás után az **sso-szolgáltató konfigurációja** lapon automatikusan kitöltődik az értékek. Ezek az értékek ellenőrizhetők, hogy megfeleljenek a fenti 4.

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
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com.

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

> [!NOTE]
> Ha azt szeretné, hogy az összes Azure-felhasználó egyszeri bejelentkezési hozzáféréssel rendelkezzon az Infinite Campus szolgáltatáshoz, és az Infinite Campus belső engedélyrendszerére támaszkodjon a hozzáférés szabályozásához, beállíthatja az alkalmazás **Felhasználói hozzárendelés kötelező** tulajdonságát Nem-re, és kihagyhatja a következő lépéseket.

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t végtelen campus hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **Infinite Campus**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Infinite Campus**lehetőséget.

    ![Az Infinite Campus hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-infinite-campus-test-user"></a>Infinite Campus tesztfelhasználó létrehozása

Végtelen Campus egy demográfia központú építészet. Kérjük, vedd fel a kapcsolatot az [Infinite Campus támogatási csapatával,](mailto:sales@infinitecampus.com) hogy hozzáadd a felhasználókat az Infinite Campus platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Infinite Campus csempére kattint, automatikusan be kell jelentkeznie az Infinite Campusba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
