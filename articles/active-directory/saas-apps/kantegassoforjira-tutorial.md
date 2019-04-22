---
title: 'Oktatóanyag: A JIRA Kantega SSO-val az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a JIRA Kantega SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d12850e094d695aef8eda2602bf526ca9cd65f8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698639"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Oktatóanyag: A JIRA Kantega SSO-val az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a JIRA Kantega SSO integrálása az Azure Active Directory (Azure AD).
A JIRA Kantega SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Kantega SSO JIRA az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Kantega SSO a JIRA (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a JIRA Kantega SSO-val, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* A JIRA egyszeri bejelentkezés Kantega SSO engedélyezése előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* A JIRA támogatja Kantega SSO **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>A JIRA Kantega SSO hozzáadása a katalógusból

Az Azure AD-be a JIRA Kantega SSO-integráció konfigurálásához, hozzá kell Kantega SSO a JIRA a galériából a felügyelt SaaS-alkalmazások listájára.

**A JIRA Kantega SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Kantega SSO a JIRA**, jelölje be **Kantega SSO a JIRA** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A JIRA az eredménylistában Kantega SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés Kantega SSO-val JIRA alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Kantega SSO a JIRA hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a JIRA Kantega SSO-val tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Kantega egyszeri bejelentkezést a JIRA egyszeri bejelentkezés](#configure-kantega-sso-for-jira-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Kantega egyszeri bejelentkezés létrehozása JIRA tesztfelhasználó](#create-kantega-sso-for-jira-test-user)**  – Kantega egyszeri Bejelentkezést a felhasználó Azure ad-ben reprezentációja kapcsolódó JIRA-megfelelője a Britta Simon van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a JIRA Kantega SSO-val, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Kantega SSO a JIRA** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![JIRA-tartomány és URL-címeket egyetlen bejelentkezési információk Kantega SSO](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![JIRA-tartomány és URL-címeket egyetlen bejelentkezési információk Kantega SSO](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

7. Az a **be Kantega egyszeri Bejelentkezést a JIRA** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>JIRA egyszeri bejelentkezésre Kantega SSO konfigurálása

1. Egy másik böngészőablakban a jira Szolgáltatásba való bejelentkezés a helyszíni kiszolgálón rendszergazdaként.

1. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon1.png)

1. A bővítmények lapon szakasz alatt kattintson **új bővítmények keresése**. Keresés **Kantega SSO a JIRA (SAML & Kerberos)** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon2.png)

1. A beépülő modul telepítése elindul.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon3.png)

1. A telepítés befejezése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon33.png)

1.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Új beépülő modul részen **INTEGRÁCIÓK**. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon35.png)

1. Az a **SAML** szakaszban. Válassza ki **Azure Active Directory (Azure AD)** származó a **Hozzáadás identitásszolgáltató** legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon4.png)

1. Válassza ki az előfizetés szintjén, **alapszintű**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon5.png)       

1. Az a **alkalmazás tulajdonságai** szakaszban, hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon6.png)

    a. Másolás a **Alkalmazásazonosító URI-t** értékét, és használja azt **azonosítóját, a válasz URL-cím és a bejelentkezési URL-** a a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    b. Kattintson a **tovább**.

1. Az a **metaadatok importálása** szakaszban, hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon7.png)

    a. Válassza ki **metaadatait tartalmazó fájl a számítógépen**, és az Azure Portalról letöltött feltöltési metaadatait tartalmazó fájl.

    b. Kattintson a **tovább**.

1. Az a **nevét és az egyszeri bejelentkezés helyét** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon8.png)

    a. Adja hozzá az identitásszolgáltató nevét **identitásszolgáltató neve** (például: az Azure AD) szövegmezőbe.

    b. Kattintson a **tovább**.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon9.png)

1. Az a **JIRA felhasználói fiókok** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon10.png)

    a. Válassza ki **felhasználók létrehozása a JIRA a belső könyvtár szükség esetén** , és adja meg a felhasználók számára a megfelelő nevet a csoport (lehet több nem. a vesszővel elválasztott csoportok).

    b. Kattintson a **tovább**.

1. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon11.png)

1. Az a **ismert tartományok az Azure ad** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon12.png)

    a. Válassza ki **tartományok ismert** az oldal bal oldali panelen.

    b. Adja meg a tartomány nevét a **tartományok ismert** szövegmezőbe.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés a JIRA Kantega SSO Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Kantega SSO a JIRA**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Kantega SSO a JIRA**.

    ![A Kantega egyszeri bejelentkezés az alkalmazásainak listájában JIRA-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-kantega-sso-for-jira-test-user"></a>Kantega egyszeri bejelentkezés létrehozása JIRA tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók jira Szolgáltatásba való bejelentkezéshez, akkor ki kell építeni JIRA be. JIRA Kantega egyszeri Bejelentkezést a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA helyileg működtetett kiszolgálón.

1. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user1.png) 

1. A **felhasználókezelés** szakasz lapra, majd **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user2.png) 

1. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user3.png) 

    a. Az a **E-mail-cím** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó például Britta Simon teljes neve.

    c. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a Kantega SSO JIRA csempe a hozzáférési panelen, meg kell lehet automatikusan bejelentkezett a Kantega egyszeri bejelentkezés, JIRA, amelynek beállítása egyszeri bejelentkezés az. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
