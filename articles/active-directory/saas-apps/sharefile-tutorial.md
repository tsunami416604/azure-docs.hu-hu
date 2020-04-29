---
title: 'Oktatóanyag: Azure Active Directory a Citrix ShareFile-integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Citrix ShareFile között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160029"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Oktatóanyag: Azure Active Directory integráció a Citrix ShareFile

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Citrix ShareFile Azure Active Directory (Azure AD) használatával.
A Citrix ShareFile és az Azure AD integrálásával a következő előnyöket nyújtja:

* A Citrix ShareFile-hez hozzáférő Azure AD-beli vezérlést is beállíthat.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Citrix ShareFile (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Citrix ShareFile való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Citrix ShareFile egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Citrix ShareFile támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Citrix ShareFile hozzáadása a katalógusból

A Citrix ShareFile az Azure AD-be való integrálásának konfigurálásához a katalógusból Citrix ShareFile kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

**Ha Citrix ShareFile szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Citrix ShareFile**kifejezést, válassza ki a **Citrix ShareFile** az eredmények paneljén, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Citrix ShareFile az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Citrix ShareFile konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Citrix ShareFile kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Citrix ShareFile való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Citrix ShareFile egyszeri bejelentkezés konfigurálása](#configure-citrix-sharefile-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Citrix ShareFile test User](#create-citrix-sharefile-test-user)** -t, hogy a Britta Simon a Citrix ShareFile, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Citrix ShareFile való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Citrix ShareFile** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Citrix ShareFile-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<tenant-name>.sharefile.com/saml/login`

    b. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez lépjen kapcsolatba a [Citrix ShareFile ügyfél-támogatási csapatával](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Citrix ShareFile beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-citrix-sharefile-single-sign-on"></a>Citrix ShareFile egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **Citrix ShareFile** vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a **rendszergazda**elemre.

3. A bal oldali navigációs panelen válassza az **egyszeri bejelentkezés konfigurálása**lehetőséget.
   
    ![Fiókkezelés](./media/sharefile-tutorial/ic773627.png "Fiókkezelés")

4. Az **egyszeri bejelentkezés/SAML 2,0 konfigurációs** párbeszédablak **alapszintű beállítások**területén hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés](./media/sharefile-tutorial/ic773628.png "Egyszeri bejelentkezés")
   
    a. Kattintson az **SAML engedélyezése**lehetőségre.
    
    b. A **identitásszolgáltató-kiállító/entitás-azonosító** szövegmezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Kattintson az **X. 509 tanúsítvány** mező melletti **módosítás** elemre, majd töltse fel a letöltött tanúsítványt a Azure Portal.
    
    d. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.
    
    e. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

5. Kattintson a **Save (Mentés** ) gombra a Citrix ShareFile felügyeleti portálon.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Citrix ShareFile-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Citrix ShareFile**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **Citrix ShareFile**elemet.

    ![A Citrix ShareFile hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Citrix ShareFileba, a Citrix ShareFile-ben kell kiépíteni őket. Citrix ShareFile esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Citrix ShareFile** -bérlőbe.

2. Kattintson a **felhasználók \> kezelése Kezdőlap \> + létrehozás alkalmazott**elemre.
   
    ![Alkalmazott létrehozása](./media/sharefile-tutorial/IC781050.png "Alkalmazott létrehozása")

3. Az **alapszintű információ** szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Alapszintű információk](./media/sharefile-tutorial/IC799951.png "Alapszintű információk")
   
    a. Az **E-mail cím** szövegmezőbe írja be a Britta Simon e-mail- **címét\@brittasimon contoso.com**néven.
   
    b. Az **Utónév** szövegmezőbe írja be a felhasználó **Britta** **nevet** .
   
    c. A **vezetéknév** szövegmezőbe írja be a felhasználó **vezetéknevét** **Simon**néven.

4. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.
  
    >[!NOTE]
    >Az Azure AD-fiók tulajdonosa egy e-mailt kap, és egy hivatkozást követve megerősíti a fiókját, mielőtt az aktívvá válna. Az Azure AD felhasználói fiókjainak kiépítéséhez a Citrix ShareFile által biztosított bármely más Citrix ShareFile felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Citrix ShareFile csempére kattint, automatikusan be kell jelentkeznie a Citrix ShareFile, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

