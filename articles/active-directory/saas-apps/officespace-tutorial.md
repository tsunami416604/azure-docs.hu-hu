---
title: 'Oktatóanyag: Az Azure Active Directory-integráció OfficeSpace szoftverrel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és OfficeSpace szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d1c48c10d2c58e5cb2ffd7df296390bfaf765bd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206395"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Oktatóanyag: Az Azure Active Directory-integráció OfficeSpace szoftverrel

Ebben az oktatóanyagban elsajátíthatja, hogyan OfficeSpace szoftver integrálása az Azure Active Directory (Azure AD).

OfficeSpace szoftver integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá OfficeSpace szoftverek az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett OfficeSpace szoftverre (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása OfficeSpace szoftverekkel rendelkező, a következő elemek szükségesek:

- Azure AD-előfizetés
- Egy OfficeSpace szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. OfficeSpace szoftver hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-officespace-software-from-the-gallery"></a>OfficeSpace szoftver hozzáadása a katalógusból
Az Azure AD-be OfficeSpace szoftver integráció konfigurálásához, kell OfficeSpace szoftver hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**OfficeSpace szoftver hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **OfficeSpace szoftver**, jelölje be **OfficeSpace szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában OfficeSpace szoftver](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és OfficeSpace szoftverrel az Azure AD egyszeri bejelentkezés tesztelése egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó OfficeSpace szoftverfrissítési mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó OfficeSpace szoftveres hivatkozás kapcsolata kell létrehozni.

OfficeSpace szoftver, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés OfficeSpace szoftverrel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy OfficeSpace szoftver tesztfelhasználót](#create-a-officespace-software-test-user)**  – egy megfelelője a Britta Simon OfficeSpace szoftver, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és OfficeSpace szoftver alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása OfficeSpace szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **OfficeSpace szoftver** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

1. Az a **OfficeSpace szoftver tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![OfficeSpace szoftver tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [OfficeSpace Szoftverügyfél támogatási csapatának](mailto:support@officespacesoftware.com) beolvasni ezeket az értékeket. 

1. OfficeSpace szoftveralkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Attribútum konfigurálása](./media/officespace-tutorial/tutorial_officespace_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédablakban válassza **user.mail** , **felhasználói azonosító** és minden egyes sorára látható az alábbi táblázat a következő lépésekkel:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | e-mail | user.mail |
    | név | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Konfigurálása hozzáadása ](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Attribútum konfigurálása](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**
 
1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** a tanúsítvány értékét.

    ![A tanúsítvány letöltési hivatkozás](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/officespace-tutorial/tutorial_general_400.png)

1. Az a **OfficeSpace szoftverfrissítési konfiguráció** területén kattintson **OfficeSpace szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![OfficeSpace szoftverfrissítési konfiguráció](./media/officespace-tutorial/tutorial_officespace_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a OfficeSpace szoftver bérlői rendszergazdaként.

1. Lépjen a **beállítások** kattintson **összekötők**.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/officespace-tutorial/tutorial_officespace_002.png)

1. Kattintson a **SAML-hitelesítés**.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/officespace-tutorial/tutorial_officespace_003.png)

1. Az a **SAML-hitelesítés** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. A a **kijelentkezési szolgáltató URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt.

    b. Az a **ügyfél identitásszolgáltató cél URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    c. Illessze be a **ujjlenyomat** érték, amely az Azure Portalról, másolta a **ügyfél Identitásszolgáltató tanúsítvány-ujjlenyomat** szövegmezőbe. 

    d. Kattintson a **beállítások mentése**.


> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/officespace-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/officespace-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/officespace-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/officespace-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-officespace-software-test-user"></a>OfficeSpace szoftver tesztfelhasználó létrehozása

Ez a szakasz célja OfficeSpace szoftver Britta Simon nevű felhasználó létrehozásához. OfficeSpace szoftver támogatja-e just-in-time-kiépítés, amely alapértelmezés szerint van engedélyezve.

Nincs meg ebben a szakaszban a művelet elem. Egy új felhasználót hoz létre az OfficeSpace szoftver elérésére, ha még nem létezik tett kísérlet során.

> [!NOTE]
> Ha kézzel létrehoz egy felhasználót van szüksége, forduljon kell [OfficeSpace szoftver támogatási csapatának](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés OfficeSpace szoftver Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel OfficeSpace szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **OfficeSpace szoftver**.

    ![Az alkalmazások listáját a OfficeSpace szoftver hivatkozás](./media/officespace-tutorial/tutorial_officespace_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a OfficeSpace szoftver csempére kattint, meg kell lekérése automatikusan bejelentkezett az OfficeSpace szoftver alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

