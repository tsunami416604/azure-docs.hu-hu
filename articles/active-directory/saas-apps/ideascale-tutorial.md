---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező IdeaScale |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és IdeaScale között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: ca5cac1888fe7e126d6bdc8bd4a2e9bc192f4d41
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810057"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező IdeaScale

Ebben az oktatóanyagban elsajátíthatja, hogyan IdeaScale integrálása az Azure Active Directory (Azure AD).

IdeaScale integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá IdeaScale Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett IdeaScale (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

IdeaScale az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy IdeaScale egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. IdeaScale hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-ideascale-from-the-gallery"></a>IdeaScale hozzáadása a katalógusból
Az Azure AD integrálása a IdeaScale konfigurálásához hozzá kell IdeaScale a katalógusból a felügyelt SaaS-alkalmazások listájára.

**IdeaScale hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **IdeaScale**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ideascale-tutorial/tutorial_ideascale_search.png)

1. Az eredmények panelen válassza ki a **IdeaScale**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó IdeaScale az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó IdeaScale mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó IdeaScale hivatkozás kapcsolata kell létrehozni.

IdeaScale, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az IdeaScale tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy IdeaScale tesztfelhasználó létrehozása](#creating-an-ideascale-test-user)**  – egy megfelelője a Britta Simon IdeaScale, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és IdeaScale alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés IdeaScale, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **IdeaScale** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_samlbase.png)

1. Az a **IdeaScale tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.ideascale.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [IdeaScale ügyfél-támogatási csapatának](https://support.ideascale.com/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_general_400.png)

1. Az a **IdeaScale konfigurációs** területén kattintson **konfigurálása IdeaScale** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT, és a SAML Entitásazonosító** származó a **gyors útmutató szakaszban**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a IdeaScale vállalati hely rendszergazdaként.

1. Lépjen a **beállításait Közösséghez**.
   
    ![Közösségi beállítások](./media/ideascale-tutorial/ic790847.png "beállításait Közösséghez")

1. Lépjen a **biztonsági \> egyszeri bejelentkezés beállításainak**.
   
    ![Egyszeri bejelentkezés beállításainak](./media/ideascale-tutorial/ic790848.png "egyszeri bejelentkezés beállításai")

1. Mint **egyszeri-bejelentkezés típusa**válassza **SAML 2.0**.
   
    ![Egyszeri bejelentkezés típusa](./media/ideascale-tutorial/ic790849.png "egyszeri bejelentkezés típusa")

1. Az a **egyszeri bejelentkezés beállításainak** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés beállításainak](./media/ideascale-tutorial/ic790850.png "egyszeri bejelentkezés beállításai")
   
    a. A **SAML identitásszolgáltató Entitásazonosító** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.

    b. Másolja a letöltött metaadatait tartalmazó fájl tartalmát az Azure Portalról, és illessze be azt a **identitásszolgáltató SAML-metaadatokat** szövegmezőbe.

    c. A **kijelentkezési sikeres URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt.

    d. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-ideascale-test-user"></a>Egy IdeaScale tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók IdeaScale szolgáltatásba való bejelentkezéshez, akkor ki kell építenie a IdeaScale. IdeaScale, esetén kiépítése a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **IdeaScale** rendszergazdaként a vállalati webhely.

1. Lépjen a **beállításait Közösséghez**.
   
    ![Közösségi beállítások](./media/ideascale-tutorial/ic790847.png "beállításait Közösséghez")

1. Lépjen a **alapbeállítások \> tag felügyeleti**.

1. Kattintson a **tag hozzáadása**.
   
    ![Tag felügyeleti](./media/ideascale-tutorial/ic790852.png "tag felügyeleti")

1. Az új tag hozzáadása a szakaszban a következő lépésekkel:
   
    ![Új tag hozzáadása](./media/ideascale-tutorial/ic790853.png "új tag hozzáadása")
   
    a. Az a **E-mail-címek** szövegmezőbe írja be egy érvényes AAD-fiók üzembe helyezni kívánt e-mail-címét.
   
    b. Kattintson a **módosítások mentése**. 
   
    >[!NOTE]
    >Az Azure Active Directory fióktulajdonos kap egy e-mailt, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók hivatkozását.
      
>[!NOTE]
>Bármely más IdeaScale felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését IdeaScale által biztosított API-k.
 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés IdeaScale Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel IdeaScale, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **IdeaScale**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése


Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a IdeaScale csempére kattint, meg kell lekérése automatikusan bejelentkezett az IdeaScale alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ideascale-tutorial/tutorial_general_01.png
[2]: ./media/ideascale-tutorial/tutorial_general_02.png
[3]: ./media/ideascale-tutorial/tutorial_general_03.png
[4]: ./media/ideascale-tutorial/tutorial_general_04.png

[100]: ./media/ideascale-tutorial/tutorial_general_100.png

[200]: ./media/ideascale-tutorial/tutorial_general_200.png
[201]: ./media/ideascale-tutorial/tutorial_general_201.png
[202]: ./media/ideascale-tutorial/tutorial_general_202.png
[203]: ./media/ideascale-tutorial/tutorial_general_203.png

