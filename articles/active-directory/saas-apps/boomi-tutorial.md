---
title: 'Oktatóanyag: Azure Active Directory-integráció az Boomi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Boomi között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: e0128d4422c462d4424583306af0b30174178bac
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049252"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Oktatóanyag: Azure Active Directory-integráció az Boomi

Ebben az oktatóanyagban elsajátíthatja, hogyan Boomi integrálása az Azure Active Directory (Azure AD).

Boomi integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Boomi Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Boomi (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Boomi az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Boomi egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Boomi hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-boomi-from-the-gallery"></a>Boomi hozzáadása a katalógusból
Az Azure AD integrálása a Boomi konfigurálásához hozzá kell Boomi a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Boomi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Boomi**válassza **Boomi** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Boomi](./media/boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Boomi a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Boomi mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Boomi hivatkozás kapcsolata kell létrehozni.

Boomi, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Boomi tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Boomi tesztfelhasználót](#create-a-boomi-test-user)**  – egy megfelelője a Britta Simon Boomi, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Boomi alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Boomi, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Boomi** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/boomi-tutorial/tutorial_boomi_samlbase.png)

3. Az a **Boomi tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Boomi tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/boomi-tutorial/tutorial_boomi_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://platform.boomi.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-cím. Kapcsolattartó [Boomi támogatási csapatának](https://boomi.com/company/contact/) a gépkulcsengedélyek értékének.
 
4. Boomi alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/boomi-tutorial/tutorial_attribute.png)

5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen, az alábbi táblázatban szereplő minden egyes sorára hajtsa végre az alábbi lépéseket:

    | Attribútum neve | Attribútum értéke |
    | -------------- | --------------- |
    | FEDERATION_ID | user.mail |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/boomi-tutorial/tutorial_officespace_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/boomi-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/boomi-tutorial/tutorial_boomi_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/boomi-tutorial/tutorial_general_400.png)

8. Az a **Boomi konfigurációs** területén kattintson **konfigurálása Boomi** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Boomi konfiguráció](./media/boomi-tutorial/tutorial_boomi_configure.png) 

9. Egy másik böngészőablakban jelentkezzen be a Boomi vállalati hely rendszergazdaként. 

10. Navigáljon a **cégnév** , majd **beállítása**.

11. Kattintson a **egyszeri bejelentkezési beállítások** lapra, és hajtsa végre az alábbi lépéseket.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Ellenőrizze **engedélyezése SAML egyszeri bejelentkezés** jelölőnégyzetet.

    b. Kattintson a **importálás** feltöltése az Azure AD-ből a letöltött tanúsítvány **szolgáltató Identitástanúsítványt**.
    
    c. Az a **Identity Provider bejelentkezési URL-cím** szövegmezőbe put értékét **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure AD-alkalmazás konfigurációs ablakából.

    d. Mint **összevonási azonosító helye**válassza **csomagazonosítója összevonási FEDERATION_ID attribútumelem** választógombot. 

    e. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/boomi-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/boomi-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/boomi-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/boomi-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-boomi-test-user"></a>Boomi tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Boomi jelentkezzen be, akkor ki kell építeni Boomi be. Boomi, esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be rendszergazdaként a Boomi vállalati webhely.

2. Miután bejelentkezett, lépjen **felhasználókezelés** , majd **felhasználók**.

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_001.png "felhasználók")

3. Kattintson a **+** ikon és a **Hozzáadás/karbantartása felhasználói szerepkörök** párbeszédpanel nyílik meg.

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_002.png "felhasználók")

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_003.png "felhasználók")

    a. Az a **felhasználó e-mail címe** szövegmezőbe írja be az e-mailt, felhasználó, például BrittaSimon@contoso.com.
    
    b. Az a **Utónév** szövegmezőbe írja be az első felhasználóneve Britta például.

    c. Az a **Vezetéknév** szövegmezőbe írja be a Simon például a felhasználó vezetékneve.
    
    d. Adja meg a felhasználó **összevonási azonosító**. Minden felhasználónak rendelkeznie kell egy összevonási azonosítója, amely egyedileg azonosítja a felhasználó a fiókon belül.
    
    e. Rendelje hozzá a **általános jogú felhasználói** szerepkört a felhasználóhoz. Ne rendeljen a rendszergazda szerepkör, mert az, hogy adna neki, normál levegőben hozzáférését, valamint az egyszeri bejelentkezéses hozzáférést.
    
    f. Kattintson az **OK** gombra.
    
    > [!NOTE]
    > A felhasználó nem kap meg az üdvözlő e-mailben értesítést tartalmazó egy jelszót, amely használható a AtomSphere fiók bejelentkezni, mert az identitásszolgáltató kezelhető a jelszavát. Előfordulhat, hogy bármely más Boomi felhasználói fiók létrehozása az eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Boomi által biztosított API-k.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Boomi Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Boomi, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Boomi**.

    ![Az alkalmazások listáját a Boomi hivatkozásra](./media/boomi-tutorial/tutorial_boomi_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Boomi csempére kattint, meg kell lekérése automatikusan bejelentkezett az Boomi alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boomi-tutorial/tutorial_general_01.png
[2]: ./media/boomi-tutorial/tutorial_general_02.png
[3]: ./media/boomi-tutorial/tutorial_general_03.png
[4]: ./media/boomi-tutorial/tutorial_general_04.png

[100]: ./media/boomi-tutorial/tutorial_general_100.png

[200]: ./media/boomi-tutorial/tutorial_general_200.png
[201]: ./media/boomi-tutorial/tutorial_general_201.png
[202]: ./media/boomi-tutorial/tutorial_general_202.png
[203]: ./media/boomi-tutorial/tutorial_general_203.png

