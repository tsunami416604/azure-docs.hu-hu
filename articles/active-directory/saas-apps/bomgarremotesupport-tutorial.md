---
title: 'Oktatóanyag: Azure Active Directory-integráció Bomgar távoli támogatásával |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Bomgar távoli támogatást és Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: c59f4291726b24b7c96bb60d0497c1578a3e4b0f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048181"
---
# <a name="tutorial-azure-active-directory-integration-with-bomgar-remote-support"></a>Oktatóanyag: Azure Active Directory-integráció Bomgar távoli támogatásával

Ebben az oktatóanyagban elsajátíthatja, hogyan Bomgar távoli támogatási integrálása az Azure Active Directory (Azure AD).

Távoli támogatás Bomgar integrálása az Azure AD nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Bomgar távoli támogatás az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett távoli támogatási Bomgar (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Bomgar távoli támogatásával az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy távoli támogatási Bomgar egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Távoli Bomgar-támogatás hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-bomgar-remote-support-from-the-gallery"></a>Távoli Bomgar-támogatás hozzáadása a katalógusból
Az Azure AD integrálása a Bomgar távoli támogatás konfigurálásához szüksége Bomgar távoli támogatás hozzáadása a katalógusból a felügyelt SaaS-alkalmazások listájában.

**Távoli támogatás Bomgar hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Bomgar távoli támogatási**, jelölje be **Bomgar távoli támogatás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Távoli támogatás Bomgar a találatok listájában](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és a teszt "Britta Simon" nevű felhasználó Bomgar távoli támogatásával az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Bomgar távoli támogatás mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Bomgar távoli támogatás hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Bomgar távoli támogatásával tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy távoli támogatási Bomgar tesztfelhasználót](#create-a-bomgar-remote-support-test-user)**  – egy megfelelője a Britta Simon Bomgar távoli támogatás, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Bomgar távoli támogatási alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Bomgar távoli támogatásával, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Bomgar távoli támogatás** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_samlbase.png)

3. Az a **Bomgar távoli támogatási tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Bomgar távoli támogatási tartomány és URL-címek egyszeri bejelentkezési adatait](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.trafficmanager.net/saml`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.trafficmanager.net`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító (entityid). Kapcsolattartó [Bomgar távoli támogatja az ügyfél-támogatási csapatának](https://www.bomgar.com/docs/index.htm#support) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/bomgarremotesupport-tutorial/tutorial_general_400.png)

6. Az egyszeri bejelentkezés konfigurálása **Bomgar távoli támogatásához** oldalon kell küldenie a letöltött **metaadatainak XML** való [Bomgar távoli támogatja a támogatási csapat](https://www.bomgar.com/docs/index.htm#support). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/bomgarremotesupport-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bomgarremotesupport-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/bomgarremotesupport-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/bomgarremotesupport-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-bomgar-remote-support-test-user"></a>Távoli támogatás Bomgar tesztfelhasználó létrehozása

Ez a szakasz célja egy távoli támogatás Bomgar Britta Simon nevű felhasználó létrehozásához. Bomgar távoli támogatás támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó Bomgar távoli támogatás eléréséhez, ha még nem létezik tett kísérlet során jön létre.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Bomgar távoli támogatja a támogatási csapat](https://www.bomgar.com/docs/index.htm#support).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Bomgar távoli támogatás Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Távoli támogatás Bomgar Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Bomgar távoli támogatási**.

    ![Az alkalmazások listáját a Bomgar távoli támogatási hivatkozásra](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a távoli támogatási Bomgar csempére kattint, meg kell lekérése automatikusan bejelentkezett az Bomgar távoli támogatást alkalmazásához.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bomgarremotesupport-tutorial/tutorial_general_01.png
[2]: ./media/bomgarremotesupport-tutorial/tutorial_general_02.png
[3]: ./media/bomgarremotesupport-tutorial/tutorial_general_03.png
[4]: ./media/bomgarremotesupport-tutorial/tutorial_general_04.png

[100]: ./media/bomgarremotesupport-tutorial/tutorial_general_100.png

[200]: ./media/bomgarremotesupport-tutorial/tutorial_general_200.png
[201]: ./media/bomgarremotesupport-tutorial/tutorial_general_201.png
[202]: ./media/bomgarremotesupport-tutorial/tutorial_general_202.png
[203]: ./media/bomgarremotesupport-tutorial/tutorial_general_203.png

