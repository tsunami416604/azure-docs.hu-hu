---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező KnowledgeOwl |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és KnowledgeOwl között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 80d8d1664100cdfbbf6d537b41823c2db0204040
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54318917"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező KnowledgeOwl

Ebben az oktatóanyagban elsajátíthatja, hogyan KnowledgeOwl integrálása az Azure Active Directory (Azure AD).

KnowledgeOwl integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá KnowledgeOwl Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett KnowledgeOwl (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

KnowledgeOwl az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy KnowledgeOwl egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. KnowledgeOwl hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-knowledgeowl-from-the-gallery"></a>KnowledgeOwl hozzáadása a katalógusból
Az Azure AD integrálása a KnowledgeOwl konfigurálásához hozzá kell KnowledgeOwl a katalógusból a felügyelt SaaS-alkalmazások listájára.

**KnowledgeOwl hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **KnowledgeOwl**válassza **KnowledgeOwl** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés KnowledgeOwl a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó KnowledgeOwl mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó KnowledgeOwl hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az KnowledgeOwl tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy KnowledgeOwl tesztfelhasználót](#create-a-knowledgeowl-test-user)**  – egy megfelelője a Britta Simon KnowledgeOwl, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és KnowledgeOwl alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés KnowledgeOwl, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **KnowledgeOwl** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. Az a **KnowledgeOwl tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![KnowledgeOwl tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![KnowledgeOwl tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, a válasz URL-cím és a bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett ezek értéket kell.

1. A KnowledgeOwl alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/knowledgeowl-tutorial/attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke | Névtér|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d.From a **Namespace** listában, adja meg a névtér értéke a sorhoz látható.
    
    e. Kattintson az **OK** gombra.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. Az a **KnowledgeOwl konfigurációs** területén kattintson **konfigurálása KnowledgeOwl** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![KnowledgeOwl konfiguráció](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a KnowledgeOwl vállalati hely rendszergazdaként.

1. Kattintson a **beállítások** majd **biztonsági**.

    ![KnowledgeOwl konfiguráció](./media/knowledgeowl-tutorial/configure1.png)

1. Görgessen a **SAML SSO-integráció** , és hajtsa végre az alábbi lépéseket:
    
    ![KnowledgeOwl konfiguráció](./media/knowledgeowl-tutorial/configure2.png)

    a. Válassza ki **SAML egyszeri bejelentkezés engedélyezése**.

    b. Másolás a **SP Entitásazonosító** értékét, és illessze be azt a **azonosító (entityid)** a a **KnowledgeOwl tartomány és URL-címek** szakaszban az Azure Portalon.

    c. Másolás a **SP bejelentkezési URL-cím** értékét, és illessze be azt a **bejelentkezési URL- és a válasz URL-cím** a szöveges mezőkben a **KnowledgeOwl tartomány és URL-címek** szakaszban az Azure Portalon.

    d. Az a **identitásszolgáltató entityID** szövegmezőjébe illessze be a **SAML Entitásazonosító** érték, amely az Azure Portalról másolta.

    e. Az a **identitásszolgáltató bejelentkezési URL-cím** szövegmezőjébe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolta.

    f. Az a **identitásszolgáltató kijelentkezési URL-címe** szövegmezőjébe illessze be a **kijelentkezéses URL-cím** érték, amely az Azure Portalról másolt

    g. Töltse fel a letöltött tanúsítvány képernyő az Azure Portalon kattintson a **IdP-tanúsítvány feltöltése**.

    h. Kattintson a **térkép SAML-attribútumok** attribútumok leképezésére, és hajtsa végre az alábbi lépéseket:
    
    ![KnowledgeOwl konfiguráció](./media/knowledgeowl-tutorial/configure3.png)

    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` be a **egyszeri bejelentkezési azonosító** szövegmező
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` be a **felhasználónév, E-mail** szövegmezőbe.
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` be a **Utónév** szövegmezőbe.
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` be a **Vezetéknév** szövegmezőbe.
    * Kattintson a **Mentés** gombra.

    i. Kattintson a lap alján található **Mentés** gombra.

    ![KnowledgeOwl konfiguráció](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-knowledgeowl-test-user"></a>KnowledgeOwl tesztfelhasználó létrehozása

Ez a szakasz célja KnowledgeOwl Britta Simon nevű felhasználó létrehozásához. KnowledgeOwl támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az KnowledgeOwl elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [KnowledgeOwl támogatási csapatának](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés KnowledgeOwl Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel KnowledgeOwl, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **KnowledgeOwl**.

    ![Az alkalmazások listáját a KnowledgeOwl hivatkozásra](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a KnowledgeOwl csempére kattint, meg kell lekérése automatikusan bejelentkezett az KnowledgeOwl alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

