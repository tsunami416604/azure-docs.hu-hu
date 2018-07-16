---
title: 'Oktatóanyag: Azure Active Directory-integráció az HappyFox |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és HappyFox között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jeedes
ms.openlocfilehash: 3dc516e24f140ad5ef7443a4cff5962e7d584065
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042367"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Oktatóanyag: Azure Active Directory-integráció az HappyFox

Ebben az oktatóanyagban elsajátíthatja, hogyan HappyFox integrálása az Azure Active Directory (Azure AD).

HappyFox integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá HappyFox Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett HappyFox (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

HappyFox az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy HappyFox egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. HappyFox hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-happyfox-from-the-gallery"></a>HappyFox hozzáadása a katalógusból
Az Azure AD integrálása a HappyFox konfigurálásához hozzá kell HappyFox a katalógusból a felügyelt SaaS-alkalmazások listájára.

**HappyFox hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **HappyFox**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/happyfox-tutorial/tutorial_happyfox_search.png)

5. Az eredmények panelen válassza ki a **HappyFox**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/happyfox-tutorial/tutorial_happyfox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó HappyFox az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó HappyFox mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó HappyFox hivatkozás kapcsolata kell létrehozni.

HappyFox, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az HappyFox tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[HappyFox tesztfelhasználó létrehozása](#creating-a-happyfox-test-user)**  – egy megfelelője a Britta Simon HappyFox, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és HappyFox alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés HappyFox, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **HappyFox** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/tutorial_happyfox_samlbase.png)

3. Az a **HappyFox tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/tutorial_happyfox_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.happyfox.com/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [HappyFox ügyfél-támogatási csapatának](https://support.happyfox.com/home) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/tutorial_happyfox_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/tutorial_general_400.png)

6. Az a **HappyFox konfigurációs** területén kattintson **konfigurálása HappyFox** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban**.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/tutorial_happyfox_configure.png) 

7. Jelentkezzen be a HappyFox személyzet portálra, és navigáljon a **kezelés**, kattintson a **Integrációk** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/header.png) 

8. Az Integrációkat lapon kattintson a **konfigurálása** alatt **SAML-integráció** a egyszeri bejelentkezést a beállításainak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/configure.png) 

9. SAML-konfigurációja területen belül illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt **SSO cél URL-cím** szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/targeturl.png)

10. Nyissa meg a Jegyzettömbben az Azure portálról letöltött tanúsítványt, és másolja be a **identitásszolgáltatót aláírását** szakaszban.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/cert.png)

11. Kattintson a **beállítások mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/savesettings.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/happyfox-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/happyfox-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/happyfox-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/happyfox-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-happyfox-test-user"></a>HappyFox tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban.
        
### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés HappyFox Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel HappyFox, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **HappyFox**.

    ![Egyszeri bejelentkezés konfigurálása](./media/happyfox-tutorial/tutorial_happyfox_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

1. Ha a hozzáférési panelen a HappyFox csempére kattint, HappyFox alkalmazás bejelentkezési oldalának szerezheti be. Megtekintheti a **"SAML"** gomb a bejelentkezési oldalon.

    ![Beépülő modul](./media/happyfox-tutorial/saml.png) 

2. Kattintson a **"SAML"** gombra kattintva jelentkezzen be az Azure AD-fiókot használó HappyFox.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/happyfox-tutorial/tutorial_general_01.png
[2]: ./media/happyfox-tutorial/tutorial_general_02.png
[3]: ./media/happyfox-tutorial/tutorial_general_03.png
[4]: ./media/happyfox-tutorial/tutorial_general_04.png

[100]: ./media/happyfox-tutorial/tutorial_general_100.png

[200]: ./media/happyfox-tutorial/tutorial_general_200.png
[201]: ./media/happyfox-tutorial/tutorial_general_201.png
[202]: ./media/happyfox-tutorial/tutorial_general_202.png
[203]: ./media/happyfox-tutorial/tutorial_general_203.png

