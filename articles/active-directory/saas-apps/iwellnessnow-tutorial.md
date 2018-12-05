---
title: 'Oktatóanyag: Azure Active Directory-integráció az iWellnessNow |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iWellnessNow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24ffc841-7a77-481c-9cc4-6f8bda58fe66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b02a5ec5a68743e148bcfef3c631d7777fd0a067
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871684"
---
# <a name="tutorial-azure-active-directory-integration-with-iwellnessnow"></a>Oktatóanyag: Azure Active Directory-integráció az iWellnessNow

Ebben az oktatóanyagban elsajátíthatja, hogyan iWellnessNow integrálása az Azure Active Directory (Azure AD).

IWellnessNow integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá iWellnessNow Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) iWellnessNow.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

IWellnessNow az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy iWellnessNow egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. IWellnessNow hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-iwellnessnow-from-the-gallery"></a>IWellnessNow hozzáadása a katalógusból
Az Azure AD integrálása a iWellnessNow konfigurálásához hozzá kell iWellnessNow a katalógusból a felügyelt SaaS-alkalmazások listájára.

**IWellnessNow hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **iWellnessNow**válassza **iWellnessNow** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés iWellnessNow a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó iWellnessNow mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó iWellnessNow hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az iWellnessNow tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy iWellnessNow tesztfelhasználót](#create-an-iwellnessnow-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó iWellnessNow rendelkeznie.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és iWellnessNow alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iWellnessNow, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **iWellnessNow** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_samlbase.png)

1. Az a **iWellnessNow tartomány és URL-címek** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl** való az alkalmazás konfigurálásához és **Identitásszolgáltató** kezdeményezett mód, hajtsa végre a a következő lépéseket:

    ![Tartomány és URL-címek egyszeri bejelentkezés iWellnessNow feltöltése](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_upload.png)

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Tartomány és URL-címek egyszeri bejelentkezés iWellnessNow uploadconfig](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_uploadconfig.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.
    
    c. Feltöltés sikeres befejezése után **szolgáltató metaadatait tartalmazó fájl** a **azonosító** és **válasz URL-cím** értékeket automatikusan az első  **iWellnessNow tartomány és URL-címek** szövegmező szakasz alább látható módon:

    ![iWellnessNow tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url3.png)

1. Ha nem rendelkezik **szolgáltató metaadatait tartalmazó fájl** való az alkalmazás konfigurálásához és **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![iWellnessNow tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `http://<CustomerName>.iwellnessnow.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<CustomerName>.iwellnessnow.com/ssologin`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![iWellnessNow tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<CustomerName>.iwellnessnow.com/`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [iWellnessNow ügyfél-támogatási csapatának](mailto:info@iwellnessnow.com) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/iwellnessnow-tutorial/tutorial_general_400.png)
    
1. Az egyszeri bejelentkezés konfigurálása **iWellnessNow** oldalon kell küldenie a letöltött **metaadatainak XML** való [iWellnessNow támogatási csapatának](mailto:info@iwellnessnow.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/iwellnessnow-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/iwellnessnow-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/iwellnessnow-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/iwellnessnow-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-iwellnessnow-test-user"></a>Hozzon létre egy iWellnessNow tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű iWellnessNow hoz létre. Együttműködve [iWellnessNow támogatási csapatának](mailto:info@iwellnessnow.com) a felhasználók hozzáadása az iWellnessNow platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iWellnessNow való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel iWellnessNow, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **iWellnessNow**.

    ![Az alkalmazások listáját a iWellnessNow hivatkozásra](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a iWellnessNow csempére kattint, meg kell lekérése automatikusan bejelentkezett az iWellnessNow alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iwellnessnow-tutorial/tutorial_general_01.png
[2]: ./media/iwellnessnow-tutorial/tutorial_general_02.png
[3]: ./media/iwellnessnow-tutorial/tutorial_general_03.png
[4]: ./media/iwellnessnow-tutorial/tutorial_general_04.png

[100]: ./media/iwellnessnow-tutorial/tutorial_general_100.png

[200]: ./media/iwellnessnow-tutorial/tutorial_general_200.png
[201]: ./media/iwellnessnow-tutorial/tutorial_general_201.png
[202]: ./media/iwellnessnow-tutorial/tutorial_general_202.png
[203]: ./media/iwellnessnow-tutorial/tutorial_general_203.png

