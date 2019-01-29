---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TextMagic |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TextMagic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.openlocfilehash: ed5107d581c880d130901bfb31d34afb9e986635
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190088"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TextMagic

Ebben az oktatóanyagban elsajátíthatja, hogyan TextMagic integrálása az Azure Active Directory (Azure AD).

TextMagic integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá TextMagic Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett TextMagic (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

TextMagic az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy TextMagic egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TextMagic hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-textmagic-from-the-gallery"></a>TextMagic hozzáadása a katalógusból

Az Azure AD integrálása a TextMagic konfigurálásához hozzá kell TextMagic a katalógusból a felügyelt SaaS-alkalmazások listájára.

**TextMagic hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **TextMagic**válassza **TextMagic** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában TextMagic](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés TextMagic a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó TextMagic mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TextMagic hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az TextMagic tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[TextMagic tesztfelhasználó létrehozása](#creating-a-textmagic-test-user)**  – egy megfelelője a Britta Simon TextMagic, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és TextMagic alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TextMagic, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TextMagic** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![TextMagic tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://my.textmagic.com/saml/metadata`

5. TextMagic alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név)  | Adatforrás-attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | Vállalati | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Keresztnév               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Vezetéknév            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefon               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. Kattintson a **Szerkesztés ikon** szerkesztése a **azonosító érték neve** a **user.userprinicipalname** való **user.mail**.

    ![TextMagic attribútum](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. Az a **neve**szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    d. Adja meg a **Namespace** értéket.

    e. Válassza ki a forrás, **attribútum**.

    f. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    g. Kattintson az **OK** gombra.

    h. Kattintson a **Save** (Mentés) gombra. 

7. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. Az a **TextMagic beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![TextMagic konfiguráció](common/configuresection.png)

9. Egy másik böngészőablakban jelentkezzen be a TextMagic vállalati hely rendszergazdaként.

10. Válassza ki **Fiókbeállítások** felhasználónév alapján.

    ![TextMagic konfiguráció](./media/textmagic-tutorial/config1.png)

11. Kattintson a lap a **egyszeri bejelentkezéses (SSO)** , és töltse ki a következő mezőket:  
    
    ![TextMagic konfiguráció](./media/textmagic-tutorial/config2.png)

    a. A **identitásszolgáltató Entitásazonosító:** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító**, Azure Portalról másolt.

    b. A **identitásszolgáltató egyszeri bejelentkezési URL-cím:** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    c. A **identitásszolgáltató SLO URL-cím:** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe**, Azure Portalról másolt.

    d. Nyissa meg a **base-64 kódolású tanúsítvány** a Jegyzettömbben az Azure-portálról letöltött, másolja a tartalmát a vágólapra, és illessze be azt a **x509 nyilvános tanúsítvány:** szövegmezőbe.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="creating-a-textmagic-test-user"></a>TextMagic tesztfelhasználó létrehozása

Alkalmazás által támogatott **csak az idő felhasználókiépítés** , miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön. Írja be az adatokat a rendszer az alárendelt fiók aktiválására szolgáló első bejelentkezéskor egyszer kell.
Nincs meg ebben a szakaszban a művelet elem.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TextMagic Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **TextMagic**.

    ![Egyszeri bejelentkezés konfigurálása](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a TextMagic csempére kattint, meg kell lekérése automatikusan bejelentkezett az TextMagic alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
