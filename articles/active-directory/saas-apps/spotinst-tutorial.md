---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Spotinst |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Spotinst között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 679284783ac93662c1c96e813f9f028fffb434f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173657"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Spotinst

Ebben az oktatóanyagban elsajátíthatja, hogyan Spotinst integrálása az Azure Active Directory (Azure AD).

Spotinst integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Spotinst Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Spotinst (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Spotinst az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Spotinst egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Spotinst hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-spotinst-from-the-gallery"></a>Spotinst hozzáadása a katalógusból
Az Azure AD integrálása a Spotinst konfigurálásához hozzá kell Spotinst a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Spotinst hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Spotinst**válassza **Spotinst** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Spotinst](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Spotinst a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Spotinst mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Spotinst hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Spotinst tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Spotinst tesztfelhasználót](#create-a-spotinst-test-user)**  – egy megfelelője a Britta Simon Spotinst, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Spotinst alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Spotinst, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Spotinst** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Az a **Spotinst tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás Identitásszolgáltató által kezdeményezett módban konfigurálása:

    ![Spotinst tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Ellenőrizze **speciális URL-beállítások megjelenítése**.

    b. Az a **továbbítási állapot** szövegmezőbe írja be egy értéket: `<ID>`

    c. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a **bejelentkezési URL-cím** szövegmezőbe írja be az URL-cím: `https://console.spotinst.com`

    > [!NOTE]
    > A továbbítási állapot értéke nem valódi. Továbbítási állapot értéke frissíti a tényleges továbbítási állapot érték, amelynek az ismertetése az oktatóanyag későbbi részében.

4. Spotinst alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa látható a hozzá tartozó.

    ![Egyszeri bejelentkezés konfigurálása](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja a **Namespace** üres.

    e. Kattintson a **Ok**

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/spotinst-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban, jelentkezzen be egy biztonsági-rendszergazdaként Spotinst.

9. Kattintson a **felhasználói ikon** meg a képernyőn, majd kattintson a jobb felső **beállítások**.

    ![Spotinst beállításai](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Kattintson a **biztonsági** felső lapot, majd **Identitásszolgáltatók** , és hajtsa végre az alábbi lépéseket:

    ![Spotinst biztonsági](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Másolás a **továbbítási állapot** a példány értékét, és illessze be a **továbbítási állapot** szövegmezőjébe **Spotinst tartomány és URL-címek** szakaszban az Azure Portalon.

    b. Kattintson a **Tallózás** Azure Portalról letöltött metaadatainak xml-fájl feltöltése

    c. Kattintson a **SAVE** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/spotinst-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/spotinst-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/spotinst-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/spotinst-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-spotinst-test-user"></a>Spotinst tesztfelhasználó létrehozása

Ez a szakasz célja Spotinst Britta Simon nevű felhasználó létrehozásához.

1. Ha konfigurálta az alkalmazás a **SP** intiated mód, hajtsa végre az alábbi lépéseket:

   a. Egy másik böngészőablakban, jelentkezzen be egy biztonsági-rendszergazdaként Spotinst.

   b. Kattintson a **felhasználói ikon** meg a képernyőn, majd kattintson a jobb felső **beállítások**.

    ![Spotinst beállításai](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kattintson a **felhasználók** válassza **felhasználó hozzáadása**.

    ![Spotinst beállításai](./media/spotinst-tutorial/adduser1.png)

    d. A felhasználó hozzáadása szakaszról hajtsa végre a következő lépéseket:

    ![Spotinst beállításai](./media/spotinst-tutorial/adduser2.png)

    * Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be például a felhasználó teljes nevét **BrittaSimon**.

    * Az a **E-mail** szövegmezőbe írja be például a felhasználó e-mail-címe **brittasimon@contoso.com**.

    * Válassza ki a szervezet konkrét részletei a **szervezeti szerepkör, a fiók szerepkör és a fiókok**.

2. Ha konfigurálta az alkalmazás a **Identitásszolgáltató** intiated mód, itt nem, nincs teendő ebben a szakaszban az Ön számára. Spotinst támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Új felhasználó jön létre az Spotinst elérésére, ha még nem létezik tett kísérlet során.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Spotinst Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Spotinst, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Spotinst**.

    ![Az alkalmazások listáját a Spotinst hivatkozásra](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Spotinst csempére kattint, meg kell lekérése automatikusan bejelentkezett az Spotinst alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

