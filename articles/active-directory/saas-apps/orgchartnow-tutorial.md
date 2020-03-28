---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a OrgChart Now-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OrgChart Now között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095434"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Oktatóanyag: Az Azure Active Directory integrációja a Szervezeti diagrammal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a OrgChart Now-t az Azure Active Directoryval (Azure AD).
A OrgChart Now integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a OrgChart Now-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a OrgChart Now (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Szervezeti diagram most, szüksége van a következő elemeket:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* OrgChart Most egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az OrgChart Now támogatja az **SP** és **az IDP** által kezdeményezett SSO-t

## <a name="adding-orgchart-now-from-the-gallery"></a>OrgChart hozzáadása most a galériából

A Szervezeti diagram most azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Galéria ból a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha most szeretné hozzáadni a Szervezeti diagramot a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **OrgChart Now**kifejezést , válassza az **Eredménypanel En diagramja** lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![OrgChart Most az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a OrgChart Now segítségével egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Szervezeti diagram most létre kell hozni a kapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a OrgChart Now Single Sign-On --t](#configure-orgchart-now-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre OrgChart Now teszt felhasználó](#create-orgchart-now-test-user)** - hogy egy megfelelője Britta Simon orgchart most, hogy kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Szervezeti diagram most, hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **OrgChart Now** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépést:

    ![OrgChart Now Domain és URL-címek egyszeri bejelentkezési információi](common/idp-identifier.png)

    Az **Azonosító** mezőbe írjon be egy URL-címet:`https://sso2.orgchartnow.com`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![image](common/both-preintegrated-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`az **Azure AD-azonosító** a **Szervezeti diagram beállítása most** szakaszból másolva, amelyet később ismertetünk az oktatóanyagban.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Szervezeti diagram beállítása most** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-orgchart-now-single-sign-on"></a>A Szervezeti diagram konfigurálása egyszeri bejelentkezés

Az egyszeri bejelentkezés konfigurálásához a **OrgChart Now** oldalon el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról az [OrgChart Now támogatási csapatának.](mailto:ocnsupport@officeworksoftware.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a OrgChart Now-hoz.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **OrgChart Now**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **OrgChart Now lehetőséget.**

    ![A Szervezeti diagram most hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-orgchart-now-test-user"></a>Szervezeti diagram létrehozása most tesztfelhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Szervezeti diagram most, ki kell építeni a OrgChart Now. 

1. Az OrgChart Now támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van. Új felhasználó jön létre a Szervezeti diagram most elérésére tett kísérlet során, ha még nem létezik. A just-in-time felhasználói kiépítési szolgáltatás csak akkor hoz létre **csak olvasható** felhasználót, ha egy egyszeri bejelentkezési kérelem egy elismert IDP-ből érkezik, és az SAML-alapcímben szereplő e-mail nem található meg a felhasználói listában. Ehhez az automatikus kiépítési funkcióhoz létre kell hoznia egy **Általános** nevű hozzáférési csoportot a Szervezeti diagram most. Hozzáférési csoport létrehozásához kövesse az alábbi lépéseket:

    a. Lépjen a **Csoportok kezelése** lehetőségre, miután a felhasználói felület jobb felső sarkában lévő **fogaskerékre** kattintott.

    ![OrgChart Most csoportok](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Jelölje ki a **Hozzáadás** ikont, és nevezze el az **Általános** csoportot, majd kattintson az **OK**gombra. 

    ![OrgChart Most hozzá](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Jelölje ki azokat a mappákat, amelyekhez az általános vagy csak olvasható felhasználóknak hozzá kell férniük:

    ![OrgChart Most mappák](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zárolja** a mappákat, hogy csak a rendszergazdai felhasználók módosíthassák azokat. Ezután nyomja **le az OK gombot.**

    ![OrgChart most zár](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Rendszergazdai és **olvasási/írási** felhasználók létrehozásához manuálisan kell létrehoznia egy felhasználót, hogy az SSO-n keresztül hozzáférhessen a jogosultsági szintjükhöz. **Admin** Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:

    a. Jelentkezzen be a Szervezeti diagram most biztonsági rendszergazdaként.

    b.  Kattintson a **Beállítások** gombra a jobb felső sarokban, majd keresse meg a **Felhasználók kezelése lapot.**

    ![OrgChart Most beállítások](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kattintson a **Hozzáadás gombra,** és hajtsa végre a következő lépéseket:

    ![OrgChart most kezelése](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * A **Felhasználói azonosító** mezőbe írja be a felhasználói **azonosítót, mint a\@brittasimon contoso.com**.

    * Az **E-mail cím** mezőbe írja be a felhasználó e-mail címét, például **\@brittasimon contoso.com**.

    * Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a Szervezeti diagram most csempére kattint, automatikusan be kell jelentkeznie arra a Szervezeti diagramba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

