---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Facebook által munkahelyi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a munkahely által Facebook között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0dac2bee3c7aff77e782cc167898534ae40cfd3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772398"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Oktatóanyag: Az Azure Active Directory-integráció a Facebook által munkahelyi

Ebben az oktatóanyagban elsajátíthatja a Facebook munkahelyi integrálása az Azure Active Directory (Azure AD).
Munkahelyi által Facebook integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure AD, aki hozzáféréssel rendelkezik a munkahelyhez Facebook által szabályozható.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett munkahelyi Facebook (egyszeri bejelentkezés) által az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a munkahely által Facebook, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* A Facebook egyszeri bejelentkezés munkahelyi előfizetés engedélyezve van

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook két termék, munkahelyi Standard (ingyenes) és a munkaterület prémium szintű (fizetős) rendelkezik. Minden olyan munkahelyi Premium-bérlő nincs egyéb következmények költség vagy a szükséges licencek SCIM és egyszeri bejelentkezés integrációs is konfigurálhatja. Egyszeri Bejelentkezéssel és SCIM a munkahelyi normál esetben nem érhetők el.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a munkahely által Facebook **SP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a munkahely által Facebook **just-in-time-kiépítés**
* Támogatja a munkahely által Facebook  **[felhasználók automatikus átadása](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>A Facebook munkahelyi hozzáadása a katalógusból

Konfigurálása az Azure AD integrálása a munkahely által Facebook, hozzá kell munkahelyi Facebook által a galériából a felügyelt SaaS-alkalmazások listájára.

**A Facebook munkahelyi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **munkahelyi Facebook által**, jelölje be **munkahelyi Facebook által** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Munkahelyi a Facebook, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a munkahelyi szerint nevű tesztfelhasználó alapján Facebook **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a munkahelyen, a Facebook között hivatkozás kapcsolat kell hozható létre.

Az Azure AD egyszeri bejelentkezés a munkahelyi által Facebook tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Munkahelyi által Facebook egyszeri bejelentkezés konfigurálása](#configure-workplace-by-facebook-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre munkahelyi Facebook teszt felhasználó](#create-workplace-by-facebook-test-user)**  – egy megfelelője a Britta Simon rendelkeznie a munkaterületen, a Facebook, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Facebook a munkahelyhez, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **munkahelyi Facebook által** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Facebook-tartomány és URL-címeket egyetlen bejelentkezési adatait a munkahelyhez](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instancename>.facebook.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. A munkahelyi közösségi tekintse meg a hitelesítési lapon a munkahelyi céges irányítópult a megfelelő értékekre.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **létrehozott munkahelyi Facebook** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Konfigurálja a munkahely által Facebook Single Sign-On

1. Egy másik böngészőablakban, jelentkezzen be rendszergazdaként Facebook vállalati hely által a munkahelyen.
  
    > [!NOTE]
    > Az SAML-hitelesítési folyamat részeként a munkahelyi előfordulhat, hogy kihasználhassák a lekérdezési karakterláncok legfeljebb 2,5 kilobájt méretű annak érdekében, hogy az Azure AD át a paramétereket.

2. Az a **felügyeleti Panel**, nyissa meg a **biztonsági** fülre.

    ![Felügyeleti Panel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. A **hitelesítési** lapon jelölje be **egyszeri bejelentkezést (SSO)** , és hajtsa végre az alábbi lépéseket:

    ![Hitelesítés lap](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. A **SAML URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    b. A **SAML kibocsátó URI szövegmező**, illessze be az értéket a **Azure Ad-azonosító**, az Azure Portalról másolt.

    c. A **SAML kijelentkezési átirányítási** (nem kötelező), illessze be az értéket, **kijelentkezési URL-címe**, amely az Azure Portalról másolt.

    d. Nyissa meg a **base-64 kódolású tanúsítvány** a Jegyzettömbben az Azure-portálról letöltött, másolja a tartalmát a vágólapra, és illessze be azt a **SAML-tanúsítvány** szövegmezőbe.

    e. Másolás a **célközönség URL-cím** a példány, és illessze be a **azonosító (entityid)** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    f. Másolás a **címzett URL-cím** a példány, és illessze be a **bejelentkezési URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    g. A szakasz alján, és kattintson a **teszt SSO** gombra. Az eredmények egy felugró ablakban jelenik meg az Azure AD bejelentkezési oldal jelenik meg. Adja meg a hitelesítő adatait a hitelesítést a szokásos módon.

    **Hibaelhárítás:** Győződjön meg arról, az Azure AD-ből vissza visszaadott e-mail-cím megegyezik a munkahelyi fiók van bejelentkezve a következővel.

    h. Ha a vizsgálat sikeresen befejeződött, görgessen a lap aljára, és kattintson a **mentése** gombra.

    i. Munkahelyi használó összes felhasználó most már megjelenik az Azure AD bejelentkezési oldal a hitelesítéshez.

4. **SAML a kijelentkezési átirányítási (nem kötelező)** -

    Ha szeretné, igény szerint állítsa be a SAML kijelentkezési URL-címe, amely használható az Azure AD kijelentkezési lapon mutasson. Ha ezt a beállítást engedélyezve és konfigurálva van, a felhasználó már nem irányítja a rendszer a munkahelyi kijelentkezési lapra. Ehelyett a felhasználót a rendszer átirányítja az URL-címet, a SAML kijelentkezési átirányítási beállítás hozzáadva.

### <a name="configuring-reauthentication-frequency"></a>Az újrahitelesítés gyakoriság beállítása

Konfigurálhat egy SAML-ellenőrzés kérése minden nap, három nap, heti, Kétheti, havi munkahelyi vagy soha nem.

> [!NOTE]
> A SAML-ellenőrzés a mobilalkalmazások minimális értéke egy hét van beállítva.

Kényszerítheti egy SAML kérését az összes felhasználónak a gomb használatával is: Most már az összes felhasználó számára szükséges SAML-hitelesítés.

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

Ebben a szakaszban Britta Simon ehhez biztosítson hozzáférést a munkahelyi Facebook által használandó Azure egyszeri bejelentkezés engedélyezése.

1. Az Azure Portalon válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **munkahelyi által Facebook**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **munkahelyi által Facebook**.

    ![Az alkalmazások listáját a Facebook-kapcsolat által a munkahelyen](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-workplace-by-facebook-test-user"></a>Munkahelyi Facebook teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozta a munkaterületen Facebook. Munkahelyi Facebook szerint támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van.

Nem tartoznak, ez a szakasz a műveletek. Ha a felhasználó nem létezik a munkaterületen, a Facebook, egy új szolgáltatástelepítés Ha próbál meg hozzáférni a munkahely Facebook.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót, lépjen kapcsolatba kell [munkahelyi Facebook ügyfél-támogatási csoport szerint](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintás után a munkahely által Facebook csempe a hozzáférési panelen, meg kell automatikusan megtörténik a a munkahelyhez, amelynek beállítása egyszeri bejelentkezés Facebook szerint. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználók átadásának konfigurálása](workplacebyfacebook-provisioning-tutorial.md)
