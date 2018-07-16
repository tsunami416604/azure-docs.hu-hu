---
title: 'Oktatóanyag: Azure Active Directory-integráció az Andromeda |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Andromeda között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 047e1ea6a474d95c57ffc2bdff5ad8a5c45e0d36
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048565"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Oktatóanyag: Azure Active Directory-integráció az Andromeda

Ebben az oktatóanyagban elsajátíthatja, hogyan Andromeda integrálása az Azure Active Directory (Azure AD).

Andromeda integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Andromeda Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Andromeda (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Andromeda az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Andromeda egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Andromeda hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-andromeda-from-the-gallery"></a>Andromeda hozzáadása a katalógusból
Az Azure AD integrálása a Andromeda konfigurálásához hozzá kell Andromeda a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Andromeda hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Andromeda**válassza **Andromeda** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Andromeda a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Andromeda mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Andromeda hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Andromeda tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Andromeda tesztfelhasználót](#create-an-andromeda-test-user)**  – egy megfelelője a Britta Simon Andromeda, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Andromeda alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Andromeda, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Andromeda** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Az a **Andromeda tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Andromeda tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantURL>.ngcxpress.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Andromeda tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Az előző érték nem valódi értéket. A tényleges azonosítója, válasz URL-cím és a bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett frissíteni az értéket.

5. Andromeda alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés attb konfigurálása](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Törölje a névtér-definíciókat, ezek beállítása közben meg.
    
6. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | -------------- | -------------------- |    
    | szerepkör        | Adott alkalmazás-szerepkör |
    | type        | Alkalmazás típusa |
    | Vállalati       | Cégnév    |

    > [!NOTE]
    > Nem áll valós értékeket. Ezek az értékek csak bemutató célra, használja a szervezet szerepkörök.

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés Addattb konfigurálása](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.

7. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. Az a **Andromeda konfigurációs** területén kattintson **konfigurálása Andromeda** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Andromeda konfiguráció](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Bejelentkezés a Andromeda vállalati webhelyre rendszergazdaként.

11. Kattintson a menüsáv tetején **rendszergazdai** , és keresse meg **felügyeleti**.

    ![Andromeda rendszergazda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. A bal oldali eszköztáron alatt a **felületek** területén kattintson **SAML-konfigurációja**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Az a **SAML-konfigurációja** című oldalon, a következő lépésekkel:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Ellenőrizze **az SAML SSO engedélyezése**.

    b. Alatt **Andromeda információk** területén másolja a **SP identitás** értékét, és illessze be azt a **azonosító** szövegmezőben, hogy a **Andromeda tartomány és URL-címek** szakaszban.

    c. Másolás a **ügyfél URL-címe** értékét, és illessze be azt a **válasz URL-cím** szövegmezőben, hogy a **Andromeda tartomány és URL-címek** szakaszban.

    d. Másolás a **bejelentkezési URL-cím** értékét, és illessze be azt a **bejelentkezési URL-** szövegmezőben, hogy a **Andromeda tartomány és URL-címek** szakaszban.

    e. A **identitásszolgáltató SAML** területén írja be az Identitásszolgáltató nevét.

    f. Az a **egyszeri bejelentkezést a végpontot** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** , amely az Azure Portalról másolt.

    g. Nyissa meg a letöltött **Base64-kódolású tanúsítványt** Azure Portalról a Jegyzettömbben, illessze be azt a **X 509-tanúsítvány** szövegmezőbe.
    
    h. Képezze le a következő attribútumokkal az Azure AD Egyszeri bejelentkezési megkönnyítése érdekében a megfelelő értékkel. A **Felhasználóazonosító** attribútumra szükség a bejelentkezés. Üzembe helyezés, **E-mail**, **vállalati**, **UserType**, és **szerepkör** szükség. Ebben a szakaszban az Azure Portalon belül definiált meghatározzuk attribútumok leképezése (nevét és értékeit) amely összekapcsolását

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/andromedascm-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/andromedascm-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-andromeda-test-user"></a>Hozzon létre egy Andromeda tesztfelhasználó számára

Ez a szakasz célja Andromeda Britta Simon nevű felhasználó létrehozásához. Andromeda támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Andromeda elérésére, ha még nem létezik tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Andromeda ügyfél-támogatási csapatának](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Andromeda Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Andromeda, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Andromeda**.

    ![Az alkalmazások listáját a Andromeda hivatkozásra](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Andromeda csempére kattint, meg kell lekérése automatikusan bejelentkezett az Andromeda alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
