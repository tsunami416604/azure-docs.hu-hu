---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező EverBridge |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és EverBridge között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95465fdc17131c996fa242d028addbab4191628c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191112"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező EverBridge

Ebben az oktatóanyagban elsajátíthatja, hogyan EverBridge integrálása az Azure Active Directory (Azure AD).

EverBridge integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá EverBridge Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett EverBridge (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

EverBridge az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy EverBridge egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. EverBridge hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-everbridge-from-the-gallery"></a>EverBridge hozzáadása a katalógusból

Az Azure AD integrálása a EverBridge konfigurálásához hozzá kell EverBridge a katalógusból a felügyelt SaaS-alkalmazások listájára.

**EverBridge hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **EverBridge**válassza **EverBridge** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában EverBridge](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés EverBridge a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó EverBridge mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó EverBridge hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az EverBridge tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy EverBridge tesztfelhasználó létrehozása](#creating-an-everbridge-test-user)**  – egy megfelelője a Britta Simon EverBridge, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és EverBridge alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés EverBridge, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **EverBridge** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

    >[!NOTE]
    >Kell tennie az alkalmazást a Manager portál vagy a tag portál mindkét végén, azaz az Azure Portal és Everbridge portál konfigurációit.

4. Konfigurálhatja a **EverBridge** alkalmazást, **EverBridge Manager portál**, az a **alapszintű SAML-konfigurációja** szakaszban a következő lépésekkel:

    ![EverBridge tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://sso.everbridge.net/<API_Name>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [EverBridge támogatási csapatának](mailto:support@everbridge.com) beolvasni ezeket az értékeket.

5. Konfigurálhatja a **EverBridge** alkalmazást, **EverBridge tag portál**, a a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    * Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

        ![EverBridge tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

        ![EverBridge tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezeket az értékeket frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címe. Kapcsolattartó [EverBridge támogatási csapatának](mailto:support@everbridge.com) beolvasni ezeket az értékeket.

6. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **összevonási metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Az a **EverBridge beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![EverBridge konfiguráció](common/configuresection.png)

8. Egyszeri bejelentkezés konfigurálva beolvasásához **EverBridge** , **EverBridge Manager portál** alkalmazás, hajtsa végre az alábbi lépéseket: 
 
9. Egy másik böngészőablakban, jelentkezzen be rendszergazdaként EverBridge.

9. A felső menüben kattintson a **beállítások** lapot, és válasszon **egyszeri bejelentkezés** alatt **biztonsági**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. Az a **neve** szövegmezőbe írja be a szolgáltató azonosító neve (például: a vállalat neve).
   
    b. Az a **API neve** szövegmezőbe írja be az API neve.
   
    c. Kattintson a **fájl kiválasztása** gombra kattintva töltse fel a metaadat-fájlt, amely az Azure Portalról letöltött.
   
    d. Válassza ki a SAML-identitás helyen **identitás a tulajdonos utasítás NameIdentifier elemében van**.
   
    e. Az a **Identity Provider bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.
   
    f. Válassza ki a szolgáltató által kezdeményezett kérelem kötés, **HTTP-átirányítás**.

    g. Kattintson a **Mentés** gombra.

10. Az egyszeri bejelentkezés konfigurálása **EverBridge** alkalmazást, **EverBridge tag portál**, kell küldenie a letöltött **összevonási metaadatainak XML** való [ Támogatási csoport Everbridge](mailto:support@everbridge.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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
  
### <a name="creating-an-everbridge-test-user"></a>Egy EverBridge tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Everbridge hoz létre. Együttműködve [EverBridge támogatási csapatának](mailto:support@everbridge.com) a felhasználók hozzáadása az Everbridge platformon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés EverBridge Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **EverBridge**.

    ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a EverBridge csempére kattint, meg kell lekérése automatikusan bejelentkezett az EverBridge alkalmazáshoz.
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
