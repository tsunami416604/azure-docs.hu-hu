---
title: 'Oktatóanyag: Azure Active Directory-integráció az BlueJeans |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BlueJeans között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095228"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Oktatóanyag: Azure Active Directory-integráció az BlueJeans

Ebben az oktatóanyagban elsajátíthatja, hogyan BlueJeans integrálása az Azure Active Directory (Azure AD).

BlueJeans integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá BlueJeans Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett BlueJeans (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

BlueJeans az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy BlueJeans egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. BlueJeans hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans hozzáadása a katalógusból

Az Azure AD integrálása a BlueJeans konfigurálásához hozzá kell BlueJeans a katalógusból a felügyelt SaaS-alkalmazások listájára.

**BlueJeans hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **BlueJeans**válassza **BlueJeans** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában blueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés BlueJeans a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó BlueJeans mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó BlueJeans hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az BlueJeans tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[BlueJeans tesztfelhasználó létrehozása](#creating-a-bluejeans-test-user)**  - a-megfelelője a Britta Simon BlueJeans a felhasználó Azure ad-ben reprezentációja kapcsolódó rendelkezik.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és BlueJeans alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BlueJeans, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **BlueJeans** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_general_301.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![BlueJeans tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > A bejelentkezési értéke nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [BlueJeans ügyfél-támogatási csapatának](https://support.bluejeans.com/contact) a gépkulcsengedélyek értékének.

6. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Az a **BlueJeans beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Az Azure AD-azonosító

    c. Kijelentkezési URL

    ![BlueJeans konfiguráció](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. Egy másik böngészőablakban, jelentkezzen be a **BlueJeans** rendszergazdaként a vállalati webhely.

9. Lépjen a **rendszergazdai \> CSOPORTBEÁLLÍTÁSOK \> biztonsági**.

    ![Rendszergazdai](./media/bluejeans-tutorial/IC785868.png "rendszergazda")

10. Az a **biztonsági** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezési SAML](./media/bluejeans-tutorial/IC785869.png "SAML egyszeri bejelentkezés")

    a. Válassza ki **egyszeri bejelentkezési SAML**.

    b. Válassza ki **automatikus kiépítés engedélyezése**.

11. Helyezze az alábbi lépéseket követve:

    ![Tanúsítvány-elérési út](./media/bluejeans-tutorial/IC785870.png "tanúsítvány elérési útja")

    a. Kattintson a **fájl kiválasztása**, töltse fel az base-64 kódolású tanúsítványt, az Azure Portalról letöltött.

    b. Az a **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. Az a **URL-Címének jelszó módosítása** szövegmezőjébe illessze be az értéket a **jelszó URL-Címének módosítása** Azure Portalról másolt.

    d. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.

12. Helyezze az alábbi lépéseket követve:

    ![Módosítások mentése](./media/bluejeans-tutorial/IC785874.png "módosítások mentése")

    a. Az a **felhasználóazonosító** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Az a **E-mail** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kattintson a **MENTSE a MÓDOSÍTÁSOKAT**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-bluejeans-test-user"></a>BlueJeans tesztfelhasználó létrehozása

Ez a szakasz célja BlueJeans Britta Simon nevű felhasználó létrehozásához. BlueJeans támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](bluejeans-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **BlueJeans** rendszergazdaként a vállalati webhely.

2. Lépjen a **rendszergazdai \> felhasználók kezelése \> felhasználó hozzáadása**.

    ![Rendszergazdai](./media/bluejeans-tutorial/IC785877.png "rendszergazda")

    >[!IMPORTANT]
    >A **felhasználó hozzáadása** lap csak akkor érhető el, ha az a **biztonsági lapon**, **automatikus kiépítés engedélyezése** nincs bejelölve. 

3. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/IC785886.png "felhasználó hozzáadása")

    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    c. A **BlueJeans felhasználónevet válasszon** szöveget adja meg például a felhasználó felhasználóneve **Brittasimon**

    d. A **hozzon létre egy jelszót** szöveget adja meg a jelszót.

    e. A **vállalati** szöveget adja meg a vállalati.

    f. A **E-mail cím** szöveget adja meg az e-mailt, például a felhasználó **brittasimon@contoso.com**.

    g. A **hozzon létre egy BlueJeans értekezlet i.** szöveget adja meg az értekezlet azonosítóját.

    h. A **válasszon ki egy moderátor PIN-kód** szöveget adja meg a PIN-kódját.

    i. Kattintson a **FOLYTATÁS**.

    ![Addd felhasználói](./media/bluejeans-tutorial/IC785887.png "Addd felhasználó")

    J. Kattintson a **felhasználó hozzáadása**.

>[!NOTE]
>Eszközt is használhat bármilyen más BlueJeans felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése BlueJeans által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BlueJeans Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **BlueJeans**.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BlueJeans csempére kattint, meg kell lekérése automatikusan bejelentkezett az BlueJeans alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
