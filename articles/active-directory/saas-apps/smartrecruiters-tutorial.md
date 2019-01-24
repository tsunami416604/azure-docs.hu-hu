---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SmartRecruiters |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SmartRecruiters között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: jeedes
ms.openlocfilehash: 6624368e0438b8f41a0f90c5da3f7f00f4cbc86c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813559"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SmartRecruiters

Ebben az oktatóanyagban elsajátíthatja, hogyan SmartRecruiters integrálása az Azure Active Directory (Azure AD).

SmartRecruiters integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SmartRecruiters Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SmartRecruiters (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

SmartRecruiters az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy SmartRecruiters egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SmartRecruiters hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-smartrecruiters-from-the-gallery"></a>SmartRecruiters hozzáadása a katalógusból
Az Azure AD integrálása a SmartRecruiters konfigurálásához hozzá kell SmartRecruiters a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SmartRecruiters hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SmartRecruiters**válassza **SmartRecruiters** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SmartRecruiters](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés SmartRecruiters a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SmartRecruiters mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SmartRecruiters hivatkozás kapcsolata kell létrehozni.

SmartRecruiters, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SmartRecruiters tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy SmartRecruiters tesztfelhasználót](#create-a-smartrecruiters-test-user)**  – egy megfelelője a Britta Simon SmartRecruiters, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SmartRecruiters alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SmartRecruiters, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SmartRecruiters** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_samlbase.png)

1. Az a **SmartRecruiters tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![SmartRecruiters tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![SmartRecruiters tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [SmartRecruiters ügyfél-támogatási csapatának](https://www.smartrecruiters.com/about-us/contact-us/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , majd mentse a tanúsítványt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/smartrecruiters-tutorial/tutorial_general_400.png)
    
1. Az a **SmartRecruiters konfigurációs** területén kattintson **konfigurálása SmartRecruiters** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![SmartRecruiters konfiguráció](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a SmartRecruiters vállalati hely rendszergazdaként.

1. Lépjen a **beállítások / Admin**.

    ![SmartRecruiters konfiguráció](./media/smartrecruiters-tutorial/configure.png)

1. Az a **konfigurációs** területén kattintson **webes egyszeri bejelentkezés**.

    ![SmartRecruiters konfiguráció](./media/smartrecruiters-tutorial/configure1.png)

1. Váltógomb **webes egyszeri bejelentkezés engedélyezése**.

    ![SmartRecruiters konfiguráció](./media/smartrecruiters-tutorial/configure2.png)

1. A **identitás-szolgáltató konfigurációjának**, hajtsa végre az alábbi lépéseket:

    ![SmartRecruiters konfiguráció](./media/smartrecruiters-tutorial/configure4.png)

    a. A **identitásszolgáltató szolgáltató URL-címe** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    b. Nyissa meg **certificate(Base64)** már letöltötte az Azure Portalról, és illessze be az értéket, amely **identitásszolgáltató tanúsítvány** szövegmezőbe.

1. Kattintson a **mentése a webes egyszeri bejelentkezés konfigurációja**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/smartrecruiters-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/smartrecruiters-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/smartrecruiters-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/smartrecruiters-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-smartrecruiters-test-user"></a>SmartRecruiters tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű SmartRecruiters hoz létre. Együttműködve [SmartRecruiters támogatási csoportjának](https://www.smartrecruiters.com/about-us/contact-us/) a felhasználók hozzáadása az SmartRecruiters platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SmartRecruiters Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel SmartRecruiters, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SmartRecruiters**.

    ![Az alkalmazások listáját a SmartRecruiters hivatkozásra](./media/smartrecruiters-tutorial/tutorial_smartrecruiters_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SmartRecruiters csempére kattint, meg kell lekérése automatikusan bejelentkezett az SmartRecruiters alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smartrecruiters-tutorial/tutorial_general_01.png
[2]: ./media/smartrecruiters-tutorial/tutorial_general_02.png
[3]: ./media/smartrecruiters-tutorial/tutorial_general_03.png
[4]: ./media/smartrecruiters-tutorial/tutorial_general_04.png

[100]: ./media/smartrecruiters-tutorial/tutorial_general_100.png

[200]: ./media/smartrecruiters-tutorial/tutorial_general_200.png
[201]: ./media/smartrecruiters-tutorial/tutorial_general_201.png
[202]: ./media/smartrecruiters-tutorial/tutorial_general_202.png
[203]: ./media/smartrecruiters-tutorial/tutorial_general_203.png

