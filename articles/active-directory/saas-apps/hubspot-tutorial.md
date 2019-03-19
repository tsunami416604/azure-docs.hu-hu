---
title: 'Oktatóanyag: HubSpot az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és HubSpot között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bbb307654d4aaf753a4a3284875dee4f5707f2a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901726"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Oktatóanyag: HubSpot az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan történő HubSpot integrálása az Azure Active Directory (Azure AD).

HubSpot integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy az Azure ad-ben történő HubSpot hozzáféréssel rendelkező
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett történő HubSpot (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Történő HubSpot az Azure AD-integráció konfigurálása, szüksége van a következő elemek:

- Azure AD-előfizetés
- Egy HubSpot egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. HubSpot hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-hubspot-from-the-gallery"></a>HubSpot hozzáadása a katalógusból

Történő HubSpot integrálása az Azure AD beállítása, hozzá kell HubSpot a galériából a felügyelt SaaS-alkalmazások listájára.

**Történő HubSpot hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **HubSpot**. Válassza ki **HubSpot** az eredmények panelen, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó HubSpot az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó HubSpot mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó HubSpot hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az HubSpot tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **HubSpot tesztfelhasználó létrehozása** – egy megfelelője a Britta Simon HubSpot, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és HubSpot alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés HubSpot, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **HubSpot** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/tutorial_general_301.png)

3. Ha váltani szeretne **SAML** üzemmódról bármely másik, kattintson a **egyszeri bejelentkezési mód megváltoztatása** a képernyő fölött.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/tutorial_general_300.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/tutorial_general_302.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban a következő lépésekkel, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![HubSpot tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím a következő minta használatával: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be az URL-cím a következő minta használatával: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját és a válasz URL-cím, az oktatóanyag későbbi részében ismertetett ezeket az értékeket. Kapcsolattartó [HubSpot ügyfél-támogatási csapatának](https://help.hubspot.com/) beolvasni ezeket az értékeket.

    c. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![HubSpot tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://app.hubspot.com/login`

6. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Az a **HubSpot beállítása** területén kattintson a Másolás gombra, hogy a **bejelentkezési URL-cím** és **az Azure AD-azonosító** értékeket.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Nyissa meg egy új lapot a böngészőben, és jelentkezzen be történő HubSpot rendszergazdai fiókjával.

9. Kattintson a **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config1.png)

10. Kattintson a **alapértelmezett fiók**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config2.png)

11. Görgessen le a **biztonsági** szakaszt, és kattintson a **beállítása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config3.png)

12. Az a **egyszeri bejelentkezés beállítása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config4.png)

    a. Kattintson a **másolási** gombra, hogy a **célközönség URl(Service Provider Entity ID)** értékét, és illessze be azt a **azonosító** szövegmezőjébe a **alapszintű SAML Konfigurációs** szakaszban az Azure Portalon.

    b. Kattintson a **másolási** gombra, hogy a **jelentkezzen be az URL-címe, ACS, címzett vagy átirányítási** értékét, és illessze be azt a **válasz URL-cím** szövegmezőjébe a **alapszintű SAML Konfigurációs** szakaszban az Azure Portalon.

    c. Az a **identitás azonosítója vagy a kiállító URL-címe** szövegmezőjébe illessze be a **az Azure AD-azonosító** az Azure Portalról másolt érték.

    d. Az a **identitás szolgáltató egyszeri bejelentkezési URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** az Azure Portalról másolt érték.

    e. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben. A tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** mezőbe.

    f. Kattintson az **Ellenőrzés** lehetőségre.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hubspot-tutorial/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hubspot-tutorial/create_aaduser_02.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-hubspot-test-user"></a>HubSpot tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók történő HubSpot jelentkezzen be, akkor ki kell építeni történő HubSpot.
HubSpot, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **HubSpot** rendszergazdaként a vállalati webhely.

2. Kattintson a **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config1.png)

3. Kattintson a **felhasználók és csoportok**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user1.png)

4. Kattintson a **felhasználó létrehozása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user2.png)

5. Adja meg például a felhasználó e-mail-címe **brittasimon\@contoso.com** a a **hozzáadása e-mail addess(es)** szövegmezőbe, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user3.png)

6. Az a **felhasználók létrehozása** szakaszban, haladjon végig a minden egyes fülre, és válassza ki a megfelelő beállításokat és a felhasználót, majd kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user4.png)

7. Kattintson a **küldése** küldése a felhasználónak a meghívót.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Felhasználó fogja aktiválni a meghívás elfogadása után.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával történő HubSpot Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **HubSpot**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a HubSpot csempére kattint, szerezheti be automatikusan HubSpot alkalmazás bejelentkezési oldalán.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
