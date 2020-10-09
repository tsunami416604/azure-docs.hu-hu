---
title: 'Oktatóanyag: Azure Active Directory integráció a Marketo-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Marketo között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 764f01fa5966a6620612405b4df37fc5ff44f33a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91857911"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Oktatóanyag: Azure Active Directory integráció a Marketo

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Marketo a Azure Active Directory (Azure AD) szolgáltatással.
A Marketo és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Marketo.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Marketo (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Marketo való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Marketo egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Marketo támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-marketo-from-the-gallery"></a>Marketo hozzáadása a gyűjteményből

A Marketo Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Marketo a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Marketo szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Marketo**kifejezést, válassza a **Marketo** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Marketo az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Marketo-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Marketo kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Marketo való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Marketo egyszeri bejelentkezés konfigurálása](#configure-marketo-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Marketo-teszt felhasználót](#create-marketo-test-user)** – hogy a Marketo Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Marketo való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Marketo** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![Marketo tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://saml.marketo.com/sp`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez forduljon a Marketo ügyfélszolgálati [csapatához](https://investors.marketo.com/contactus.cfm) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Marketo beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-marketo-single-sign-on"></a>Marketo egyetlen Sign-On konfigurálása

1. Az alkalmazás Munchkin-azonosítójának lekéréséhez jelentkezzen be a Marketo rendszergazdai hitelesítő adatokkal, és hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.
   
    b. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![A képernyőképen a navigációs ablaktáblán kiválasztott rendszergazda látható.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigáljon az integráció menüre, és kattintson a **Munchkin hivatkozásra**.
   
    ![A képernyőképen az integrációból kiválasztott Munchkin látható.](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Másolja a képernyőn megjelenő Munchkin-azonosítót, és fejezze be a válasz URL-címét az Azure AD konfigurációs varázslóban.
   
    ![Képernyőfelvétel: a Munchkin oldal, amelyen átmásolhatja az I D fiókot.](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Az egyszeri bejelentkezés az alkalmazásban való konfigurálásához kövesse az alábbi lépéseket:
   
    a. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.
   
    b. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![A képernyőképen a navigációs ablaktáblán kiválasztott rendszergazda látható.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigáljon az integráció menüre, és kattintson az **egyszeri bejelentkezés**lehetőségre.
   
    ![Képernyőfelvétel: az integrációból kiválasztott egyetlen Sign-On.](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Az SAML-beállítások engedélyezéséhez kattintson a **Szerkesztés** gombra.
   
    ![Képernyőfelvétel: a Szerkesztés gombra kattintva megtekintheti az S S O-beállításokat.](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Engedélyezve** Egyetlen Sign-On beállítások.
   
    f. Illessze be az **Azure ad-azonosítót**a **kiállító azonosítója** szövegmezőbe.
   
    : Az **entitás-azonosító** szövegmezőbe írja be az URL-címet `http://saml.marketo.com/sp` .
   
    h. Válassza ki a felhasználói azonosító helyét **név-azonosító elemként**.
   
    ![A képernyőképen az SAML-beállítások szerkesztése látható, ahol megadhatja a leírt értékeket.](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Ha a felhasználói azonosító nem UPN-érték, akkor módosítsa az értéket az attribútum lapon.
   
    i. Töltse fel a tanúsítványt, amelyet az Azure AD konfigurációs varázslójával töltött le. **Mentse** a beállításokat.
   
    j. Az átirányítási lapok beállításainak szerkesztése.
   
    k. Illessze be a **bejelentkezési URL-címet** a **bejelentkezési URL** szövegmezőbe.
   
    l. Illessze be a **kijelentkezési URL-címet** a **KIjelentkezési URL** -szövegmezőbe.
   
    m. A **hiba URL-címében**másolja a **Marketo-példány URL-címét** , és kattintson a **Save (Mentés** ) gombra a beállítások mentéséhez.
   
    ![Képernyőfelvétel: az átirányítási lapok szerkesztése párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Ha engedélyezni szeretné az egyszeri bejelentkezést a felhasználók számára, hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.
   
    b. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![A képernyőképen a navigációs ablaktáblán kiválasztott rendszergazda látható.](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Lépjen a **Biztonság** menüre, és kattintson a **bejelentkezési beállítások**elemre.
   
    ![A képernyőképen a biztonság lehetőség közül kiválasztott bejelentkezési beállítások láthatók.](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Keresse meg az **egyszeri bejelentkezés megkövetelése** beállítást, és **mentse** a beállításokat.
   
    ![Képernyőfelvétel: a jelszó erősségének beállítása, ahol kiválaszthatja a szükséges S S O-t.](./media/marketo-tutorial/tutorial_marketo_14.png)

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

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Marketo hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Marketo**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Marketo**lehetőséget.

    ![Az Marketo hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-marketo-test-user"></a>Marketo-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Marketo-ben. a következő lépésekkel hozhat létre felhasználót a Marketo platformon.

1. Jelentkezzen be a Marketo alkalmazásba a rendszergazdai hitelesítő adatok használatával.

2. Kattintson a **rendszergazda** gombra a felső navigációs ablaktáblán.
   
    ![A képernyőképen a navigációs ablaktáblán kiválasztott rendszergazda látható.](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigáljon a **Biztonság** menüre, és kattintson a **felhasználók & szerepkörök** elemre.
   
    ![A képernyőképen a felhasználók & a Biztonság elemre kiválasztott szerepköröket jeleníti meg.](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kattintson az **új felhasználó meghívása** hivatkozásra a felhasználók lapon
   
    ![Képernyőfelvétel: új felhasználó meghívása a felhasználók lapon.](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Az új felhasználó meghívása varázsló adja meg a következő adatokat
   
    a. Adja meg a felhasználó **e-mail-** címét a szövegmezőben
   
    ![A képernyőképen az új felhasználó meghívása varázsló első lépése látható, ahol megadhatja a felhasználói adatokat.](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Adja meg az **első nevet** a szövegmezőben
   
    c. Adja meg az **utolsó nevet**  a szövegmezőben
   
    d. Kattintson a **tovább** gombra

6. Az **engedélyek** lapon válassza ki a **userRoles** , és kattintson a **tovább** gombra.
   
    ![A képernyőképen az új felhasználó meghívása varázsló első lépése látható, ahol megadhatja az engedélyeket.](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kattintson a **Küldés** gombra a felhasználó meghívásának elküldéséhez
   
    ![A képernyőképen az új felhasználó meghívása varázsló első lépése látható, ahol megadhatja az üzenetet.](./media/marketo-tutorial/tutorial_marketo_18.png)

8. A felhasználó megkapja az e-mail-értesítést, és a hivatkozásra kattintva megváltoztathatja a jelszót a fiók aktiválásához. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Marketo csempére kattint, automatikusan be kell jelentkeznie arra a Marketo, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

