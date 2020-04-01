---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a FirmPlay-lel – Munkavállalói érdekképviselet toborzáshoz | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a FirmPlay – Employee Advocacy for Recruiting között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2bdc3a13582f079cc9325ef1c6949c3ae10138eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102521"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Oktatóanyag: Az Azure Active Directory integrációja a FirmPlay-lel – Munkavállalói érdekképviselet a toborzáshoz

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a FirmPlay – Employee Advocacy for Recruiting szolgáltatást az Azure Active Directoryval (Azure AD).
A FirmPlay integrálása – Az Alkalmazottak érdekképviselete az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a FirmPlay - Employee Advocacy for Recruiting szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a FirmPlay - Employee Advocacy for Recruiting (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a FirmPlay – Employee Advocacy for Recruiting szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* FirmPlay - Munkavállalói érdekképviselet egyszeri bejelentkezéses előfizetés felvételéhez

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* FirmPlay - Munkavállalói érdekképviselet toborzástámogatja **SP** kezdeményezett SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>FirmPlay hozzáadása - Munkavállalói érdekképviselet a galériából való toborzáshoz

A FirmPlay - Employee Advocacy for Recruiting azure AD integrációjának konfigurálásához hozzá kell adnia a FirmPlay - Employee Advocacy for Recruiting for Recruiting alkalmazást a galériából a felügyelt SaaS-alkalmazások listájához.

**FirmPlay - Employee Advocacy for Recruiting a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **FirmPlay – Employee Advocacy for Recruiting (Dolgozói érdekképviselet a toborzáshoz**) kifejezést, válassza a **FirmPlay – Employee Advocacy for Recruiting from** result panel (A felvételhez) lehetőséget, majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

     ![FirmPlay - Munkavállalói érdekképviselet a toborzáshoz az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a FirmPlay – Employee Advocacy for Recruiting segítségével egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés hez létre kell hozni egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a FirmPlay – Employee Advocacy for Recruiting alkalmazásban.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a FirmPlay – Employee Advocacy for Recruiting segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a FirmPlay - Employee Advocacy for Recruiting Single Sign-On --t](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre FirmPlay - Employee Advocacy for Recruiting teszt felhasználó](#create-firmplay---employee-advocacy-for-recruiting-test-user)** -, hogy egy megfelelője Britta Simon firmplay - munkavállalói érdekképviselet a toborzás, amely kapcsolódik az Azure AD képviselete a felhasználó.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a FirmPlay – Employee Advocacy for Recruiting segítségével hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **FirmPlay – Employee Advocacy for Recruiting alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![FirmPlay - Munkavállalói érdekképviselet a domain toborzásához és az URL-ek egyszeri bejelentkezési információihoz](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a FirmPlay-lel - Az ügyféltámogató csapat toborzásának munkavállalói érdekképviseletével,](mailto:engineering@firmplay.com) hogy megkapja az értéket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **FirmPlay beállítása – Alkalmazotti érdekképviselet a toborzáshoz** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>A FirmPlay konfigurálása - Munkavállalói érdekképviselet az egyszeri bejelentkezés felvételére

Az egyszeri bejelentkezés konfigurálásához a **FirmPlay - Employee Advocacy for Recruiting** oldalon el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-eket az Azure Portalról a [FirmPlay – Employee Advocacy for Recruiting támogatási csapatnak.](mailto:engineering@firmplay.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a FirmPlay – Munkavállalói érdekképviselet toborzáshoz.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **FirmPlay – Employee Advocacy for Recruiting lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **FirmPlay - Employee Advocacy for Recruiting lehetőséget.**

    ![A FirmPlay - Munkavállalói érdekképviselet a toborzáshoz link az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>FirmPlay létrehozása - Munkavállalói érdekképviselet a tesztfelhasználó toborzására

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a FirmPlay - Employee Advocacy for Recruiting alkalmazásban. Együttműködve [firmplay - Munkavállalói érdekképviselet a toborzás támogatási csapat,](mailto:engineering@firmplay.com) hogy adjunk a felhasználók a FirmPlay - Munkavállalói érdekképviselet a toborzás platform. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a FirmPlay – Employee Advocacy for Recruiting csempére kattint, automatikusan be kell jelentkeznie a FirmPlay – Alkalmazotti érdekképviselet toborzáshoz, amelyhez az Egyszeri bejelentkezést beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

