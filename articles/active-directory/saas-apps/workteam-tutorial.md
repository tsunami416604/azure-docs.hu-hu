---
title: 'Oktatóanyag: Azure Active Directory integráció a Workteam-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Workteam között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d628b12171cd67455ea308a2ca2b29e67855cccc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825613"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Oktatóanyag: Azure Active Directory integráció a Workteam

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Workteam a Azure Active Directory (Azure AD) szolgáltatással.
A Workteam és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Workteam.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Workteam (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Workteam való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Workteam egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Workteam támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO

## <a name="adding-workteam-from-the-gallery"></a>Workteam hozzáadása a gyűjteményből

A Workteam Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Workteam a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Workteam szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Workteam**kifejezést, válassza a **Workteam** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Workteam az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Workteam-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Workteam kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Workteam való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Workteam egyszeri bejelentkezés konfigurálása](#configure-workteam-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Workteam-teszt felhasználót](#create-workteam-test-user)** – hogy a Workteam Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Workteam való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Workteam** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![Workteam tartomány és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Workteam tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://app.workte.am`

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. A **Workteam beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-workteam-single-sign-on"></a>Workteam egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workteam biztonsági rendszergazdaként.

2. A jobb felső sarokban kattintson a **profil emblémára** , majd a **szervezeti beállítások**elemre. 

    ![Workteam-beállítások](./media/workteam-tutorial/tutorial_workteam_settings.png)

3. A **hitelesítés** szakaszban kattintson a **Beállítások emblémára**.

     ![Workteam az Azure-ban](./media/workteam-tutorial/tutorial_workteam_azure.png)

4. Az **SAML-beállítások** lapon hajtsa végre a következő lépéseket:

     ![Workteam SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Válassza ki az **SAML-identitásszolgáltató** az **ad Azure**-ban.

    b. Az **SAML egyszeri bejelentkezési szolgáltatás URL-címe** szövegmezőbe illessze be a **bejelentkezési URL**-cím értékét, amelyet a Azure Portal másolt.

    c. Az **SAML-entitás azonosítója** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító**értékét.

    d. Nyissa meg a Jegyzettömbben a Azure Portal letöltött **Base-64 kódolású tanúsítványt** , másolja a tartalmát, majd illessze be az **SAML aláíró tanúsítvány (Base64)** mezőbe.

    e. Kattintson az **OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Workteam hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Workteam**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Workteam**lehetőséget.

    ![Az Workteam hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-workteam-test-user"></a>Workteam-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Workteam, a Workteam kell kiépíteni őket. A Workteam-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Workteam biztonsági rendszergazdaként.

2. A **szervezeti beállítások** lap felső középső részén kattintson a **felhasználók** elemre, majd az **új felhasználó**lehetőségre.

    ![Workteam-felhasználó](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Az **új alkalmazott** lapon hajtsa végre a következő lépéseket:

    ![Workteam newuser](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. A **név** szövegmezőbe írja be a felhasználó utónevét (például **Brittasimon**).

    b. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **Brittasimon\@contoso.com**-e-mail-címét.

    c. Kattintson az **OK** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Workteam csempére kattint, automatikusan be kell jelentkeznie arra a Workteam, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

