---
title: 'Oktatóanyag: Azure Active Directoryval integrált ADP Globalview |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ADP Globalview között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: fb2391224522ed152ddf23fc6684a1a95e08608e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35921698"
---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Oktatóanyag: Azure Active Directoryval integrált ADP Globalview

Ebben az oktatóanyagban elsajátíthatja ADP Globalview integrálása az Azure Active Directory (Azure AD).

ADP Globalview integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a ADP Globalview hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a ADP Globalview (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs ADP Globalview konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy ADP Globalview egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ADP Globalview hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-adp-globalview-from-the-gallery"></a>A gyűjteményből ADP Globalview hozzáadása
Az Azure AD integrálása a ADP Globalview konfigurálásához kell hozzáadnia ADP Globalview a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből ADP Globalview hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **ADP Globalview**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adglobalview-tutorial/tutorial_adpglobalview_search.png)

5. Az eredmények panelen válassza ki a **ADP Globalview**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adglobalview-tutorial/tutorial_adpglobalview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és a vizsgálat az Azure AD egyszeri bejelentkezést a ADP Globalview "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ADP Globalview a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a ADP Globalview közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** ADP Globalview a.

Az Azure AD egyszeri bejelentkezést a ADP Globalview tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy ADP Globalview tesztfelhasználó létrehozása](#creating-an-adp-globalview-test-user)**  - való egy megfelelője a Britta Simon ADP Globalview, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ADP Globalview alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés ADP Globalview, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ADP Globalview** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_adpglobalview_samlbase.png)

3. Az a **ADP Globalview tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_adpglobalview_url.png)

     Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-cím: `https://<subdomain>.globalview.adp.com/federate` vagy `https://<subdomain>.globalview.adp.com/federate2`

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges azonosítóval. Ügyfél [ADP Globalview támogatási](https://www.adp.com/contact-us/overview.aspx) az értéket be kell olvasni.
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_adpglobalview_certificate.png) 

5. A ADP GlobalView alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. 

6. Az alábbi képernyőfelvételen egy példát mutat be, amíg. A jogcím neve mindig lehet **"PersonImmutableID"** és az érték, amelynek jelenleg van leképezve ExtensionAttribute2, amely tartalmazza az EmployeeID felhasználó. Itt az EmployeeID állítva az Azure AD ADP GlobalView történik, de is képezi le egy másik értéket is alapján az alkalmazás beállításait. Használhatja a ADP GlobalView csapatától először használja a helyes azonosító egy olyan felhasználó, és képezze le az értéket a **"PersonImmutableID"** jogcímek. Az e-mailek és a UserID jogcím a képen látható módon is hozzárendelhető.

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_adpglobalview_attribute.png)

7. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | personalimmutableid | User.extensionattribute2 |
    | e-mailben               | user.mail |
    | felhasználói azonosítóját              | User.userPrincipalName|
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

    > [!NOTE] 
    > A SAML-előfeltétel konfigurálása előtt kapcsolatba kell lépnie a [ADP Globalview támogatási](https://www.adp.com/contact-us/overview.aspx) és az egyedi azonosító attribútum értékének kérhetnek a bérlő. Ezt az értéket az egyéni jogcímleírásokat, az alkalmazás konfigurálása van szüksége. 

8. A a **ADP Globalview konfigurációs** kattintson **konfigurálása ADP Globalview** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_adpglobalview_configure.png) 

9. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_general_400.png)

10. Egyszeri bejelentkezés konfigurálása **ADP Globalview** oldalon kell küldeniük a letöltött **tanúsítvány (Base64)**, **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** való [ADP Globalview támogatási](https://www.adp.com/contact-us/overview.aspx).

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adglobalview-tutorial/create_aaduser_01.png) 

2.  Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/adglobalview-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/adglobalview-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/adglobalview-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-adp-globalview-test-user"></a>Egy ADP Globalview tesztfelhasználó létrehozása

Ez a szakasz célja ADP GlobalView Britta Simon nevű felhasználót létrehozni. Együttműködve [ADP Globalview támogatási](https://www.adp.com/contact-us/overview.aspx) a felhasználók hozzáadása a ADP GlobalView fiók. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ADP Globalview Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése ADP Globalview, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ADP Globalview**.

    ![Egyszeri bejelentkezés konfigurálása](./media/adglobalview-tutorial/tutorial_adpglobalview_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.  

Ha a hozzáférési panelen ADP GlobalView csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az ADP GlobalView alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adglobalview-tutorial/tutorial_general_01.png
[2]: ./media/adglobalview-tutorial/tutorial_general_02.png
[3]: ./media/adglobalview-tutorial/tutorial_general_03.png
[4]: ./media/adglobalview-tutorial/tutorial_general_04.png

[100]: ./media/adglobalview-tutorial/tutorial_general_100.png

[200]: ./media/adglobalview-tutorial/tutorial_general_200.png
[201]: ./media/adglobalview-tutorial/tutorial_general_201.png
[202]: ./media/adglobalview-tutorial/tutorial_general_202.png
[203]: ./media/adglobalview-tutorial/tutorial_general_203.png

