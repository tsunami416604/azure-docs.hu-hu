---
title: 'Oktatóanyag: Azure Active Directory-integráció az Adobe Captivate Prime |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Adobe Captivate Prime között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: bbeae2cadde3e64f17b20eafabaf5e2dbf5a5cc6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044072"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Oktatóanyag: Azure Active Directory-integráció az Adobe Captivate Prime

Ebben az oktatóanyagban elsajátíthatja, hogyan Adobe Captivate Prime integrálása az Azure Active Directory (Azure AD).

Az Adobe Captivate Prime integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az Adobe Captivate Prime Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Adobe Captivate Prime (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Captivate Prime, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az Adobe Captivate Prime egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az Adobe Captivate Prime hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Az Adobe Captivate Prime hozzáadása a katalógusból
Az Azure AD-be Adobe Captivate Prime integrációjának konfigurálása, hozzá kell Adobe Captivate Prime a galériából a felügyelt SaaS-alkalmazások listájára.

**Az Adobe Captivate Prime hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Adobe Captivate Prime**, jelölje be **Adobe Captivate Prime** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Adobe Captivate Prime a találatok listájában](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az Adobe Captivate Prime a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Adobe Captivate Prime mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az Adobe Captivate Prime hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Adobe Captivate Prime tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Adobe Captivate Prime tesztfelhasználót](#create-an-adobe-captivate-prime-test-user)**  – egy megfelelője a Britta Simon Adobe Captivate Prime, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Adobe Captivate Prime alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés az Adobe Captivate Prime, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Adobe Captivate Prime** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. Az a **Adobe Captivate Prime tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az Adobe Captivate Prime tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://captivateprime.adobe.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: `https://captivateprime.adobe.com/saml/SSO`

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Lépjen a **tulajdonságok** fülre, másolja a **felhasználói hozzáférési URL-címe** , és illessze be a Jegyzettömbben.

    ![A felhasználó hozzáférési hivatkozása](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Az egyszeri bejelentkezés konfigurálása **Adobe Captivate Prime** oldalon kell küldenie a letöltött **metaadatainak XML** és másolt **felhasználói hozzáférési URL-címe** való [Adobe Támogatási csoport Prime captivate](mailto:captivateprimesupport@adobe.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Az Adobe Captivate Prime tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon ezt az Adobe Captivate Prime hoz létre. Együttműködve [Adobe Captivate Prime támogatási csapatának](mailto:captivateprimesupport@adobe.com) a felhasználók hozzáadása az Adobe Captivate Prime platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Adobe Captivate Prime Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel az Adobe Captivate Prime, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Adobe Captivate Prime**.

    ![Az alkalmazások listáját az Adobe Captivate Prime hivatkozásra](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Adobe Captivate Prime csempére kattint, akkor kell lekérése automatikusan bejelentkezett az Adobe Captivate Prime alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

