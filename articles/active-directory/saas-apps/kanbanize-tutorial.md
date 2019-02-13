---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Kanbanize |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Kanbanize között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199680"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Kanbanize

Ebben az oktatóanyagban elsajátíthatja, hogyan Kanbanize integrálása az Azure Active Directory (Azure AD).

Kanbanize integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Kanbanize Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Kanbanize (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Kanbanize az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Kanbanize egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Kanbanize hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-kanbanize-from-the-gallery"></a>Kanbanize hozzáadása a katalógusból
Az Azure AD integrálása a Kanbanize konfigurálásához hozzá kell Kanbanize a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Kanbanize hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Kanbanize**válassza **Kanbanize** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Kanbanize a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Kanbanize mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Kanbanize hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Kanbanize tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Kanbanize tesztfelhasználót](#create-a-kanbanize-test-user)**  – egy megfelelője a Britta Simon Kanbanize, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Kanbanize alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Kanbanize, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Kanbanize** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Az a **Kanbanize tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Kanbanize tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.kanbanize.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Ellenőrizze **speciális URL-beállítások megjelenítése**.

    d.  Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `/ctrl_login/saml_login`

    e. Ha az alkalmazás a konfigurálni kívánt **SP** módban kezdeményezett **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Kanbanize ügyfél-támogatási csapatának](mailto:support@ms.kanbanize.com) beolvasni ezeket az értékeket. 

5. Kanbanize alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **felhasználóazonosító** van **user.userprincipalname** , de Kanbanize vár ezt a képezhető le a felhasználó e-mail-címmel. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a szervezet konfiguráció alapján a megfelelő attribútum-érték
    
    ![Egyszeri bejelentkezés konfigurálása](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Az a **Kanbanize konfigurációs** területén kattintson **konfigurálása Kanbanize** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Kanbanize konfiguráció](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. Egy másik böngészőablakban, jelentkezzen be egy biztonsági-rendszergazdaként Kanbanize. 

10. Az oldal jobb oldalán, kattintson a go **beállítások** embléma.

    ![Kanbanize beállításai](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. A bal oldali menü Felügyeleti panel oldalon kattintson **Integrációk** , majd engedélyezze a **egyszeri bejelentkezés**. 

    ![Kanbanize Integrációk](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Integrációk szakasz alatt kattintson a **KONFIGURÁLÁSA** megnyitásához **egyszeri bejelentkezéses integrációs** lapot.

    ![Kanbanize config](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Az a **egyszeri bejelentkezéses integrációs** lap **konfigurációk**, hajtsa végre az alábbi lépéseket:

    ![Kanbanize Integrációk](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. Az a **identitásszolgáltató entitásazonosító** szövegmező, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt.

    b. Az a **identitásszolgáltató bejelentkezési végpont** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    c. Az a **identitásszolgáltató kijelentkezési végpont** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    d. A **attribútumnév e-mailek** szövegmezőbe írja be ezt az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. A **attribútum nevét, az Utónév** szövegmezőbe írja be ezt az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. A **Vezetéknév attribútumnévben** szövegmezőbe írja be ezt az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Ezeket az értékeket a felhasználói attribútumok szakaszban az Azure Portalon a megfelelő attribútum névtere és neve értékek kombinálásával kérheti le.

    g. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, a tartalom (a kezdő és záró jelölők) nélkül másolja és illessze be azt a **identitásszolgáltató X.509-tanúsítvány** mezőbe.

    h. Ellenőrizze **engedélyezése jelentkezzen be egyszeri Bejelentkezést és a Kanbanize**.
    
    i. Kattintson a **beállítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/kanbanize-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/kanbanize-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-kanbanize-test-user"></a>Kanbanize tesztfelhasználó létrehozása

Ez a szakasz célja Kanbanize Britta Simon nevű felhasználó létrehozásához. Kanbanize támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Kanbanize elérésére, ha még nem létezik tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Kanbanize ügyfél-támogatási csapatának](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Kanbanize Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Kanbanize, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Kanbanize**.

    ![Az alkalmazások listáját a Kanbanize hivatkozásra](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Kanbanize csempére kattint, meg kell lekérése automatikusan bejelentkezett az Kanbanize alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

