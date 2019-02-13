---
title: 'Oktatóanyag: Képernyőfelvétel-O-értéket az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a képernyőfelvétel-O felosztásban között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51daf3b9553babb5fc627c02d79914c68c318145
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190823"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Oktatóanyag: Képernyőfelvétel-O-értéket az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a képernyőfelvétel-O-értéket az Azure Active Directoryval (Azure AD).

Az Azure AD integrálása a képernyőfelvétel-O-értéket nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a képernyőfelvétel-O-értéket az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a képernyőfelvétel-O-értéket (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a képernyőfelvétel-O-értéket, a következőkre van szükség:

- Azure AD-előfizetés
- A képernyőfelvétel-O felosztásban egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadása a képernyőfelvétel-O-értéket a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Hozzáadása a képernyőfelvétel-O-értéket a katalógusból
Az Azure AD integrálása a képernyőfelvétel-O automatikus konfigurálásához, hozzá kell képernyőfelvétel-O-értéket a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a képernyőfelvétel-O-értéket a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **képernyőfelvétel-O felosztásban**, jelölje be **képernyőfelvétel-O-értéket** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Képernyőfelvétel-O-értéket a találatok listájában](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés képernyőfelvétel-O-értéket a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó a képernyőfelvétel-O felosztásban mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a képernyőfelvétel-O értéket hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a képernyőfelvétel-O felosztásban tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Képernyőfelvétel-O felosztásban tesztfelhasználó létrehozása](#create-a-screencast-o-matic-test-user)**  – képernyőfelvétel-O-értéket, amely a felhasználó Azure ad-ben ábrázolása van csatolva van egy megfelelője a Britta Simon.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a képernyőfelvétel-O automatikus alkalmazása az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a képernyőfelvétel-O felosztásban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **képernyőfelvétel-O felosztásban** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Az a **képernyőfelvétel-O felosztásban tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Képernyőfelvétel-O felosztásban tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/screencast-tutorial/tutorial_screencast_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [képernyőfelvétel-O automatikus ügyfél-támogatási csapatának](mailto:support@screencast-o-matic.com) a gépkulcsengedélyek értékének. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/screencast-tutorial/tutorial_general_400.png)

6. Egy másik böngészőablakban, jelentkezzen be a képernyőfelvétel-O-értéket rendszergazdaként.

7. Kattintson a **előfizetés**.

    ![Az előfizetés](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. A **lapot** részen kattintson **telepítő**.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. Az a **telepítő lapot**, hajtsa végre az alábbi lépéseket:

    * A **URL-CÍMEN** szakaszban, a megadott szövegmezőbe írja be a példánynév.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_access.png)

    * Válassza ki **szükséges tartományi felhasználó** alatt **SAML felhasználói korlátozása (nem kötelező)** szakaszban.

    * A **Identitásszolgáltató metaadatainak XML-fájl feltöltése**, kattintson a **fájl kiválasztása** feltölteni a metaadatokat, amelyek már letöltötte az Azure Portalról.

    * Kattintson az **OK** gombra. 

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/screencast-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/screencast-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/screencast-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/screencast-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Képernyőfelvétel-O felosztásban tesztfelhasználó létrehozása

Ez a szakasz célja a képernyőfelvétel-O felosztásban Britta Simon nevű felhasználó létrehozásához. Képernyőfelvétel-O-értéket támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni képernyőfelvétel-O-értéket, ha még nem létezik jön létre.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [képernyőfelvétel-O automatikus ügyfél-támogatási csapatának](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés hozzáférést biztosít a képernyőfelvétel-O értéket használja.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel képernyőfelvétel-O felosztásban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **képernyőfelvétel-O felosztásban**.

    ![Az alkalmazások listáját a képernyőfelvétel-O felosztásban hivatkozás](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a képernyőfelvétel-O felosztásban csempére kattint, akkor kell lekérése automatikusan bejelentkezett a képernyőfelvétel-O felosztásban alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png

