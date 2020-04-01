---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az LMS elnyelésével | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az LMS elnyelése között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107506"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Oktatóanyag: Az Azure Active Directory integrációja az LMS elnyelésével

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az LMS-t az Azure Active Directoryval (Azure AD).
Az Absorb LMS integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki rendelkezik hozzáféréssel az LMS-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve az LMS (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció absorb LMS-sel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* LMS egyszeri bejelentkezéssel rendelkező előfizetésének elnyelése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Elnyeli lms támogatja **IDP** kezdeményezett SSO

## <a name="adding-absorb-lms-from-the-gallery"></a>Az LMS hozzáadása a galériából

Az Elnyelő LMS azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Absorb LMS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az Absorb LMS hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az Absorb LMS ( **Elnyel)** kifejezést, válassza **az LMS elnyelése** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az LMS elnyelése az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az Elnyelő LMS-szel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat az LMS elnyelése.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Absorb LMS-szel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az LMS egyszeri bejelentkezést](#configure-absorb-lms-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre elnyeli az LMS-teszt felhasználóját](#create-absorb-lms-test-user)** – ha britta Simon megfelelőjét szeretné elnyelni az LMS-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Absorb LMS-szel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **LMS-alkalmazások integrációs felületének elnyeli** az **LMS-alkalmazások**integrációját, válassza az Egyszeri bejelentkezés lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Az LMS-tartomány és az URL-címek egyszeri bejelentkezési adatainak elnyelése](common/idp-intiated.png)

    Ha absorb **5 - ui** használata a következő konfiguráció:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://company.myabsorb.com/account/saml`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://company.myabsorb.com/account/saml`

    Ha az **Absorb 5 – New Learner Experience játékot** használja, használja a következő konfigurációt:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az LMS ügyféltámogatási csapatával,](https://support.absorblms.com/hc/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Beállítás imedvő LMS** szakaszban másolja a megfelelő URL-cím(ek)et a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-absorb-lms-single-sign-on"></a>Konfigurálja az LMS egyszeri bejelentkezést

1. Egy új böngészőablakban jelentkezzen be rendszergazdaként az Absorb LMS vállalati webhelyére.

2. Válassza a **Fiók** gombot a jobb felső sarokban.

    ![A Fiók gomb](./media/absorblms-tutorial/1.png)

3. A Fiók ablaktáblán válassza a **Portálbeállítások lehetőséget.**

    ![A Portál beállítások hivatkozása](./media/absorblms-tutorial/2.png)

4. Válassza az **SSO-beállítások kezelése** lapot.

    ![A Felhasználók lap](./media/absorblms-tutorial/managesso.png)

5. Az **Egyszeri bejelentkezés beállításainak kezelése** lapon tegye a következőket:

    ![Az egyszeri bejelentkezési konfigurációs lap](./media/absorblms-tutorial/settings.png)

    a. A **név** szövegmezőbe írja be a nevet, például az Azure AD Piactér SSO.

    b. Válassza az **SAML** **metódusként lehetőséget.**

    c. A Jegyzettömbben nyissa meg az Azure Portalról letöltött tanúsítványt. Távolítsa el a **---BEGIN CERTIFICATE---** és **---END CERTIFICATE---** címkéket. Ezután a **Kulcs** mezőbe illessze be a fennmaradó tartalmat.

    d. A **Mód** mezőben válassza az **Kezdeményezett identitásszolgáltató lehetőséget.**

    e. Az **Id Property** mezőben válassza ki az Azure AD felhasználói azonosítóként konfigurált attribútumot. Ha például az Azure AD-ben *névazonosító* van kiválasztva, válassza a **Felhasználónév**lehetőséget.

    f. Válassza a **Sha256 (Sha256)** **lehetőséget aláírástípusként.**

    g. A **Bejelentkezési URL-cím** mezőbe illessze be a **felhasználói hozzáférés URL-címét** az alkalmazás Az Azure Portal **Tulajdonságok** lapjáról.

    h. A **kijelentkezési URL-címbe**illessze be az Azure **Portal Bejelentkezéskonfigurálása** ablakból másolt **kijelentkezési URL-címet.**

    i. Automatikus **átirányítás** **bekapcsolása**.

6. Válassza a **Mentés gombot.**

    ![Az egyetlen sso bejelentkezési kapcsolóváltás engedélyezése](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban`brittasimon\@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést az LMS-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **az LMS elnyelése**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **LMS elnyelése**lehetőséget.

    ![Az LMS elnyelése hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-absorb-lms-test-user"></a>Elnyeli az LMS-teszt felhasználóját

Az Azure AD-felhasználók számára, hogy jelentkezzen be az LMS elnyelése, be kell állítani az LMS-ben. Az LMS elnyelése esetén a kiépítés manuális feladat.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az Absorb LMS vállalati webhelyére rendszergazdaként.

2. A **Felhasználók** ablaktáblán válassza a **Felhasználók**lehetőséget.

    ![A Felhasználók hivatkozás](./media/absorblms-tutorial/absorblms_userssub.png)

3. Válassza **a Felhasználó** lapot.

    ![Az Új hozzáadása legördülő lista](./media/absorblms-tutorial/absorblms_createuser.png)

4. A **Felhasználó hozzáadása** lapon tegye a következőket:

    ![A Felhasználó hozzáadása lap](./media/absorblms-tutorial/user.png)

    a. Az **Utónév** mezőbe írja be az utónevet, például **Britta**.

    b. A **Vezetéknév** mezőbe írja be a vezetéknevet, például **Simon**.

    c. A **Felhasználónév** mezőbe írjon be egy teljes nevet, például **Britta Simon**.

    d. A **Jelszó** mezőbe írja be a felhasználói jelszót.

    e. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.

    f. Állítsa az **Aktív** kapcsolót **Aktív**ra.

5. Válassza a **Mentés gombot.**

    ![Az egyetlen sso bejelentkezési kapcsolóváltás engedélyezése](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Alapértelmezés szerint a felhasználói kiépítés nincs engedélyezve az SSO-ban. Ha az ügyfél engedélyezni szeretné ezt a funkciót, be kell állítania [azt](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) a jelen dokumentációban említettek szerint. Kérjük, vegye figyelembe, hogy a Felhasználói provisioing csak az **Absorb 5 - New Learner Experience** with ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az LMS elnyelése csempére kattint, a rendszer automatikusan bejelentkezik az SSO beállítását képező LMS-be. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
