---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező etouches |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és etouches között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 7e4dcc91ecafab6fc0b7864658b0f7724ec04bf2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813916"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező etouches

Ebben az oktatóanyagban elsajátíthatja, hogyan etouches integrálása az Azure Active Directory (Azure AD).

Etouches integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá etouches Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) etouches
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Etouches az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy etouches egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Etouches hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-etouches-from-the-gallery"></a>Etouches hozzáadása a katalógusból
Az Azure AD integrálása a etouches konfigurálásához hozzá kell etouches a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Etouches hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **etouches**válassza **etouches** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában etouches](./media/etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés etouches a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó etouches mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó etouches hivatkozás kapcsolata kell létrehozni.

Etouches, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az etouches tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy etouches tesztfelhasználót](#create-an-etouches-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó etouches rendelkeznie.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és etouches alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés etouches, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **etouches** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/etouches-tutorial/tutorial_etouches_samlbase.png)

1. Az a **etouches tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Tartomány és URL-címek etouches egyszeri bejelentkezési adatait](./media/etouches-tutorial/tutorial_etouches_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Ezek a értékei nem valódi. A tényleges bejelentkezési URL-címet és az azonosítója, amelynek az ismertetése az oktatóanyag későbbi részében, frissítse az értéket.
    > 

1. etouches alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútum** az alkalmazás. Az alábbi képernyőfelvételen látható erre egy példa látható. 

    ![Felhasználói attribútum](./media/etouches-tutorial/tutorial_etouches_attribute.png) 

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |
    | E-mail | user.mail |    
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Attribútum hozzáadása](./media/etouches-tutorial/tutorial_attribute_04.png)

    ![Attribútum párbeszédpanel hozzáadása](./media/etouches-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/etouches-tutorial/tutorial_etouches_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/etouches-tutorial/tutorial_general_400.png)

1. Egyszeri bejelentkezés konfigurálva az alkalmazás eléréséhez hajtsa végre az alábbi lépéseket a etouches alkalmazásban: 

    ![etouches konfiguráció](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Jelentkezzen be **etouches** alkalmazás használatával a rendszergazdai jogosultságokkal.
   
    b. Nyissa meg a **SAML** konfigurációja.

    c. Az a **általános beállítások** szakaszban, nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbben, másolja a tartalmat és illessze be az Identitásszolgáltató metaadatok szövegmezőbe. 

    d. Kattintson a **mentés, és legyen naprakész** gombra.
  
    e. Kattintson a **frissítési metaadatokat** gombot a SAML-metaadatokat tartalmazó szakasz. 

    f. Ez megnyitja a lapot, és hajtsa végre az egyszeri Bejelentkezést. Miután az egyszeri bejelentkezés működik majd állíthat be a felhasználónevet.

    g. A felhasználónév mezőben válassza ki a **emailaddress** az alábbi képen látható módon. 

    h. Másolás a **SP Entitásazonosító** értékét, és illessze be azt a **azonosító** szövegmező, amely **etouches tartomány és URL-címek** szakaszban az Azure Portalon.

    i. Másolás a **egyszeri bejelentkezési URL-cím / ACS** értékét, és illessze be azt a **bejelentkezési URL-cím** szövegmező, amely **etouches tartomány és URL-címek** szakaszban az Azure Portalon.
   
> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/etouches-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/etouches-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/etouches-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/etouches-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-etouches-test-user"></a>Hozzon létre egy etouches tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű etouches hoz létre. Együttműködve [etouches ügyfél-támogatási csoportjának](https://www.etouches.com/event-software/support/customer-support/) a felhasználók hozzáadása az etouches platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés etouches való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel etouches, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **etouches**.

    ![Az alkalmazások listáját a etouches hivatkozásra](./media/etouches-tutorial/tutorial_etouches_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése


Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a etouches csempére kattint, meg kell lekérése automatikusan bejelentkezett az etouches alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/etouches-tutorial/tutorial_general_01.png
[2]: ./media/etouches-tutorial/tutorial_general_02.png
[3]: ./media/etouches-tutorial/tutorial_general_03.png
[4]: ./media/etouches-tutorial/tutorial_general_04.png

[100]: ./media/etouches-tutorial/tutorial_general_100.png

[200]: ./media/etouches-tutorial/tutorial_general_200.png
[201]: ./media/etouches-tutorial/tutorial_general_201.png
[202]: ./media/etouches-tutorial/tutorial_general_202.png
[203]: ./media/etouches-tutorial/tutorial_general_203.png

