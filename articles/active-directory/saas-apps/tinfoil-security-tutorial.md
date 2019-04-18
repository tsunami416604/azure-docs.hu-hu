---
title: 'Oktatóanyag: A TINFOIL SECURITY szolgáltatással az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a TINFOIL SECURITY között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262380"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Oktatóanyag: A TINFOIL SECURITY szolgáltatással az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan teljesíteni a TINFOIL SECURITY integrálása az Azure Active Directory (Azure AD).
TINFOIL SECURITY integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a TINFOIL SECURITY az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a TINFOIL SECURITY (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a TINFOIL SECURITY szolgáltatással, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* TINFOIL SECURITY egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* A TINFOIL SECURITY támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-tinfoil-security-from-the-gallery"></a>TINFOIL SECURITY hozzáadása a katalógusból

Az Azure AD integrálása a TINFOIL SECURITY konfigurálásához hozzá kell teljesíteni a TINFOIL SECURITY a galériából a felügyelt SaaS-alkalmazások listájára.

**TINFOIL SECURITY hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **teljesíteni a TINFOIL SECURITY**válassza **teljesíteni a TINFOIL SECURITY** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a TINFOIL SECURITY](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a TINFOIL SECURITY szolgáltatással nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó a TINFOIL SECURITY felhasználói hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a TINFOIL SECURITY szolgáltatással tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A TINFOIL SECURITY egyszeri bejelentkezés konfigurálása](#configure-tinfoil-security-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre teljesíteni a TINFOIL SECURITY tesztfelhasználót](#create-tinfoil-security-test-user)**  – egy megfelelője a Britta Simon kell teljesíteni a TINFOIL SECURITY, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a TINFOIL SECURITY szolgáltatással, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **teljesíteni a TINFOIL SECURITY** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![A TINFOIL SECURITY tartomány és URL-címek egyszeri bejelentkezési adatait](common/preintegrated.png)

5. A TINFOIL SECURITY-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

        ![image](common/edit-attribute.png)

6. Emellett a fentiekben teljesíteni a TINFOIL SECURITY-alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum |
    | ------------------- | -------------|
    | fiókazonosító | UXXXXXXXXXXXXX |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** szövegmezőjébe illessze be a Fiókazonosító-értéket, amelyet később az oktatóanyag fog kapni.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **SAML-aláíró tanúsítvány** területén kattintson **szerkesztése** gombra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel.

    ![SAML-aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

8. Az a **SAML-aláíró tanúsítvány** területén másolja a **ujjlenyomat** , és mentse a számítógépre.

    ![Másolja ki az ujjlenyomat értéket](common/copy-thumbprint.png)

9. Az a **állítsa be a TINFOIL SECURITY** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-tinfoil-security-single-sign-on"></a>A TINFOIL SECURITY egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a TINFOIL SECURITY vállalati hely rendszergazdaként.

2. A felső eszköztáron kattintson **My Account**.
   
    ![Irányítópult](./media/tinfoil-security-tutorial/ic798971.png "irányítópult")

3. Kattintson a **biztonsági**.
   
    ![Biztonsági](./media/tinfoil-security-tutorial/ic798972.png "biztonsági")

4. Az a **egyszeri bejelentkezés** konfiguráció lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés](./media/tinfoil-security-tutorial/ic798973.png "egyszeri bejelentkezés")
   
    a. Válassza ki **SAML engedélyezése**.
   
    b. Kattintson a **manuális konfigurációs**.
   
    c. A **SAML Post URL-címe** szövegmezőjébe illessze be az értéket, **bejelentkezési URL-cím** Azure Portalról másolt
   
    d. A **SAML-tanúsítvány ujjlenyomat** szövegmezőjébe illessze be az értéket, **ujjlenyomat** másolta amely **SAML-aláíró tanúsítvány** szakasz.
  
    e. Másolás **a Fiókazonosító** értékét, és illessze be az értéket a **attribútumérték** szövegmező alatt **attribútum hozzáadása** szakaszban az Azure Portalon.
   
    f. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a TINFOIL SECURITY Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **teljesíteni a TINFOIL SECURITY**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **teljesíteni a TINFOIL SECURITY**.

    ![A TINFOIL SECURITY hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-tinfoil-security-test-user"></a>TINFOIL SECURITY tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a TINFOIL SECURITY az Azure AD-felhasználók, akkor ki kell építeni teljesíteni a TINFOIL SECURITY be. TINFOIL SECURITY, esetén kézi tevékenység kiépítése.

**Kiépített felhasználó lekérése, hajtsa végre az alábbi lépéseket:**

1. Ha a felhasználó egy vállalati fiók tagja, akkor kell [fel a kapcsolatot a TINFOIL SECURITY csapatával](https://www.tinfoilsecurity.com/contact) beolvasni a létrehozott felhasználói fiókkal.

1. Ha a felhasználó egy rendszeres teljesíteni a TINFOIL SECURITY SaaS-felhasználó, majd a felhasználót adhat hozzá egy közreműködő a felhasználó webhelyek bármelyikéhez. Ez elindít egy folyamatot, hogy hozzon létre egy új teljesíteni a TINFOIL SECURITY-felhasználói fiókot a megadott e-mail-meghívó küldése.

> [!NOTE]
> TINFOIL SECURITY felhasználói fiók létrehozása eszközöket és teljesíteni a TINFOIL SECURITY által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.
> 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a TINFOIL SECURITY csempére kattint, meg kell lehet automatikusan bejelentkezett a TINFOIL SECURITY, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

