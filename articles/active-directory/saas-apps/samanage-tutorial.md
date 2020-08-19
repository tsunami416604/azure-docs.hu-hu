---
title: 'Oktatóanyag: Azure Active Directory integráció a Samanage-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Samanage között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 56018ff0be07a48cf9448b9b92de5694ebac18bc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543533"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Oktatóanyag: Azure Active Directory integráció a Samanage

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Samanage a Azure Active Directory (Azure AD) szolgáltatással.
A Samanage és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Samanage.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Samanage (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Samanage való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Samanage egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Samanage támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-samanage-from-the-gallery"></a>Samanage hozzáadása a gyűjteményből

A Samanage Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Samanage a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Samanage szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Samanage**kifejezést, válassza a **Samanage** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Samanage az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Samanage-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Samanage kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Samanage való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Samanage egyszeri bejelentkezés konfigurálása](#configure-samanage-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Samanage-teszt felhasználót](#create-samanage-test-user)** – hogy a Samanage Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Samanage való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Samanage** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Samanage tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval, amelyet az oktatóanyag későbbi részében ismertet. További részletekért forduljon a [Samanage ügyfél-támogatási csapatához](https://www.samanage.com/support). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Samanage beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-samanage-single-sign-on"></a>Samanage egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Samanage vállalati webhelyre rendszergazdaként.

2. Kattintson az **irányítópult** elemre, és válassza a **beállítás** lehetőséget a bal oldali navigációs panelen.
   
    ![Irányítópult](./media/samanage-tutorial/tutorial_samanage_001.png "Irányítópult")

3. Kattintson az **egyszeri bejelentkezés**elemre.
   
    ![Egyszeri bejelentkezés](./media/samanage-tutorial/tutorial_samanage_002.png "Egyszeri bejelentkezés")

4. Navigáljon a **Bejelentkezés az SAML használatával** szakaszban, hajtsa végre a következő lépéseket:
   
    ![Bejelentkezés az SAML használatával](./media/samanage-tutorial/tutorial_samanage_003.png "Bejelentkezés az SAML használatával")
 
    a. Kattintson **az egyszeri bejelentkezés engedélyezése SAML**használatával lehetőségre.  
 
    b. Az **identitás-szolgáltató URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.    
 
    c. Erősítse meg, hogy a **bejelentkezési URL-cím** megegyezik a Azure Portal **ALAPszintű SAML-konfigurációs** szakaszának **bejelentkezési URL-címével** .
 
    d. A **kijelentkezési URL** szövegmezőbe írja be a Azure Portalból másolt **KIJELENTKEZÉSI URL-cím** értékét.
 
    e. Az **SAML-kiállító** szövegmezőbe írja be az azonosító URI-jét az identitás-szolgáltatóba.
 
    f. Nyissa meg Azure Portal a Jegyzettömbből letöltött Base-64 kódolású tanúsítványt, másolja a vágólapra a tartalmát, majd illessze be az **Identity Provider x. 509-tanúsítvány beillesztése** szövegmezőbe.
 
    : Kattintson **a felhasználók létrehozása lehetőségre, ha nem léteznek a Samanage-ben**.
 
    h. Kattintson a **Frissítés** parancsra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Samanage hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Samanage**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Samanage**lehetőséget.

    ![Az Samanage hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-samanage-test-user"></a>Samanage-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Samanage, a Samanage kell kiépíteni őket.  
Samanage esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Samanage vállalati webhelyre rendszergazdaként.

2. Kattintson az **irányítópult** elemre, és válassza a **beállítás** elemet a bal oldali navigációs sávon.
   
    ![Beállítás](./media/samanage-tutorial/tutorial_samanage_001.png "Telepítés")

3. Kattintson a **felhasználók** lapra
   
    ![Felhasználók](./media/samanage-tutorial/tutorial_samanage_006.png "Felhasználók")

4. Kattintson az **új felhasználó**elemre.
   
    ![Új felhasználó](./media/samanage-tutorial/tutorial_samanage_007.png "Új felhasználó")

5. Írja be a létrehozni kívánt Azure Active Directory fiók **nevét** és **e-mail-címét** , majd kattintson a **felhasználó létrehozása**lehetőségre.
   
    ![Felhasználó létrehozása](./media/samanage-tutorial/tutorial_samanage_008.png "Felhasználó létrehozása")
   
   >[!NOTE]
   >A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve erősítse meg a fiókját, mielőtt az aktívvá válna. A Samanage által biztosított egyéb Samanage-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Samanage csempére kattint, automatikusan be kell jelentkeznie arra a Samanage, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

