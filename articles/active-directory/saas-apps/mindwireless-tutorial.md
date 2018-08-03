---
title: 'Oktatóanyag: Azure Active Directory-integráció az mindWireless |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és mindWireless között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 6c6fe0a720795c67a7062f5a5971c699472fca07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434346"
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Oktatóanyag: Azure Active Directory-integráció az mindWireless

Ebben az oktatóanyagban elsajátíthatja, hogyan mindWireless integrálása az Azure Active Directory (Azure AD).

MindWireless integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá mindWireless Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) mindWireless.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

MindWireless az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy mindWireless egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. MindWireless hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-mindwireless-from-the-gallery"></a>MindWireless hozzáadása a katalógusból
Az Azure AD integrálása a mindWireless konfigurálásához hozzá kell mindWireless a katalógusból a felügyelt SaaS-alkalmazások listájára.

**MindWireless hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **mindWireless**válassza **mindWireless** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában mindWireless](./media/mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés mindWireless a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó mindWireless mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó mindWireless hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az mindWireless tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy mindWireless tesztfelhasználót](#create-a-mindwireless-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó mindWireless rendelkeznie.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és mindWireless alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés mindWireless, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **mindWireless** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

1. Az a **mindWireless tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Tartomány és URL-címek mindWireless egyszeri bejelentkezési adatait](./media/mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.mwsmart.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [mindWireless támogatási csoportjának](mailto:sdulloor@mindwireless.com) beolvasni ezeket az értékeket.

1. A mindWireless alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja.

1. Az alábbi képernyőfelvételen látható egy példa látható a hozzá tartozó. A jogcímszabály neve mindig lehet **alkalmazott azonosítója** és az érték, amelynek user.employeeid, amely tartalmazza a felhasználó az EmployeeID azt van leképezve. Itt történik, a felhasználó Azure AD-ből mindWireless leképezés az EmployeeID, de azt leképezhet egy másik értéket is alapján az alkalmazás beállításait. Használhatja a [mindWireless támogatási csoportjának](mailto:sdulloor@mindwireless.com) először a felhasználó a megfelelő azonosító használja, és képezze le az értéket a **alkalmazott azonosítója** jogcím.

    ![Egyszeri bejelentkezés konfigurálása](./media/mindwireless-tutorial/tutorial_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke | Namespace érték |
    | -------------- | --------------- | ----------------|
    | Alkalmazott azonosítója | User.EmployeeID | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/mindwireless-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/mindwireless-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Az a **Namespace** szövegmezőbe írja be a névtér értéke a sorhoz látható.
    
    e. Kattintson az **OK** gombra.
    
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/mindwireless-tutorial/tutorial_general_400.png)

1. Az a **mindWireless konfigurációs** területén kattintson **mindWireless konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![mindWireless konfiguráció](./media/mindwireless-tutorial/tutorial_mindwireless_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **mindWireless** oldalon kell küldenie a letöltött **Certificate(Base64), SAML egyszeri bejelentkezési szolgáltatás URL-cím**, és **SAML Entitásazonosító** , [mindWireless támogatási csoportjának](mailto:sdulloor@mindwireless.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/mindwireless-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/mindwireless-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/mindwireless-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/mindwireless-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-mindwireless-test-user"></a>MindWireless tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű mindWireless hoz létre. Együttműködve [mindWireless támogatási csoportjának](mailto:sdulloor@mindwireless.com) a felhasználók hozzáadása az mindWireless platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés mindWireless való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel mindWireless, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **mindWireless**.

    ![Az alkalmazások listáját a mindWireless hivatkozásra](./media/mindwireless-tutorial/tutorial_mindwireless_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a mindWireless csempére kattint, meg kell lekérése automatikusan bejelentkezett az mindWireless alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/mindwireless-tutorial/tutorial_general_203.png

