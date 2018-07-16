---
title: 'Oktatóanyag: Azure Active Directory-integráció az SignalFx |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SignalFx között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 24a5c1a260cf86f0860e0292c2eb9527f976a363
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040510"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Oktatóanyag: Azure Active Directory-integráció az SignalFx

Ebben az oktatóanyagban elsajátíthatja, hogyan SignalFx integrálása az Azure Active Directory (Azure AD).

SignalFx integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SignalFx Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SignalFx (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

SignalFx az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy SignalFx egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SignalFx hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-signalfx-from-the-gallery"></a>SignalFx hozzáadása a katalógusból
Az Azure AD integrálása a SignalFx konfigurálásához hozzá kell SignalFx a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SignalFx hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **SignalFx**válassza **SignalFx** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SignalFx](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés SignalFx a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SignalFx mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SignalFx hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SignalFx tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy SignalFx tesztfelhasználót](#create-a-signalfx-test-user)**  – egy megfelelője a Britta Simon SignalFx, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SignalFx alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SignalFx, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SignalFx** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Az a **SignalFx tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SignalFx tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://api.signalfx.com/v1/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Az előző érték nem valódi értéket. A tényleges válasz URL-cím, az oktatóanyag későbbi részében ismertetett, frissítse az értéket.

4. SignalFx alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.   

    ![Egyszeri bejelentkezés konfigurálása](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | User.FirstName          | User.givenName |
    | User.email          | user.mail |
    | PersonImmutableID       | User.userPrincipalName    |
    | User.LastName       | User.surname    |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés Addattb konfigurálása](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.
 
6. Az a **SAML-aláíró tanúsítvány** szakaszban, hajtsa végre az alábbi lépéseket: 

    ![A tanúsítvány letöltési hivatkozás](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    b. Kattintson a **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/signalfx-tutorial/tutorial_general_400.png)

8. Az a **SignalFx konfigurációs** területén kattintson **konfigurálása SignalFx** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    ![SignalFx konfiguráció](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Bejelentkezés a SignalFx vállalati webhelyre rendszergazdaként.

10. A SignalFx, kattintson a felső a **Integrációk** Integrációk lap megnyitásához.

    ![SignalFx integráció](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

11. Kattintson a **Azure Active Directory** alatt csempe **bejelentkezési szolgáltatások** szakaszban.
 
    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

12. Kattintson a **új integrációs** és a **telepítése** lapon tegye a következőket:
 
    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Az a **neve** szövegmezőbe írja be, egy új integrációs nevet, például **OurOrgName SAML SSO**.

    b. Másolás a **integrációs azonosítója** értékét, és a hozzáfűző a **válasz URL-cím** például `https://api.signalfx.com/v1/saml/acs/<integration ID>` a a **válasz URL-cím** szövegmezőben, hogy a **SignalFx tartomány és URL-címek** szakaszban az Azure Portalon.

    c. Kattintson a **fájl feltöltése** feltölteni a **Base64-kódolású tanúsítványt** az Azure Portalról letöltött a **tanúsítvány** szövegmezőbe.

    d. A a **kiállítójának URL-címe** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító**, az Azure Portalról másolt.

    e. Az a **metaadatok URL-címe** szövegmezőjébe illessze be a **alkalmazás összevonási metaadatainak URL-címe** az Azure Portalról másolt.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/signalfx-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/signalfx-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/signalfx-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/signalfx-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-signalfx-test-user"></a>SignalFx tesztfelhasználó létrehozása

Ez a szakasz célja SignalFx Britta Simon nevű felhasználó létrehozásához. SignalFx támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az SignalFx elérésére, ha még nem létezik tett kísérlet során.

Amikor egy felhasználó bejelentkezik SignalFx a SAML SSO az első alkalommal [SignalFx támogatási csapatának](mailto:kmazzola@signalfx.com) őket, hogy azok kattintással kell hitelesíteni hivatkozást tartalmazó e-mailt küld. Ez csak akkor történik, a felhasználó bejelentkezésekor; az első alkalommal további bejelentkezési kísérletek nem követeli meg az e-mail-ellenőrzést.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [SignalFx támogatási csoport](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SignalFx Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel SignalFx, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **SignalFx**.

    ![Az alkalmazások listáját a SignalFx hivatkozásra](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SignalFx csempére kattint, meg kell lekérése automatikusan bejelentkezett az SignalFx alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png

