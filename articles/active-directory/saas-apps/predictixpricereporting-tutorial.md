---
title: 'Oktatóanyag: Azure Active Directory-integráció az Predictix ár Reporting |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Predictix ár Reporting között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: 3686a90cb088dae99d20df619c161251b5bdfd60
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438943"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Oktatóanyag: Azure Active Directory-integráció az Predictix ár Reporting

Ebben az oktatóanyagban elsajátíthatja, hogyan Predictix ár Reporting integrálása az Azure Active Directoryval (Azure AD).

Predictix ár Reporting integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Predictix ár jelentéskészítés az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Predictix ár jelentéskészítéssel (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Predictix ár jelentéskészítési, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Predictix ár Reporting egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Predictix ár jelentés hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Predictix ár jelentés hozzáadása a katalógusból
Az Azure AD-be Predictix ár Reporting integráció konfigurálásához, hozzá kell Predictix ár Reporting a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Predictix ár jelentés hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Predictix ár Reporting**, jelölje be **Predictix ár Reporting** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Reporting ár Predictix](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Predictix ár Reporting "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Predictix ár jelentési mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Predictix ár a jelentési hivatkozás kapcsolata kell létrehozni.

Predictix ár Reporting, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Predictix ár jelentéskészítési tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Predictix ár Reporting tesztfelhasználót](#create-a-predictix-price-reporting-test-user)**  – van egy megfelelője a Britta Simon Predictix ár Reporting, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Predictix ár Reporting alkalmazását az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés az Predictix ár jelentéskészítési, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Predictix ár Reporting** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_samlbase.png)

1. Az a **Predictix ár Reporting tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Predictix ár Reporting tartomány és URL-címek egyszeri bejelentkezési adatait](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname-pricing>.predictix.com/sso/request`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname-pricing>.predictix.com` |
    | `https://<companyname-pricing>.dev.predictix.com` |

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Predictix ár Reporting ügyfél-támogatási csapatának](http://www.infor.com/company/customer-center/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/predictixpricereporting-tutorial/tutorial_general_400.png)

1. Az a **Predictix ár beállítása** területén kattintson **jelentéskészítés beállítása a Predictix ár** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Predictix ár beállítása](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Predictix ár Reporting** oldalon kell küldenie a letöltött **tanúsítvány (Base64)**, **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím**  való [ügyfélszolgálathoz Predictix ár Reporting](http://www.infor.com/company/customer-center/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/predictixpricereporting-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/predictixpricereporting-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/predictixpricereporting-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/predictixpricereporting-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-predictix-price-reporting-test-user"></a>Predictix ár Reporting tesztfelhasználó létrehozása

Ebben a szakaszban egy Predictix ár jelentési Britta Simon nevű felhasználó hoz létre. Együttműködve [ügyfélszolgálathoz Predictix ár Reporting](http://www.infor.com/company/customer-center/) a felhasználók hozzáadása az Predictix ár Reporting platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával Predictix ár a jelentéskészítés engedélyezéséhez.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Predictix ár Reporting, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Predictix ár Reporting**.

    ![Az alkalmazások listáját a Predictix ár jelentési hivatkozás](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Predictix ár Reporting csempére kattint, meg kell beolvasása automatikusan bejelentkezett az Predictix ár Reporting alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/predictixpricereporting-tutorial/tutorial_general_01.png
[2]: ./media/predictixpricereporting-tutorial/tutorial_general_02.png
[3]: ./media/predictixpricereporting-tutorial/tutorial_general_03.png
[4]: ./media/predictixpricereporting-tutorial/tutorial_general_04.png

[100]: ./media/predictixpricereporting-tutorial/tutorial_general_100.png

[200]: ./media/predictixpricereporting-tutorial/tutorial_general_200.png
[201]: ./media/predictixpricereporting-tutorial/tutorial_general_201.png
[202]: ./media/predictixpricereporting-tutorial/tutorial_general_202.png
[203]: ./media/predictixpricereporting-tutorial/tutorial_general_203.png

