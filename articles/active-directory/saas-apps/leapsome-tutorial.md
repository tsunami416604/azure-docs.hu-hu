---
title: 'Oktatóanyag: Azure Active Directory-integráció az Leapsome |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Leapsome között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.openlocfilehash: e55d161b7c95118736f4443c3fed0312418feee7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441935"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Oktatóanyag: Azure Active Directory-integráció az Leapsome

Ebben az oktatóanyagban elsajátíthatja, hogyan Leapsome integrálása az Azure Active Directory (Azure AD).

Leapsome integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Leapsome Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Leapsome (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Leapsome az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Leapsome egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Leapsome hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-leapsome-from-the-gallery"></a>Leapsome hozzáadása a katalógusból
Az Azure AD integrálása a Leapsome konfigurálásához hozzá kell Leapsome a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Leapsome hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Leapsome**válassza **Leapsome** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Leapsome](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Leapsome a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Leapsome mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Leapsome hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Leapsome tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Leapsome tesztfelhasználót](#create-a-leapsome-test-user)**  – egy megfelelője a Britta Simon Leapsome, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Leapsome alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Leapsome, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Leapsome** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. Az a **Leapsome tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Leapsome tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://www.leapsome.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Leapsome tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > A fenti válasz URL-cím és a bejelentkezési URL-cím értéke nem valódi érték. Frissíteni ezen a tényleges értékekre, amelyeket az oktatóanyag későbbi részében kifejtett.

1. Leapsome alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen szereplő példán látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke | Névtér |
    | ---------------| --------------- | --------- |   
    | Keresztnév | User.givenName | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Vezetéknév | User.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | cím | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | kép | Az alkalmazott kép URL-címe | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > A kép attribútum értéke nem valódi. Frissítse ezt az értéket tényleges kép URL-címe. Az e érték névjegy beolvasása [Leapsome ügyfél-támogatási csapatának](mailto:support@leapsome.com).
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Az a **Namespace** szövegmezőbe írja be a névtér URI-t a sorhoz.
    
    e. Kattintson a **Ok**

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. Az a **Leapsome konfigurációs** területén kattintson **konfigurálása Leapsome** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Leapsome konfiguráció](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Leapsome egy biztonsági-rendszergazdaként.

1. A jobb felső sarokban, kattintson a beállítások embléma, és kattintson a **adminisztrátori beállítások**. 

    ![Leapsome beállítása](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Kattintson a bal oldali menüsáv **egyszeri bejelentkezés (SSO)**, majd a a **SAML-alapú egyszeri bejelentkezés (SSO)** oldalon tegye a következőket:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Válassza ki **engedélyezése SAML-alapú egyszeri bejelentkezés**.

    b. Másolás a **bejelentkezési URL-címe (a felhasználók itt pont indítsa el a bejelentkezési)** értékét, és illessze be azt a **bejelentkezési URL-** szövegmezőjébe **Leapsome tartomány és URL-címek** szakaszban az Azure Portalon.

    c. Másolás a **válasz URL-cím (recieves válasz identitásszolgáltatótól származó)** értékét, és illessze be azt a **válasz URL-cím** szövegmezőjébe **Leapsome tartomány és URL-címek** szakaszban az Azure Portalon.

    d. Az a **Egyszeri bejelentkezési URL-címe (identitásszolgáltató által rendelkezésre bocsátott)** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    e. Másolja a tanúsítványt, hogy már letöltötte az Azure Portalról nélkül – a tanúsítvány KEZDŐ és záró tanúsítvány – megjegyzéseket, és illessze be a **(identitásszolgáltató által biztosított) tanúsítványt** szövegmezőbe.

    f. Kattintson a **frissítés egyszeri bejelentkezési BEÁLLÍTÁSAINAK**.
    
### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/leapsome-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/leapsome-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/leapsome-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/leapsome-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-leapsome-test-user"></a>Leapsome tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Leapsome hoz létre. Együttműködve [Leapsome ügyfél-támogatási csapatának](mailto:support@leapsome.com) hozzáadása a felhasználók vagy a tartományban kell lennie a Leapsome platform szerepel az engedélyezési listán. Ha a tartomány a csapata által hozzáadott, a Leapsome platform automatikusan kiépítve felhasználók. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Leapsome Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Leapsome, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Leapsome**.

    ![Az alkalmazások listáját a Leapsome hivatkozásra](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Leapsome csempére kattint, meg kell lekérése automatikusan bejelentkezett az Leapsome alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

