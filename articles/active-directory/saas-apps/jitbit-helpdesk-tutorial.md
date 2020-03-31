---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Jitbit Helpdeskkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Jitbit Helpdesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 29addcd62afd193af83196b2d942e9778ff3f031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099410"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Oktatóanyag: Az Azure Active Directory integrációja a Jitbit ügyfélszolgálatával

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Jitbit Helpdesket az Azure Active Directoryval (Azure AD).
A Jitbit helpdesk integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Jitbit ügyfélszolgálatához.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Jitbit Helpdesk (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Jitbit Helpdesk szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Jitbit Helpdesk egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Jitbit Helpdesk támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Jitbit ügyfélszolgálat hozzáadása a galériából

A Jitbit Helpdesk Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Jitbit Helpdesk-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Jitbit ügyfélszolgálatot a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Jitbit Helpdesk**kifejezést , válassza a **Jitbit Helpdesk** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Jitbit Helpdesk az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Jitbit Helpdesk-kel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Jitbit Helpdesk létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Jitbit Helpdesk szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Jitbit Helpdesk Single Sign-On](#configure-jitbit-helpdesk-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Jitbit Helpdesk teszt felhasználó](#create-jitbit-helpdesk-test-user)** - egy megfelelője Britta Simon a Jitbit Helpdesk, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Jitbit Helpdesk szolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Jitbit Helpdesk** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Jitbit Helpdesk Domain és URL-címek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Jitbit Helpdesk ügyfélszolgálati csapatával,](https://www.jitbit.com/support/) hogy megkapja ezt az értéket.

    b. Az **Azonosító (entitásazonosító)** mezőbe írja be az URL-címet a következőképpen:`https://www.jitbit.com/web-helpdesk/`

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Jitbit-helpdesk beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>A Jitbit Helpdesk egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Jitbit Helpdesk ügyfélszolgálati webhelyére rendszergazdaként.

1. A felső eszköztáron kattintson a **Felügyelet gombra.**

    ![Felügyelet](./media/jitbit-helpdesk-tutorial/ic777681.png "Adminisztráció")

1. Kattintson **az Általános beállítások gombra.**

    ![Felhasználók, vállalatok és engedélyek](./media/jitbit-helpdesk-tutorial/ic777680.png "Felhasználók, vállalatok és engedélyek")

1. A **Hitelesítési beállítások konfigurálása** csoportban hajtsa végre az alábbi lépéseket:

    ![Hitelesítési beállítások](./media/jitbit-helpdesk-tutorial/ic777683.png "Hitelesítési beállítások")

    a. Jelölje be **az SAML 2.0 egyszeri bejelentkezés engedélyezése**lehetőséget, ha a **OneLogin**segítségével szeretne bejelentkezni az egyszeri bejelentkezéssel ..

    b. Az **EndPoint URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.

    c. Nyissa meg **az alap-64** kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **X.509 tanúsítvány** szövegdobozába

    d. Kattintson **a Módosítások mentése gombra.**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Jitbit Helpdesk hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **Jitbit Helpdesk**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Jitbit Helpdesk**lehetőséget.

    ![A Jitbit Helpdesk hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit helpdesk tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Jitbit Helpdesk szolgáltatásba, ki kell építeni őket a Jitbit Helpdesk-be. Jitbit Helpdesk esetén kiépítése manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Jitbit Helpdesk bérlőjéhez.**

1. A felső menüben kattintson az **Adminisztráció gombra.**

    ![Felügyelet](./media/jitbit-helpdesk-tutorial/ic777681.png "Adminisztráció")

1. Kattintson **a Felhasználók, vállalatok és engedélyek elemre.**

    ![Felhasználók, vállalatok és engedélyek](./media/jitbit-helpdesk-tutorial/ic777682.png "Felhasználók, vállalatok és engedélyek")

1. Kattintson **a Felhasználó hozzáadása**gombra.

    ![Felhasználói csoportok](./media/jitbit-helpdesk-tutorial/ic777685.png "Felhasználó hozzáadása")

1. A Létrehozás szakaszban írja be a kiépíteni kívánt Azure AD-fiók adatait az alábbiak szerint:

    ![Létrehozás](./media/jitbit-helpdesk-tutorial/ic777686.png "Létrehozás")

   a. A **Felhasználónév** mezőbe írja be a felhasználó felhasználónevét, például **BrittaSimon**.

   b. Az **E-mail** mezőbe írja be **BrittaSimon@contoso.com**a felhasználó e-mail címét a programhoz hasonlóan.

   c. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

   d. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

   e. Kattintson **a Létrehozás gombra.**

> [!NOTE]
> A Jitbit Helpdesk súgórendszer-létrehozási eszközeinek vagy API-inak a Jitbit Helpdesk által biztosított bármely más használatával azure AD felhasználói fiókokat hozhat létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Jitbit Helpdesk csempére kattint, automatikusan be kell jelentkeznie a Jitbit ügyfélszolgálatba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
