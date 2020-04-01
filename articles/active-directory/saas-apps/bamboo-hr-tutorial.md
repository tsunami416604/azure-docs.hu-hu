---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a BambooHR-rel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a BambooHR között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfa7934472b512e2c884ab3f9a06728a8fb18b12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157743"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Oktatóanyag: Az Azure Active Directory integrációja a BambooHR-rel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a BambooHR-t az Azure Active Directoryval (Azure AD).
A BambooHR integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a BambooHR.You can control in Azure AD who has access to BambooHR.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve bambooHR (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a BambooHR-rel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* BambooHR egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* BambooHR támogatja **SP** kezdeményezett SSO

## <a name="adding-bamboohr-from-the-gallery"></a>Hozzáadása BambooHR a galériából

A BambooHR azure-beli AD-be való integrálásának konfigurálásához hozzá kell adnia a BambooHR-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a BambooHR-t a galériából szeretné hozzáadni, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **BambooHR**kifejezést, válassza a **BambooHR** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![BambooHR az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a BambooHR-rel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a BambooHR-ben létre kell hozni a kapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a BambooHR-rel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a BambooHR Single Sign-On --hoz](#configure-bamboohr-single-sign-on)** konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre BambooHR teszt felhasználó](#create-bamboohr-test-user)** - egy megfelelője Britta Simon a BambooHR, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **BambooHR-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![BambooHR Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<company>.bamboohr.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`BambooHR-SAML`

    > [!NOTE]
    > A **Bejelentkezés url-cím** értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a BambooHR ügyféltámogatási csapatával](https://www.bamboohr.com/contact.php) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **BambooHR beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-bamboohr-single-sign-on"></a>BambooHR egyszeri bejelentkezés konfigurálása

1. Egy új ablakban jelentkezzen be a BambooHR vállalati webhelyére rendszergazdaként.

2. A kezdőlapon tegye a következőket:
   
    ![A BambooHR Single Sign-On oldal](./media/bamboo-hr-tutorial/ic796691.png "Egyszeri bejelentkezés")   

    a. Válassza az **Alkalmazások**lehetőséget.
   
    b. Az **Alkalmazások** ablaktáblán válassza az **Egyszeri bejelentkezés**lehetőséget.
   
    c. Válassza **az SAML single sign-on**lehetőséget.

3. Az **SAML egyszeri bejelentkezés** ablaktáblán tegye a következőket:
   
    ![Az SAML egyszeri bejelentkezési ablaktábla](./media/bamboo-hr-tutorial/IC796692.png "SAML egyszeri bejelentkezés")
   
    a. Az **SSO bejelentkezési URL-címmezőbe** illessze be a **bejelentkezési URL-címet,** amelyet az Azure Portalról másolt a 6.
      
    b. A Jegyzettömbben nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmát, majd illessze be az **X.509 tanúsítvány** mezőbe.
   
    c. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a BambooHR hozzáférést biztosít.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **a BambooHR**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza **a BambooHR**lehetőséget.

    ![A BambooHR hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-bamboohr-test-user"></a>BambooHR tesztfelhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezhessenek a BambooHR-be, állítsa be őket manuálisan a BambooHR-ben az alábbi módon:

1. Jelentkezzen be **bamboohr-webhelyére** rendszergazdaként.

2. A felső eszköztáron válassza a **Beállítások lehetőséget.**
   
    ![A Beállítások gomb](./media/bamboo-hr-tutorial/IC796694.png "Beállítás")

3. Válassza az **Áttekintés** lehetőséget.

4. A bal oldali ablaktáblában válassza a **Biztonsági** > **felhasználók lehetőséget.**

5. Írja be a beállítani kívánt érvényes Azure AD-fiók felhasználónevét, jelszavát és e-mail címét.

6. Kattintson a **Mentés** gombra.
        
>[!NOTE]
>Az Azure AD felhasználói fiókok beállítása, a BambooHR felhasználói fiók-létrehozó eszközök vagy API-k is használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a BambooHR csempére kattint, automatikusan be kell jelentkeznie a BambooHR-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

