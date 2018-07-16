---
title: 'Oktatóanyag: Azure Active Directory-integráció az Elium |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Elium között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: 2957fffecbf448fa456d80200aba9752569b5f69
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042724"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Oktatóanyag: Azure Active Directory-integráció az Elium

Ebben az oktatóanyagban elsajátíthatja, hogyan Elium integrálása az Azure Active Directory (Azure AD).

Elium integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Elium Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Elium (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Elium az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Elium egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Elium hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-elium-from-the-gallery"></a>Elium hozzáadása a katalógusból
Az Azure AD integrálása a Elium konfigurálásához hozzá kell Elium a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Elium hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Elium**válassza **Elium** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Elium](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Elium a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Elium mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Elium hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Elium tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Elium tesztfelhasználót](#create-an-elium-test-user)**  – egy megfelelője a Britta Simon Elium, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Elium alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Elium, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Elium** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/elium-tutorial/tutorial_elium_samlbase.png)

3. Az a **Elium tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Elium tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/elium-tutorial/tutorial_elium_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<platform-domain>.elium.com/login/saml2/acs`

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Elium tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/elium-tutorial/tutorial_elium_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezeket az értéteket fog kapni a **SP metaadatait tartalmazó fájl** címen letölthető `https://<platform-domain>.elium.com/login/saml2/metadata`, amely kifejtett az oktatóanyag későbbi részében.

5. A Elium alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/tutorial_attribute.png)

6. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző képen látható módon, és hajtsa végre az alábbi lépéseket:
           
    | Attribútum neve | Attribútum értéke |   
    | ---------------| ----------------|
    | e-mailben   |user.mail |
    | first_name| User.givenName |
    | last_name| User.surname|
    | job_title| user.jobtitle|
    | Vállalati| User.CompanyName|
    
    > [!NOTE]
    > Ezek a alapértelmezett jogcímeket. **Csak e-mail követelés szükség**. Igény szerinti kiépítés is csak az e-mailt a jogcím megadása kötelező. Egyéb egyéni jogcímek egy ügyfél platformról egy másik ügyfél platformra eltérőek lehetnek.

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/tutorial_attribute_04.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Névtér hagyja üresen.
    
    e. Kattintson az **OK** gombra. 

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/elium-tutorial/tutorial_elium_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/elium-tutorial/tutorial_general_400.png)
    
7. Egy másik böngészőablakban jelentkezzen be a Elium vállalati hely rendszergazdaként.

8. Kattintson a **felhasználói profil** jobb felső sarokban, majd válassza ki a **felügyeleti**.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user1.png)

9. Válassza ki **biztonsági** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user2.png)

10. Görgessen le a **egyszeri bejelentkezés (SSO)** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user3.png)

    a. Másolja az értéket a **ellenőrizze, hogy egy SAML2 hitelesítés működését a fiók** , és illessze be a a **bejelentkezési URL-** a szövegmezőbe a **Elium tartomány és URL-címek** szakaszban az Azure-ban portál.

    > [!NOTE]
    > Egyszeri bejelentkezés konfigurálása, után mindig érhető el az alapértelmezett távoli bejelentkezési oldalt a következő URL-címen: `https://<platform_domain>/login/regular/login` 

    b. Válassza ki **engedélyezése egy SAML2 összevonási** jelölőnégyzetet.

    c. Válassza ki **igény szerinti kiépítés** jelölőnégyzetet.

    d. Nyissa meg a **SP metaadatok** kattintva a **letöltése** gombra.

    e. Keresse meg a **entityID** a a **SP metaadatok** fájlt, másolja a **entityID** értékét, és illessze be a **azonosító** a aszövegmező **Elium tartomány és URL-címek** szakaszban az Azure Portalon. 

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user4.png)

    f. Keresse meg a **AssertionConsumerService** a a **SP metaadatok** fájlt, másolja a **hely** értékét, és illessze be a **válasz URL-cím** a szövegmezőben, hogy a **Elium tartomány és URL-címek** szakaszban az Azure Portalon.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user5.png)

    g. Nyissa meg a letöltött metaadatait tartalmazó fájl az Azure Portalról a Jegyzettömbbe, másolja a tartalmat, és illessze be azt a **identitásszolgáltató metaadatok** szövegmezőbe.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/elium-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/elium-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/elium-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/elium-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-elium-test-user"></a>Hozzon létre egy Elium tesztfelhasználó számára

Ez a szakasz célja Elium Britta Simon nevű felhasználó létrehozásához. Elium támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Elium elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Elium támogatási csapatának](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Elium Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Elium, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Elium**.

    ![Az alkalmazások listáját a Elium hivatkozásra](./media/elium-tutorial/tutorial_elium_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Elium csempére kattint, meg kell lekérése automatikusan bejelentkezett az Elium alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

