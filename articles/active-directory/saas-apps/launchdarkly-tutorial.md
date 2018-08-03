---
title: 'Oktatóanyag: Azure Active Directory-integráció az LaunchDarkly |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és LaunchDarkly között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420759"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Oktatóanyag: Azure Active Directory-integráció az LaunchDarkly

Ebben az oktatóanyagban elsajátíthatja, hogyan LaunchDarkly integrálása az Azure Active Directory (Azure AD).

LaunchDarkly integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá LaunchDarkly Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett LaunchDarkly (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

LaunchDarkly az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy LaunchDarkly egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. LaunchDarkly hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-launchdarkly-from-the-gallery"></a>LaunchDarkly hozzáadása a katalógusból
Az Azure AD integrálása a LaunchDarkly konfigurálásához hozzá kell LaunchDarkly a katalógusból a felügyelt SaaS-alkalmazások listájára.

**LaunchDarkly hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **LaunchDarkly**válassza **LaunchDarkly** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés LaunchDarkly a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó LaunchDarkly mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó LaunchDarkly hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az LaunchDarkly tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy LaunchDarkly tesztfelhasználót](#create-a-launchdarkly-test-user)**  – egy megfelelője a Britta Simon LaunchDarkly, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és LaunchDarkly alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés LaunchDarkly, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **LaunchDarkly** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. Az a **LaunchDarkly tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![LaunchDarkly tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. Az a **azonosító (entityid)** szövegmezőbe írja be az URL-cím: `app.launchdarkly.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A tényleges válasz URL-címe, amelynek az ismertetése az oktatóanyag későbbi részében fogja frissítse az értéket.

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![LaunchDarkly tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://app.launchdarkly.com`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. Az a **LaunchDarkly konfigurációs** területén kattintson **konfigurálása LaunchDarkly** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a LaunchDarkly vállalati hely rendszergazdaként.

1. Válassza ki **fiókbeállításokat** a bal oldali navigációs panelen.

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure1.png)

1. Kattintson a **biztonsági** fülre.

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure2.png)

1. Kattintson a **SSO engedélyezése** , majd **szerkesztési SAML-KONFIGURÁCIÓJA**.

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure3.png)

1. Az a **az SAML-konfiguráció szerkesztése** szakaszban, hajtsa végre az alábbi lépéseket:

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure4.png)

    a. Másolás a **SAML fogyasztói URL-címe** a példány, és illessze be a válasz URL-címe szövegmezőbe **LaunchDarkly tartomány és URL-címek** szakaszban az Azure Portalon.

    b. Az a **bejelentkezési URL-** szövegmezőjébe illessze be a **egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolta.

    c. Nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbbe. másolja a tartalmat, majd illessze be azt a **X.509-tanúsítvány** mezőbe, vagy Ön is közvetlenül feltölthet a tanúsítványt a **töltsön fel**.

    d. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-launchdarkly-test-user"></a>LaunchDarkly tesztfelhasználó létrehozása

Ez a szakasz célja LaunchDarkly Britta Simon nevű felhasználó létrehozásához. LaunchDarkly támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az LaunchDarkly elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [LaunchDarkly ügyfél-támogatási csapatának](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LaunchDarkly Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel LaunchDarkly, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **LaunchDarkly**.

    ![Az alkalmazások listáját a LaunchDarkly hivatkozásra](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LaunchDarkly csempére kattint, meg kell lekérése automatikusan bejelentkezett az LaunchDarkly alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

