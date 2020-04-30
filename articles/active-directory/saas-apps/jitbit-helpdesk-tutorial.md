---
title: 'Oktatóanyag: Azure Active Directory integráció a Jitbit ügyfélszolgálattal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Jitbit helpdesk között.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099410"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Oktatóanyag: Azure Active Directory integráció a Jitbit ügyfélszolgálattal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Jitbit segélyszolgálat Azure Active Directory (Azure AD) szolgáltatással.
A Jitbit helpdesk Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Jitbit ügyfélszolgálatához.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Jitbit segélyszolgálat (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Jitbit ügyfélszolgálattal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Jitbit segélyszolgálat egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Jitbit helpdesk támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Jitbit helpdesk hozzáadása a katalógusból

A Jitbit segélyszolgálat Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Jitbit segélyszolgálat szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Jitbit segélyszolgálat a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Jitbit segélyszolgálat**kifejezést, válassza a **Jitbit segélyszolgálat** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Jitbit segélyszolgálat az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Jitbit ügyfélszolgálattal konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Jitbit segélyszolgálat kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Jitbit ügyfélszolgálattal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[Jitbit segélyszolgálat egyszeri bejelentkezésének konfigurálása](#configure-jitbit-helpdesk-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Jitbit helpdesk-teszt felhasználó létrehozása](#create-jitbit-helpdesk-test-user)** – ha a felhasználó Azure ad-Britta összekapcsolt Jitbit helpdesk-beli partneri kapcsolattal rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Jitbit ügyfélszolgálattal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Jitbit helpdesk** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Jitbit segélyszolgálat-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Jitbit helpdesk](https://www.jitbit.com/support/) ügyfélszolgálatával.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következőképpen:`https://www.jitbit.com/web-helpdesk/`

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Jitbit-segélyszolgálat beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Az Jitbit segélyszolgálat egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Jitbit segélyszolgálat vállalati webhelyére.

1. A felső eszköztáron kattintson az **Adminisztráció**elemre.

    ![Felügyelet](./media/jitbit-helpdesk-tutorial/ic777681.png "Felügyelet")

1. Kattintson az **általános beállítások**elemre.

    ![Felhasználók, vállalatok és engedélyek](./media/jitbit-helpdesk-tutorial/ic777680.png "Felhasználók, vállalatok és engedélyek")

1. A **hitelesítési beállítások** konfigurálása szakaszban hajtsa végre a következő lépéseket:

    ![Hitelesítési beállítások](./media/jitbit-helpdesk-tutorial/ic777683.png "Hitelesítési beállítások")

    a. Válassza az **SAML 2,0 egyszeri bejelentkezés engedélyezése**lehetőséget az egyszeri bejelentkezés (SSO) és a **OneLogin**használatával történő bejelentkezéshez.

    b. A **végpont URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. Nyissa meg a **Base-64** kódolású tanúsítványt a Jegyzettömbben, másolja vágólapra a tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    d. Kattintson a **módosítások mentése**gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt:**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Jitbit segélyszolgálat elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Jitbit segélyszolgálat**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Jitbit segélyszolgálat**elemet.

    ![Az Jitbit segélyszolgálat hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit segélyszolgálat-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Jitbit Segélyszolgálatba, ki kell építeni őket a Jitbit helpdesk szolgáltatásba. Jitbit segélyszolgálat esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Jitbit helpdesk** -bérlőbe.

1. A felső menüben kattintson az **Adminisztráció**elemre.

    ![Felügyelet](./media/jitbit-helpdesk-tutorial/ic777681.png "Felügyelet")

1. Kattintson **a felhasználók, vállalatok és engedélyek**elemre.

    ![Felhasználók, vállalatok és engedélyek](./media/jitbit-helpdesk-tutorial/ic777682.png "Felhasználók, vállalatok és engedélyek")

1. Kattintson a **felhasználó hozzáadása**elemre.

    ![Felhasználói csoportok](./media/jitbit-helpdesk-tutorial/ic777685.png "Felhasználó hozzáadása")

1. A létrehozás szakaszban adja meg az Azure AD-fiók által a következőképpen kiépített adatmennyiséget:

    ![Létrehozás](./media/jitbit-helpdesk-tutorial/ic777686.png "Létrehozás")

   a. A **Felhasználónév** szövegmezőbe írja be annak a felhasználónak a felhasználónevét, mint a **BrittaSimon**.

   b. Az **e-mail** szövegmezőbe írja be a felhasználó e-mail címét, **BrittaSimon@contoso.com**például:.

   c. Az **Utónév** szövegmezőbe írja be a felhasználó nevét (például **Britta**).

   d. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

   e. Kattintson a **Létrehozás**gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Jitbit helpdesk által biztosított Jitbit-segélyszolgálat felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Jitbit helpdesk csempére kattint, automatikusan be kell jelentkeznie arra a Jitbit-Helpdeskre, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
