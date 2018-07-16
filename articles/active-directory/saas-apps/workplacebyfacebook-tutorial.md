---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Facebook, a munkahelyi az |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a munkahely által Facebook között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: fe876db13cc5fb97f827a80a328003a7d2585adc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041985"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Oktatóanyag: Az Azure Active Directory-integráció a Facebook, a munkahelyi az

Ebben az oktatóanyagban elsajátíthatja a Facebook munkahelyi integrálása az Azure Active Directory (Azure AD).

Munkahelyi által Facebook integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy az Azure AD, aki hozzáféréssel rendelkezik a munkahelyhez Facebook által
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett munkahelyi Facebook (egyszeri bejelentkezés) által az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a munkahely által Facebook, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Facebook egyszeri bejelentkezés munkahelyi előfizetés engedélyezve van

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook két termék, munkahelyi Standard (ingyenes) és a munkaterület prémium szintű (fizetős) rendelkezik. Minden olyan munkahelyi Premium-bérlő nincs egyéb következmények költség vagy a szükséges licencek SCIM és egyszeri bejelentkezés integrációs is konfigurálhatja. Egyszeri Bejelentkezéssel és SCIM a munkahelyi normál esetben nem érhetők el.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Facebook munkahelyi hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>A Facebook munkahelyi hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a munkahely által Facebook, hozzá kell munkahelyi Facebook által a galériából a felügyelt SaaS-alkalmazások listájára.

**A Facebook munkahelyi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **munkahelyi által Facebook**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. Az eredmények panelen válassza ki a **munkahelyi által Facebook**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a munkahelyi Facebook, a teszt "Britta Simon." nevű felhasználó által

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó által Facebook munkahelyi mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a munkahelyen, a Facebook hivatkozás kapcsolatát kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a munkaterületen, a Facebook.

Az Azure AD egyszeri bejelentkezés a munkahelyi által Facebook tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az újrahitelesítés gyakoriság konfigurálása](#configuring-reauthentication-frequency)**  – munkahelyi kérése egy SAML-ellenőrzés konfigurálásához.
3. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Egy Facebook-teszt felhasználó munkahelyi létrehozása](#creating-a-workplace-by-facebook-test-user)**  – egy megfelelője a Britta Simon rendelkeznie a munkaterületen, a Facebook, amely kapcsolódik az Azure AD felhasználói ábrázolása.
5. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a munkahelyi Facebook-alkalmazás által egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Facebook a munkahelyhez, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **munkahelyi által Facebook** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Az a **Facebook-tartomány és URL-címek munkahelyi** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<instancename>.facebook.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. A munkahelyi közösségi tekintse meg a hitelesítési lapon a munkahelyi céges irányítópult a megfelelő értékekre. 

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

6. Az a **Facebook-konfigurációja munkahelyi** területén kattintson **munkahelyi konfigurálása a Facebook** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/workplacebyfacebook-tutorial/config.png) 

7. Egy másik böngészőablakban, jelentkezzen be rendszergazdaként Facebook vállalati hely által a munkahelyen.
  
   > [!NOTE] 
   > Az SAML-hitelesítési folyamat részeként a munkahelyi előfordulhat, hogy kihasználhassák a lekérdezési karakterláncok legfeljebb 2,5 kilobájt méretű annak érdekében, hogy az Azure AD át a paramétereket.

8. Az a **céges irányítópult**, nyissa meg a **hitelesítési** lapon.

9. A **SAML-hitelesítés**válassza **egyszeri bejelentkezés csak** a legördülő listából.

10. Adjon meg a másolt értékek **munkahelyi Facebook-konfigurációja** az Azure Portalon a megfelelő mezőkbe szakaszában:

    *   A **SAML URL-cím** szövegmező, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
    *   A **SAML kiállítójának URL-címe szövegmezőbe**, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt.
    *   A **SAML kijelentkezési átirányítási** (nem kötelező), illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.
    *   Nyissa meg a **base-64 kódolású tanúsítvány** a Jegyzettömbben az Azure-portálról letöltött, másolja a tartalmát a vágólapra, és illessze be azt a **SAML-tanúsítvány** szövegmezőbe.

11. Előfordulhat, hogy meg kell adnia a célközönség URL-címe, címzett URL-CÍMÉT, és az ACS (helyességi feltétel fogyasztói szolgáltatás) URL-címet felsorolva a **SAML-konfigurációja** szakaszban.

12. A szakasz alján, és kattintson a **teszt SSO** gombra. Az eredmények egy felugró ablakban jelenik meg az Azure AD bejelentkezési oldal jelenik meg. Adja meg a hitelesítő adatait a hitelesítést a szokásos módon. 

    **Hibaelhárítás:** az e-mail-cím már ad vissza az Azure ad-ből pedig ugyanaz, mint a munkahelyi fiók van bejelentkezve a következővel biztosítása.

13. Ha a vizsgálat sikeresen befejeződött, görgessen a lap aljára, és kattintson a **mentése** gombra.

14. Munkahelyi használó összes felhasználó most már megjelenik az Azure AD bejelentkezési oldal a hitelesítéshez.

15. **SAML a kijelentkezési átirányítási (nem kötelező)** - 

    Ha szeretné, igény szerint állítsa be a SAML kijelentkezési URL-címe, amely használható az Azure AD kijelentkezési lapon mutasson. Ha ezt a beállítást engedélyezve és konfigurálva van, a felhasználó már nem irányítja a rendszer a munkahelyi kijelentkezési lapra. Ehelyett a felhasználót a rendszer átirányítja az URL-címet, a SAML kijelentkezési átirányítási beállítás hozzáadva.

### <a name="configuring-reauthentication-frequency"></a>Az újrahitelesítés gyakoriság beállítása

Konfigurálhat egy SAML-ellenőrzés kérése minden nap, három nap, heti, Kétheti, havi munkahelyi vagy soha nem.

> [!NOTE] 
>A SAML-ellenőrzés a mobilalkalmazások minimális értéke egy hét van beállítva.

A SAML kérését az összes felhasználónak a gomb használatával is kényszeríthető: megkövetelése SAML-hitelesítés az összes felhasználó most.


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Egy Facebook-teszt felhasználó munkahelyi létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozta a munkaterületen Facebook. Munkahelyi Facebook szerint támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van.

Nem tartoznak, ez a szakasz a műveletek. Ha a felhasználó nem létezik a munkaterületen, a Facebook, egy új szolgáltatástelepítés Ha próbál meg hozzáférni a munkahely Facebook.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót, lépjen kapcsolatba kell [munkahelyi Facebook ügyfél-támogatási csoport szerint](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon ehhez biztosítson hozzáférést a munkahelyi Facebook által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelni a munkahelyhez, a Facebook, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **munkahelyi által Facebook**.

    ![Egyszeri bejelentkezés konfigurálása](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
