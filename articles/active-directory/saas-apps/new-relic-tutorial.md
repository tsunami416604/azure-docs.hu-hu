---
title: 'Oktatóanyag: A New relic használatával az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a New relic-bővítménnyel között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: a60360d8268d43d920ad878d372014fa6d9cc875
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176862"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Oktatóanyag: Az Azure Active Directory-integráció, a New relic használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a New relic-bővítménnyel az Azure Active Directory (Azure AD).

New relic-bővítménnyel integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a New relic-bővítménnyel, Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a New relic-bővítménnyel (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a New relic-bővítménnyel, a következőkre van szükség:

- Azure AD-előfizetés
- A New relic-bővítménnyel egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. New relic-bővítménnyel hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-new-relic-from-the-gallery"></a>New relic-bővítménnyel hozzáadása a katalógusból
Konfigurálhatja az Azure AD integrálása a New relic-bővítménnyel, hozzá kell New relic-bővítménnyel a galériából a felügyelt SaaS-alkalmazások listájára.

**New relic-bővítménnyel hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **New relic-bővítménnyel**, jelölje be **New relic-bővítménnyel** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a New relic-bővítménnyel](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és a New relic használatával, a teszt "Britta Simon" nevű felhasználó az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a New relic-bővítménnyel tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a New relic-bővítménnyel hivatkozás kapcsolatát kell létrehozni.

New relic-bővítménnyel, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a New relic segítségével tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[New relic-bővítménnyel tesztfelhasználó létrehozása](#create-a-new-relic-test-user)**  – van egy Britta Simon megfelelője a New relic-bővítménnyel, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a New Relic application az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a New relic-bővítménnyel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **New relic-bővítménnyel** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

1. Az a **új Relic tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új Relic tartomány és URL-címek egyszeri bejelentkezési adatait](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – ügyeljen arra, hogy cserélje le a saját New Relic-fiók azonosítóját.

    b. Az a **azonosító** szövegmezőbe írja be az értéket: `rpm.newrelic.com`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/new-relic-tutorial/tutorial_general_400.png)

1. Az a **új Relic konfiguráció** területén kattintson **New Relic konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Új Relic konfiguráció](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

1. Egy másik böngészőablakban, jelentkezzen be a **New relic-bővítménnyel** rendszergazdaként a vállalati webhely.

1. A felső menüben kattintson **fiókbeállításokat**.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797036.png "Fiókbeállítások")

1. Kattintson a **biztonság és hitelesítés** fülre, majd a **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés](./media/new-relic-tutorial/ic797037.png "egyszeri bejelentkezés")

1. Az SAML párbeszédpanel oldalon hajtsa végre az alábbi lépéseket:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. Kattintson a **fájl kiválasztása** a letöltött Azure Active Directory-tanúsítvány feltöltéséhez.

   b. Az a **távoli bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
   
   c. Az a **kijelentkezési URL-cím üzenetsorokra** szövegmezőbe, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.

   d. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/new-relic-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/new-relic-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/new-relic-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/new-relic-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-new-relic-test-user"></a>New relic-bővítménnyel tesztfelhasználó létrehozása

Ahhoz, hogy az Azure Active Directory-felhasználók jelentkezzen be a New relic-bővítménnyel, akkor ki kell építeni, a New relic-bővítménnyel. New relic-bővítménnyel, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot a New relic-bővítménnyel, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **New relic-bővítménnyel** rendszergazdaként a vállalati webhely.

1. A felső menüben kattintson **fiókbeállításokat**.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797040.png "Fiókbeállítások")

1. Az a **fiók** a bal oldali ablaktáblán kattintson a **összefoglalás**, és kattintson a **felhasználó hozzáadása**.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797041.png "Fiókbeállítások")

1. Az a **aktív felhasználók** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Aktív felhasználók](./media/new-relic-tutorial/ic797042.png "aktív felhasználók")
   
    a. Az a **E-mail** szövegmezőbe írja be egy érvényes kíván üzembe helyezni Azure Active Directory-felhasználó e-mail-címét.

    b. Mint **szerepkör** kiválasztása **felhasználói**.

    c. Kattintson a **adja hozzá a felhasználót**.

>[!NOTE]
>Bármely más New relic-bővítménnyel felhasználói fiók létrehozása eszközöket használhatja, vagy aad-ben a felhasználói fiókok kiépítését a New Relic által biztosított API-k.
> 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a New relic-bővítménnyel való hozzáférés biztosítása az Azure egyszeri bejelentkezés használatával.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a New relic-bővítménnyel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **New relic-bővítménnyel**.

    ![A New relic-bővítménnyel hivatkozásra az alkalmazások listáját](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a New relic-bővítménnyel csempére kattint, meg kell lekérése automatikusan bejelentkezett a New Relic-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/new-relic-tutorial/tutorial_general_01.png
[2]: ./media/new-relic-tutorial/tutorial_general_02.png
[3]: ./media/new-relic-tutorial/tutorial_general_03.png
[4]: ./media/new-relic-tutorial/tutorial_general_04.png

[100]: ./media/new-relic-tutorial/tutorial_general_100.png

[200]: ./media/new-relic-tutorial/tutorial_general_200.png
[201]: ./media/new-relic-tutorial/tutorial_general_201.png
[202]: ./media/new-relic-tutorial/tutorial_general_202.png
[203]: ./media/new-relic-tutorial/tutorial_general_203.png

