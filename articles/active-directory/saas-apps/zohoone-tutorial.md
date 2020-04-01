---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zoho One-nal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zoho One között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086231"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Oktatóanyag: Az Azure Active Directory integrációja a Zoho One-nal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zoho One-t az Azure Active Directoryval (Azure AD).
A Zoho One integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zoho One.You can control in Azure AD who has access to Zoho One.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Zoho One -ba (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Zoho One-nal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Zoho Egy egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Zoho One támogatja az **SP-t** és az **IDP** által kezdeményezett Egyszeri erőforrást

## <a name="adding-zoho-one-from-the-gallery"></a>Zoho One hozzáadása a galériából

A Zoho One Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Zoho One-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Zoho One-t a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zoho One**kifejezést, válassza a **Zoho One elemet** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Zoho One az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Zoho One-nal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a zoho one-i kapcsolódó felhasználó közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Zoho One szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja zoho one single sign-on](#configure-zoho-one-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Zoho One teszt felhasználó](#create-zoho-one-test-user)** - egy megfelelője Britta Simon zoho one, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Zoho One-nal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Zoho One** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Zoho One Domain és URL-ek egyszeri bejelentkezési információk](common/idp-relay.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:`one.zoho.com`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Az előző **Válasz URL-érték** nem valós. Az értéket `<saml-identifier>` a **Zoho One Single Sign-On szakasz konfigurálása** #step4 kapja meg, amelyet az oktatóanyag későbbi részében ismertetünk.

    c. Kattintson **a További URL-címek beállítása gombra.**

    d. A **Továbbítási állapot** mezőbe írjon be egy URL-címet:`https://one.zoho.com`

5. Ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépést:


    ![Zoho One Domain és URL-ek egyszeri bejelentkezési információk](common/both-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Az előző **bejelentkezési URL-érték** nem valós. Az értéket a tényleges bejelentkezési URL-címmel fogja frissíteni a **Zoho One Single Sign-On** szakaszból, amelyet az oktatóanyag későbbi részében ismertetünk. 

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Zoho Beállítása Egy** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-zoho-one-single-sign-on"></a>Zoho One Single Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zoho One vállalati webhelyére rendszergazdaként.

2. A **Szervezet** lapon kattintson a **Beállítás** gombra az **SAML-hitelesítés**csoportban.

    ![Zoho Egy org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Az előugró lapon hajtsa végre a következő lépéseket:

    ![Zoho Egy sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    b. A **Kijelentkezési URL-cím** mezőbe illessze be a **Kijelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Kattintson a **Tallózás** gombra az Azure Portalról letöltött **tanúsítvány (Base64)** feltöltéséhez.

    d. Kattintson a **Mentés** gombra.

4. Az SAML-hitelesítés beállításának mentése után másolja az **SAML-azonosító** értékét, `<saml-identifier>`és `https://accounts.zoho.com/samlresponse/one.zoho.com` fűzze hozzá a **válasz URL-címével** a helyett, például illessze be a létrehozott értéket a **Válasz URL** szövegmezőbe az **Egyszerű SAML konfiguráció csoportban.**

    ![Zoho Egy saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Nyissa meg a **Tartományok** lapot, és kattintson a **Tartomány hozzáadása gombra.**

    ![Zoho Egy domain](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. A **Tartomány hozzáadása** lapon hajtsa végre az alábbi lépéseket:

    ![Zoho One add domain](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. A **Tartománynév** mezőbe írja be a tartományt, például contoso.com.

    b. Kattintson a **Hozzáadás** gombra.

    >[!Note]
    >A tartomány hozzáadása után kövesse az [alábbi](https://www.zoho.com/one/help/admin-guide/domain-verification.html) lépéseket a tartomány igazolásához. A tartomány ellenőrzése után használja a tartománynevét **a bejelentkezési URL-címben** az Azure Portal **alapszintű SAML-konfigurációszakaszában.**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a Zoho One hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Zoho One lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zoho One lehetőséget.**

    ![A Zoho One hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-zoho-one-test-user"></a>Zoho One tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Zoho One-ba, ki kell építeni őket a Zoho One-ba. A Zoho One-ban a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Zoho One-ba biztonsági rendszergazdaként.

2. A **Felhasználók** lapon kattintson a **felhasználói emblémára.**

    ![Zoho Egy felhasználó](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. A **Felhasználó hozzáadása** lapon hajtsa végre az alábbi lépéseket:

    ![Zoho One felhasználó hozzáadása](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. A **Név** mezőbe írja be a felhasználó nevét, például **Britta simon**.
    
    b. Az **E-mail cím** mezőbe írja brittasimon@contoso.combe a felhasználó e-mail címét, például .

    >[!Note]
    >Válassza ki az ellenőrzött tartományt a tartománylistából.

    c. Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a Zoho One csempére kattint, automatikusan be kell jelentkeznie arra a Zoho One-ba, amelyhez beállította az Egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

