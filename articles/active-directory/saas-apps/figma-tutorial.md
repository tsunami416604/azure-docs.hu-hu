---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Figma |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Figma között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8569cae1-87dd-4c40-9bbb-527ac80d6a96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3885f579e4543f25c409010c0aae970c26d2b9c4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183974"
---
# <a name="tutorial-azure-active-directory-integration-with-figma"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Figma

Ebben az oktatóanyagban elsajátíthatja, hogyan Figma integrálása az Azure Active Directory (Azure AD).

Figma integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Figma Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Figma (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Figma az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Figma [egyszeri bejelentkezés engedélyezve van az előfizetés](https://www.figma.com/pricing/)

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja. Új ügyfeleket és az aktív előfizetők Figma Professional csapat kapcsolatba léphet a Figma [frissítik előfizetésüket](https://www.figma.com/pricing/) Figma szervezet szintre.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Figma hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-figma-from-the-gallery"></a>Figma hozzáadása a katalógusból

Az Azure AD integrálása a Figma konfigurálásához hozzá kell Figma a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Figma hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Figma**. Válassza ki **Figma** az eredmények panelen, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Figma](./media/figma-tutorial/tutorial_figma_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Figma a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD Figma társítani kell.  Az Azure AD egyszeri bejelentkezés az Figma tesztelése és konfigurálása, hajtsa végre az alábbi lépéseket:

1. [**Forduljon az ügyfélszolgálathoz Figma** ](mailto:support@figma.com?subject=SAML+Config) kezdeményezéséhez SAML-konfigurációt a szervezet számára, és a egy ORG_SAML_CONFIG_ID beolvasása.
2. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
3. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Hozzon létre egy Figma tesztfelhasználót](#creating-a-figma-test-user)**  – egy megfelelője a Britta Simon Figma, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
5. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Figma alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Figma, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Figma** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/figma-tutorial/tutorial_general_301.png)

3. Ha váltani szeretne **SAML** üzemmódról bármely másik, kattintson a **egyszeri bejelentkezési mód megváltoztatása** a képernyő fölött.

    ![Egyszeri bejelentkezés konfigurálása](./media/figma-tutorial/tutorial_general_300.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/figma-tutorial/tutorial_general_302.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, a következő lépésekkel, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Figma tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/figma-tutorial/tutorial_figma_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/consume`

6. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Figma tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/figma-tutorial/tutorial_figma_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/start`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Figma támogatási csapatának](mailto:support@figma.com?subject=SAML+Config) beolvasni ezeket az értékeket.

7. Figma alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![Attribútum szakasz](./media/figma-tutorial/edit_attribute.png)

8. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------- |
    | `externalId` | `user.mailnickname` |
    | `displayName` | `user.displayname` |
    | `title` | `user.jobtitle` |
    | `emailaddress` | `user.mail` |
    | `familyName` | `user.surname` |
    | `givenName` | `givenName` |
    | `userName` | `user.userprincipalname` |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![Új attribútum](./media/figma-tutorial/new_save_attribute.png)

    ![Attribútum hozzáadása](./media/figma-tutorial/new_attribute_details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

9. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](./media/figma-tutorial/tutorial_figma_certificate.png)

10. Egyszeri bejelentkezés konfigurálása Figma oldalán, töltse ki ezt a kérdőívet: [ https://goo.gl/forms/XkRB1z5ed4eVUzXn2 ](https://goo.gl/forms/XkRB1z5ed4eVUzXn2). Akkor fogadja el a **alkalmazás összevonási metaadatainak URL-címe** a 9. lépés.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/figma-tutorial/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/figma-tutorial/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-figma-test-user"></a>Figma tesztfelhasználó létrehozása

Ez a szakasz célja Figma Britta Simon nevű felhasználó létrehozásához. Figma támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Figma elérésére, ha még nem létezik tett kísérlet során.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával történő HubSpot SAML Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **HubSpot SAML**.

    ![Egyszeri bejelentkezés konfigurálása](./media/figma-tutorial/tutorial_figma_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Figma csempére kattint, meg kell lekérése automatikusan bejelentkezett az Figma alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/figma-tutorial/tutorial_general_01.png
[2]: ./media/figma-tutorial/tutorial_general_02.png
[3]: ./media/figma-tutorial/tutorial_general_03.png
[4]: ./media/figma-tutorial/tutorial_general_04.png

[100]: ./media/figma-tutorial/tutorial_general_100.png

[200]: ./media/figma-tutorial/tutorial_general_200.png
[201]: ./media/figma-tutorial/tutorial_general_201.png
[202]: ./media/figma-tutorial/tutorial_general_202.png
[203]: ./media/figma-tutorial/tutorial_general_203.png
