---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SuccessFactors |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SuccessFactors között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37130d71c3d8dfc9ad4cba68ce24fddf82c05959
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162765"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SuccessFactors

Ebben az oktatóanyagban elsajátíthatja, hogyan SuccessFactors integrálása az Azure Active Directory (Azure AD).
SuccessFactors integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá SuccessFactors Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve SuccessFactors (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

SuccessFactors az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SuccessFactors egyszeri bejelentkezéses engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a SuccessFactors **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors hozzáadása a katalógusból

Az Azure AD integrálása a SuccessFactors konfigurálásához hozzá kell SuccessFactors a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SuccessFactors hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SuccessFactors**válassza **SuccessFactors** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában SuccessFactors](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SuccessFactors nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó SuccessFactors hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SuccessFactors tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SuccessFactors egyszeri bejelentkezés konfigurálása](#configure-successfactors-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre SuccessFactors tesztfelhasználót](#create-successfactors-test-user)**  – egy megfelelője a Britta Simon SuccessFactors, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SuccessFactors, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **SuccessFactors** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SuccessFactors tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [SuccessFactors ügyfél-támogatási csapatának](https://www.successfactors.com/content/ssf-site/en/support.html) beolvasni ezeket az értékeket.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **SuccessFactors beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-successfactors-single-sign-on"></a>SuccessFactors egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **SuccessFactors felügyeleti portál** rendszergazdaként.

2. Látogasson el **alkalmazásbiztonsági** natívan **egyszeri bejelentkezést a szolgáltatás**.

3. Helyezze el az értéket a **Token alaphelyzetbe** kattintson **Token mentése** SAML SSO engedélyezése.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása][11]

    > [!NOTE]
    > Ez az érték a be-és kikapcsolása kapcsoló szolgál. Bármilyen érték mentette, a SAML SSO-e be. Ha egy üres értéket a rendszer menti a SAML SSO OFF állásban.

4. Az alábbi képernyőfelvétel natív, és hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása][12]
  
    a. Válassza ki a **SAML SSO-v2** választógomb
  
    b. Állítsa be a **SAML, amely azt mutatja, entitás neve**(például az SAML-kibocsátó + vállalat neve).

    c. Az a **kiállítójának URL-címe** szövegmezőjébe illessze be a **az Azure AD-azonosító** az Azure Portalról másolt érték.

    d. Válassza ki **helyességi feltétel** , **kötelező kötelező aláírás**.

    e. Válassza ki **engedélyezve** , **SAML jelző engedélyezése**.

    f. Válassza ki **nem** , **bejelentkezési kérelem-aláírás (SF generált/SP/RP)**.

    g. Válassza ki **böngésző/Post profil** , **SAML-alapú profilban**.

    h. Válassza ki **nem** , **kényszerítése a tanúsítvány érvényes időtartama**.

    i. Másolja a letöltött tanúsítvány-fájl tartalmát az Azure Portalról, és illessze be azt a **SAML-tanúsítvány ellenőrzése** szövegmezőbe.

    > [!NOTE] 
    > A tanúsítványok tartalmát kell kezdődnie tanúsítvány és a záró tanúsítvány címkéket.

5. Keresse meg az SAML-V2, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása][13]

    a. Válassza ki **Igen** , **támogatja az SP által kezdeményezett globális kijelentkezési**.

    b. Az a **globális kijelentkezési szolgáltatás URL-címe (LogoutRequest cél)** szövegmezőjébe illessze be a **kijelentkezéses URL-cím** érték, amely másolta űrlapon az Azure Portalon.

    c. Válassza ki **nem** , **sp titkosítani kell az összes NameID elem megkövetelése**.

    d. Válassza ki **meghatározatlan** , **nameid-formátumához**.

    e. Válassza ki **Igen** , **engedélyezése sp által kezdeményezett bejelentkezési (AuthnRequest)**.

    f. Az a **küldési kérelmek, mint a céges szintű kiállító** szövegmezőjébe illessze be **bejelentkezési URL-cím** az Azure Portalról másolt érték.

6. Hajtsa végre ezeket a lépéseket, ha azt szeretné, hogy a bejelentkezési felhasználónevek megkülönbözteti a kis-és nagybetű nincs megkülönböztetve.

    ![Egyszeri bejelentkezés konfigurálása][29]

    a. Látogasson el **vállalati beállítások**(aljának közelében).

    b. Jelölje be a jelölőnégyzetet közel **engedélyezése eset – bizalmas adatokkal nem rendelkező felhasználónév**.

    c. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Ha ezt engedélyezni szeretné, a rendszer ellenőrzi, ha létrehoz egy ismétlődő SAML-bejelentkezési név. Például ha az ügyfél a felhasználó1, user1 felhasználónevek rendelkezik. Kis-és nagybetűk azonnal véve teszi ezeket az ismétlődéseket. A rendszer nem engedélyezi a szolgáltatást, és a egy hibaüzenetet kínálja. Az ügyfél kell módosítani a felhasználónevek egyikét, így más van írva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SuccessFactors Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SuccessFactors**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SuccessFactors**.

    ![Az alkalmazások listáját a SuccessFactors hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-successfactors-test-user"></a>SuccessFactors tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SuccessFactors jelentkezzen be, akkor ki kell építeni SuccessFactors be. SuccessFactors, esetén kiépítése a manuális feladat.

SuccessFactors létrehozott felhasználók lekéréséhez lépjen kapcsolatba kell a [SuccessFactors támogatási csoportjának](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SuccessFactors csempére kattint, meg kell lehet automatikusan bejelentkezett a SuccessFactors, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
