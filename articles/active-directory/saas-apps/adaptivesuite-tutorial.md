---
title: 'Oktatóanyag: Azure Active Directory-integráció az adaptív Insights |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az adaptív Insights között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 74f38ef3043cfa0e6df40c264b72cc5eae38544e
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430289"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Oktatóanyag: Azure Active Directory-integráció az adaptív insights segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan adaptív Insights integrálása az Azure Active Directory (Azure AD).

Az adaptív Insights integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az adaptív Insights az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett adaptív insights (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az adaptív Insights, a következőkre van szükség:

- Azure AD-előfizetés
- Az adaptív Insights egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az adaptív Insights hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-adaptive-insights-from-the-gallery"></a>Az adaptív Insights hozzáadása a katalógusból
Az adaptív elemzéseket kaphat az Azure AD-integráció konfigurálása, meg kell adaptív Insights hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Az adaptív Insights hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **adaptív Insights**válassza **adaptív Insights** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az adaptív Insights "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó adaptív Insights mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó, az adaptív Insights hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az adaptív Insights tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy adaptív Insights tesztfelhasználót](#create-an-adaptive-insights-test-user)**  – egy megfelelője a Britta Simon van adaptív Insights, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az adaptív Insights alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az adaptív Insights, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **adaptív Insights** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** intiated mód:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Az adaptív Insights kaphat azonosító (entityid) és a válasz URL-cím értékek **SAML egyszeri bejelentkezési beállításainak** lapot.
 
5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** , és mentse a számítógépre.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Az a **adaptív Insights beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    Vegye figyelembe, hogy az URL-cím lehet, hogy tegyük fel, hogy a következő:

    a. Bejelentkezési URL

    b. Az Azure Ad-azonosító

    c. Kijelentkezési URL

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. Egy másik böngészőablakban jelentkezzen be az adaptív Insights vállalati hely rendszergazdaként.

8. Lépjen a **rendszergazdai**.

    ![Rendszergazdai](./media/adaptivesuite-tutorial/IC805644.png "rendszergazda")

9. Az a **felhasználók és szerepkörök** területén kattintson **SAML egyszeri bejelentkezési beállításainak kezelése**.

    ![SAML egyszeri bejelentkezési beállításainak kezelése](./media/adaptivesuite-tutorial/IC805645.png "SAML egyszeri bejelentkezési beállításainak kezelése")

10. Az a **SAML egyszeri bejelentkezési beállításainak** lapon, a következő lépésekkel:

    ![SAML egyszeri bejelentkezési beállításainak](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO-beállítások")

    a. Az a **identitásszolgáltató neve** szövegmezőbe írja be a konfiguráció nevét.

    b. Illessze be a **Azure Ad-azonosító** az Azure Portalról másolt érték a **identitásszolgáltató Entitásazonosító** szövegmezőbe.

    c. Illessze be a **bejelentkezési URL-cím** az Azure Portalról másolt érték a **identitásszolgáltató egyszeri bejelentkezési URL-cím** szövegmezőbe.

    d. Illessze be a **kijelentkezési URL-címe** az Azure Portalról másolt érték a **egyéni kijelentkezési URL-címe** szövegmezőbe.

    e. A letöltött tanúsítvány feltöltéséhez kattintson **fájl kiválasztása**.

    f. Válassza ki a következőket:

    * **SAML-felhasználói azonosító**válassza **felhasználó adaptív Insights nevét**.

    * **SAML-felhasználói azonosító hely**válassza **NameID-tulajdonos felhasználói azonosító**.

    * **Az SAML NameID formátum**válassza **E-mail-cím**.

    * **SAML engedélyezése**válassza **SAML SSO engedélyezése és a közvetlen adaptív Insights bejelentkezési**.

    g. Másolás **adaptív Insights egyszeri bejelentkezési URL-címe** , és illessze be a **azonosító (entityid)** és **válasz URL-cím** az eszköztippek a **adaptív Insights tartomány és URL-címek** szakaszban az Azure Portalon.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-adaptive-insights-test-user"></a>Az adaptív Insights tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be az adaptív Insights az Azure AD-felhasználók, akkor ki kell építeni az adaptív elemzésekké. Adaptív Insights esetén kiépítése a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:** 

1. Jelentkezzen be a **adaptív Insights** rendszergazdaként a vállalati webhely.
2. Lépjen a **rendszergazdai**.

   ![Rendszergazdai](./media/adaptivesuite-tutorial/IC805644.png "rendszergazda")

3. Az a **felhasználók és szerepkörök** területén kattintson **felhasználó hozzáadása**.

   ![Felhasználó hozzáadása](./media/adaptivesuite-tutorial/IC805648.png "felhasználó hozzáadása")
   
4. Az a **új felhasználó** szakaszban, hajtsa végre az alábbi lépéseket:

   ![Küldje el](./media/adaptivesuite-tutorial/IC805649.png "beküldése")

   a. Írja be a **neve**, **bejelentkezési**, **E-mail**, **jelszó** egy érvényes Azure Active Directory-felhasználó létrehozásához a kapcsolódó kívánt a szöveges mezőkben.

   b. Válassza ki a **szerepkör**.

   c. Kattintson a **elküldése**.

>[!NOTE]
>Bármely más adaptív Insights felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését adaptív Insights által biztosított API-k.
>

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított adaptív Insights hozzáférés Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **adaptív Insights**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a adaptív Insights csempére kattint, akkor kell lekérése automatikusan bejelentkezett az adaptív Insights alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
