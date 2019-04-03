---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Questetra BPM Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Questetra BPM Suite és az Azure Active Directory között.
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
ms.openlocfilehash: bcc1930bf099955af9716a04aa73fb7b9496aa88
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848964"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Oktatóanyag: Az Azure Active Directory-integráció Questetra BPM Suite-tal

Ebben az oktatóanyagban elsajátíthatja, hogyan Questetra BPM Suite integrálható az Azure Active Directory (Azure AD).
Questetra BPM Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Questetra BPM Suite Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Questetra BPM Suite (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Questetra BPM Suite, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Questetra BPM Suite egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Questetra BPM Suite **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Questetra BPM Suite hozzáadása a katalógusból

Az Azure AD-be Questetra BPM Suite integrációjának konfigurálása, hozzá kell Questetra BPM Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**Questetra BPM Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Questetra BPM Suite**, jelölje be **Questetra BPM Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Questetra BPM Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Questetra BPM Suite nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Questetra BPM Suite hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Questetra BPM Suite tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Questetra BPM Suite egyszeri bejelentkezés konfigurálása](#configure-questetra-bpm-suite-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Questetra BPM Suite tesztfelhasználót](#create-questetra-bpm-suite-test-user)**  – egy megfelelője a Britta Simon Questetra BPM csomag, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Questetra BPM Suite-tal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Questetra BPM Suite** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Questetra BPM Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Beszerezheti ezeket az értéteket **SP információk** szakaszában a **Questetra BPM Suite** vállalati webhelyre, ahol később az oktatóanyag, vagy forduljon a [Questetra BPM Suite ügyfelek támogatása csapat](https://www.questetra.com/contact/). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Questetra BPM Suite beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Suite Questetra BPM egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **Questetra BPM Suite** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **rendszerbeállítások**. 
   
    ![Az Azure AD egyszeri bejelentkezés][10]

3. Megnyitásához a **SingleSignOnSAML** kattintson **egyszeri bejelentkezés (SAML)**. 
   
    ![Az Azure AD egyszeri bejelentkezés][11]

4. Az a **Questetra BPM Suite** vállalati hely, az a **SP információkat** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Másolás a **ACS URL**, és illessze be azt a **bejelentkezési URL-** szövegmezőjébe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalról.
    
    b. Másolás a **Entitásazonosító**, és illessze be azt a **azonosító** szövegmezőjébe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalról.

5. Az a **Questetra BPM Suite** vállalati hely, hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][15]
   
    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.
   
    b. A **Entitásazonosító** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító** Azure Portalról másolt.
    
    c. A **bejelentkezési oldal URL** szövegmezőjébe illessze be az értéket, **bejelentkezési URL-cím** Azure Portalról másolt.
    
    d. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.
    
    e. Az a **nameid-formátumához** szövegmezőbe írja be `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Nyissa meg a **Base-64** kódolt tanúsítvány a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **érvényesítési tanúsítvány** szövegmezőbe. 

    g. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Questetra BPM Suite Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Questetra BPM Suite**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Questetra BPM Suite**.

    ![Az alkalmazások listáját a Questetra BPM Suite hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-questetra-bpm-suite-test-user"></a>Questetra BPM Suite tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Questetra BPM Suite nevű felhasználó létrehozásához.

**Britta Simon Questetra BPM Suite nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Questetra BPM Suite vállalati webhely.

2. Lépjen a **Rendszerbeállítások > Felhasználólista > Új felhasználó**.
 
3. Új felhasználó párbeszédpanelen hajtsa végre az alábbi lépéseket: 
   
    ![Tesztfelhasználó létrehozása][300] 
   
    a. Az a **neve** szövegmezőbe írja be **neve** felhasználó britta.simon@contoso.com.
   
    b. Az a **E-mail** szövegmezőbe írja be **e-mail** felhasználó britta.simon@contoso.com.
   
    c. Az a **jelszó** szövegmezőben adjon meg egy **jelszó** felhasználó.
    
    d. Kattintson a **új felhasználó hozzáadása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Questetra BPM Suite csempére kattint, akkor kell automatikusan megtörténik a az Questetra BPM Suite, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png