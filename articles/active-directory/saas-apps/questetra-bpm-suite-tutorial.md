---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Questetra BPM Suite programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Questetra BPM Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093357"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Oktatóanyag: Az Azure Active Directory integrációja a Questetra BPM Suite programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Questetra BPM Suite-ot az Azure Active Directoryval (Azure AD).
A Questetra BPM Suite és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Questetra BPM Suite-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Questetra BPM Suite (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Questetra BPM Suite csomaggal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Questetra BPM Suite egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Questetra BPM Suite támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Questetra BPM Suite hozzáadása a galériából

A Questetra BPM Suite Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Questetra BPM Suite-ot a galériából a felügyelt SaaS-alkalmazások listájához.

**Ha a Questetra BPM Suite-ot a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Questetra BPM Suite**kifejezést , válassza a **Questetra BPM Suite** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Questetra BPM Suite az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Questetra BPM Suite szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Questetra BPM Suite kell létrehozni közötti kapcsolat.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Questetra BPM Suite szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Questetra BPM Suite Single Sign-On --t](#configure-questetra-bpm-suite-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Questetra BPM Suite tesztfelhasználót](#create-questetra-bpm-suite-test-user)** - hogy a Questetra BPM Suite-ban britta Simon megfelelője legyen, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Questetra BPM Suite szolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Questetra BPM Suite** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Questetra BPM Suite Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Ezeket az értékeket a **Questetra BPM Suite** cég webhelyén található **SP Information** részben kaphatja meg, amelyet a bemutató későbbi részében ismertetünk, vagy lépjen kapcsolatba a [Questetra BPM Suite ügyféltámogatási csapatával.](https://www.questetra.com/contact/) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Questetra BPM Suite beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurálja a Questetra BPM Suite egyszeri bejelentkezést

1. Egy másik böngészőablakban jelentkezzen be **a Questetra BPM Suite** vállalati webhelyére rendszergazdaként.

2. A felső menüben kattintson a **Rendszerbeállítások parancsra.** 
   
    ![Az Azure AD egyszeri bejelentkezése][10]

3. A **SingleSignOnSAML** lap megnyitásához kattintson az **SSO (SAML) gombra.** 
   
    ![Az Azure AD egyszeri bejelentkezése][11]

4. A **Questetra BPM Suite** vállalati webhelyén, az **SP Információ** szakaszban hajtsa végre a következő lépéseket:

    a. Másolja az **ACS URL-címét,** majd illessze be a **Bejelentkezési URL-cím** beszövegébe az Azure Portal **alapszintű SAML-konfiguráció szakaszában.**
    
    b. Másolja az **entitásazonosítót**, majd illessze be az Azure Portal **alapszintű SAML-konfigurációja** **szakaszazonosító** szövegmezőjébe.

5. A **Questetra BPM Suite** vállalati webhelyén hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][15]
   
    a. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.
   
    b. Az **Entitásazonosító** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.
    
    c. A **Bejelentkezési lap URL-címmezőjébe** illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.
    
    d. A **Kijelentkezési lap URL-címmezőjébe** illessze be a **Kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.
    
    e. A **NameID formátumú** szövegmezőbe írja be a következőt: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

    f. Nyissa meg a **Base-64** kódolású tanúsítványt az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **Érvényesítési tanúsítvány** szövegmezőjébe. 

    g. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Questetra BPM Suite-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Questetra BPM Suite lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Questetra BPM Suite lehetőséget.**

    ![A Questetra BPM Suite link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-questetra-bpm-suite-test-user"></a>Questetra BPM Suite tesztfelhasználó létrehozása

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű Britta Simon questetra BPM Suite.

**Britta Simon nevű felhasználó létrehozásához a Questetra BPM Suite alkalmazásban hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Questetra BPM Suite vállalati webhelyére rendszergazdaként.

2. Nyissa meg **a Rendszerbeállítások > felhasználói lista > új felhasználó .**
 
3. Az Új felhasználó párbeszédpanelen hajtsa végre az alábbi lépéseket: 
   
    ![Tesztfelhasználó létrehozása][300] 
   
    a. A **Név** mezőbe írja be a britta.simon@contoso.comfelhasználó **nevét.**
   
    b. Az **E-mail** mezőbe írja be britta.simon@contoso.coma felhasználó **e-mail címét.**
   
    c. A **Jelszó** mezőbe írja be a felhasználó **jelszavát.**
    
    d. Kattintson **az Új felhasználó hozzáadása gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Questetra BPM Suite csempére kattint, automatikusan be kell jelentkeznie a Questetra BPM Suite programba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png