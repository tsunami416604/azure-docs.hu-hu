---
title: 'Oktatóanyag: Azure Active Directory-integráció az egyértelmű áttekintése |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés egyértelmű felülvizsgálati és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: 604a557a91176c08a361ffd058adda63f53b30fc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433020"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Oktatóanyag: Azure Active Directory-integráció az egyértelmű áttekintése

Ebben az oktatóanyagban elsajátíthatja, hogyan egyértelmű felülvizsgálati integrálása az Azure Active Directory (Azure AD).

Egyértelmű felülvizsgálati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá egyértelmű tekintse át az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett egyértelmű felülvizsgálatra (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Egyértelmű tekintse át az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy világos tekintse át az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Egyértelmű felülvizsgálat hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-clear-review-from-the-gallery"></a>Egyértelmű felülvizsgálat hozzáadása a katalógusból
Egyértelmű tekintse át az Azure ad-ben integráció konfigurálásához, hozzá kell egyértelmű tekintse át a galériából a felügyelt SaaS-alkalmazások listájára.

**Egyértelmű felülvizsgálat hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **egyértelmű felülvizsgálati**, jelölje be **egyértelmű tekintse át** eredmény panelen kattintson a **hozzáadása** gombra kattintva vegye fel az alkalmazást.

    ![Felülvizsgálat törlése az eredmények listájában](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés egyértelmű tekintse át a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó törlése tekintse át a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó törlése tekintse át a hivatkozás kapcsolata kell létrehozni.

Egyértelmű felülvizsgálat, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés egyértelmű tekintse át a tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy egyértelmű felülvizsgálati tesztfelhasználót](#create-a-clear-review-test-user)**  – egy megfelelője a Britta Simon egyértelmű tekintse át, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyértelmű felülvizsgálat alkalmazása az egyszeri bejelentkezés konfigurálása.

**Egyértelmű tekintse át az Azure AD egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **egyértelmű felülvizsgálati** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

1. Az a **egyértelmű felülvizsgálati tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **identitásszolgáltató által kezdeményezett** mód:

    ![Világos felülvizsgálati tartomány és URL-címek egyszeri bejelentkezési adatait](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<customer name>.clearreview.com/sso/metadata/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<customer name>.clearreview.com/sso/acs/`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Világos felülvizsgálati tartomány és URL-címek egyszeri bejelentkezési adatait](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-azonosító és válasz URL-cím. Kapcsolattartó [egyértelmű támogatási csapatától](https://clearreview.com/contact/) beolvasni ezeket az értékeket.

1. A Névazonosító jogcímet egyértelmű felülvizsgálat alkalmazása számítson az egyedi felhasználói azonosító értékét. Hozzá kell rendelnie a felhasználói azonosító értéket **user.mail**.

    ![Az attribútum szakasz](./media/clearreview-tutorial/attribute.png)


1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_general_400.png)

1. Az a **törölje a konfiguráció áttekintése** területén kattintson **egyértelmű felülvizsgálati konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Törölje a konfiguráció áttekintése](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **egyértelmű felülvizsgálati** oldalán, nyissa meg a **egyértelmű tekintse át** portál, rendszergazdai hitelesítő adataival.

1. Válassza ki **rendszergazdai** a bal oldali navigációs sávon.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

1. Válassza ki **módosítása** az oldal alján.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

1. Hajtsa végre a következő lépéseket **egyszeri bejelentkezési beállításainak** lap

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Az a **kiállítójának URL-címe** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.

    b. Az a **SAML-végpont** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.    

    c. A a **SLO-végpont** szövegmezőjébe illessze be az értéket, **bejelentkezési szolgáltatás URL-** Azure Portalról másolt. 

    d. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, és illessze be a tartalmat a **X.509-tanúsítvány** szövegmezőbe.   

1. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/clearreview-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/clearreview-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/clearreview-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/clearreview-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-clear-review-test-user"></a>Egyértelmű felülvizsgálati tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű egyértelmű tekintse át a felhasználó hoz létre. Együttműködve [egyértelmű támogatási csapatától](https://clearreview.com/contact/) a felhasználók hozzáadása az egyértelmű felülvizsgálati platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával egyértelmű tekintse át az Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel egyértelmű áttekintése, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **egyértelmű felülvizsgálati**.

    ![A tiszta felülvizsgálati hivatkozással alkalmazásainak listájában](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a tiszta áttekintése csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett a tiszta felülvizsgálati alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png
