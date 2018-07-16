---
title: 'Oktatóanyag: Azure Active Directory-integráció az CylancePROTECT |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és CylancePROTECT között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea392d8c-c8aa-4475-99d0-b08524ef0f3a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jeedes
ms.openlocfilehash: 62709817e6f906922ff1008608770bcfa5605078
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042707"
---
# <a name="tutorial-azure-active-directory-integration-with-cylanceprotect"></a>Oktatóanyag: Azure Active Directory-integráció az CylancePROTECT

Ebben az oktatóanyagban elsajátíthatja, hogyan CylancePROTECT integrálása az Azure Active Directory (Azure AD).

CylancePROTECT integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá CylancePROTECT Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett CylancePROTECT (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

CylancePROTECT az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy CylancePROTECT egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. CylancePROTECT hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-cylanceprotect-from-the-gallery"></a>CylancePROTECT hozzáadása a katalógusból
Az Azure AD integrálása a CylancePROTECT konfigurálásához hozzá kell CylancePROTECT a katalógusból a felügyelt SaaS-alkalmazások listájára.

**CylancePROTECT hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **CylancePROTECT**válassza **CylancePROTECT** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában CylancePROTECT](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés CylancePROTECT a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó CylancePROTECT mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó CylancePROTECT hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az CylancePROTECT tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy CylancePROTECT tesztfelhasználót](#create-a-cylanceprotect-test-user)**  – egy megfelelője a Britta Simon CylancePROTECT, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és CylancePROTECT alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés CylancePROTECT, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **CylancePROTECT** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_samlbase.png)

3. Az a **CylancePROTECT tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![CylancePROTECT tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím:
    
    | Régió | URL-érték |
    |----------|---------|
    | Ázsia és csendes-óceáni északkelet (APNE1)| ` https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Ázsia és csendes-óceáni délkeleti régiója (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Európa közép-India (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Észak-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Dél-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    
    b. Az a **válasz URL-cím** szövegmezőbe írja be az URL-cím:
    
    | Régió | URL-érték |
    |----------|---------|
    | Ázsia és csendes-óceáni északkelet (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Ázsia és csendes-óceáni délkeleti régiója (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Európa közép-India (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Észak-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Dél-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítvány-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/cylanceprotect-tutorial/tutorial_general_400.png)

6. Az a **CylancePROTECT konfigurációs** területén kattintson **konfigurálása CylancePROTECT** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![CylancePROTECT konfiguráció](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_configure.png) 

7. Az egyszeri bejelentkezés konfigurálása **CylancePROTECT** oldalon kell küldenie a letöltött **Certificate(Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** konzol rendszergazda számára. Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/cylanceprotect-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/cylanceprotect-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/cylanceprotect-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/cylanceprotect-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-cylanceprotect-test-user"></a>CylancePROTECT tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű CylancePROTECT hoz létre. A felhasználók hozzáadása az CylancePROTECT platformon konzol rendszergazdával együttműködve. Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozásra kattintva a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés CylancePROTECT Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel CylancePROTECT, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **CylancePROTECT**.

    ![Az alkalmazások listáját a CylancePROTECT hivatkozásra](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a CylancePROTECT csempére kattint, meg kell lekérése automatikusan bejelentkezett az CylancePROTECT alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cylanceprotect-tutorial/tutorial_general_01.png
[2]: ./media/cylanceprotect-tutorial/tutorial_general_02.png
[3]: ./media/cylanceprotect-tutorial/tutorial_general_03.png
[4]: ./media/cylanceprotect-tutorial/tutorial_general_04.png

[100]: ./media/cylanceprotect-tutorial/tutorial_general_100.png

[200]: ./media/cylanceprotect-tutorial/tutorial_general_200.png
[201]: ./media/cylanceprotect-tutorial/tutorial_general_201.png
[202]: ./media/cylanceprotect-tutorial/tutorial_general_202.png
[203]: ./media/cylanceprotect-tutorial/tutorial_general_203.png

