---
title: 'Oktatóanyag: A pagerduty segítségével az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a PagerDuty között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 703bc11ec7b0de0e8b0cfe3b99b7516afe8cac0b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810023"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Oktatóanyag: Az Azure Active Directory-integráció a pagerduty segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan PagerDuty integrálása az Azure Active Directory (Azure AD).

Az Azure AD integrálása a PagerDuty nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a PagerDuty Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a PagerDuty (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a pagerduty segítségével, a következőkre van szükség:

- Azure AD-előfizetés
- A PagerDuty egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. PagerDuty hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-pagerduty-from-the-gallery"></a>PagerDuty hozzáadása a katalógusból
Az Azure AD integrálása a PagerDuty konfigurálásához hozzá kell PagerDuty a galériából a felügyelt SaaS-alkalmazások listájára.

**PagerDuty hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **PagerDuty**válassza **PagerDuty** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a pagerduty segítségével a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a pagerduty szolgáltatásban a partner felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a pagerduty szolgáltatásban a kapcsolódó felhasználó hivatkozás kapcsolatának kell létrehozni.

A pagerduty szolgáltatásban, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés vizsgálata a pagerduty segítségével konfigurálni, kell hajtsa végre a következő építőelemeit:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy PagerDuty tesztfelhasználót](#create-a-pagerduty-test-user)**  - a-megfelelője a Britta Simon rendelkezik a pagerduty szolgáltatásban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a PagerDuty-kérelem az egyszeri bejelentkezés konfigurálása.

**Konfigurálás a pagerduty segítségével az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **PagerDuty** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

1. Az a **PagerDuty-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![PagerDuty-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenant-name>.pagerduty.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [PagerDuty-ügyfél-támogatási csapatának](https://www.pagerduty.com/support/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pagerduty-tutorial/tutorial_general_400.png)

1. Az a **PagerDuty konfigurációs** területén kattintson **konfigurálása PagerDuty** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![PagerDuty-konfiguráció](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Pagerduty vállalati hely rendszergazdaként.

1. A felső menüben kattintson **fiókbeállításokat**.

    ![Fiókbeállítások](./media/pagerduty-tutorial/ic778535.png "Fiókbeállítások")

1. Kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés](./media/pagerduty-tutorial/ic778536.png "egyszeri bejelentkezés")

1. Az a **engedélyezése egyszeri bejelentkezés (SSO)** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés engedélyezése](./media/pagerduty-tutorial/ic778537.png "egyszeri bejelentkezés engedélyezése")

    a. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben az Azure portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmező
  
    b. Az a **bejelentkezési URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
  
    c. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezéses URL-cím** Azure Portalról másolt.

    d. Válassza ki **engedélyezése felhasználónév/jelszó bejelentkezési**.

    e. Válassza ki **PONTOS szükséges hitelesítési környezeti összehasonlító** jelölőnégyzetet.

    f. Kattintson a **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/pagerduty-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/pagerduty-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/pagerduty-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-pagerduty-test-user"></a>PagerDuty tesztfelhasználó létrehozása

Ahhoz, hogy a PagerDuty jelentkezzen be az Azure AD-felhasználók, akkor ki kell építeni PagerDuty-be.  
PagerDuty, esetén kiépítése a manuális feladat.

>[!NOTE]
>Bármely más Pagerduty felhasználói fiók létrehozása eszközöket használhatja, vagy API-k által biztosított Pagerduty üzembe helyezni az Azure Active Directory felhasználói fiókokat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Pagerduty** bérlő.

1. A felső menüben kattintson **felhasználók**.

1. Kattintson a **felhasználók hozzáadása**.
   
    ![Felhasználók hozzáadása](./media/pagerduty-tutorial/ic778539.png "felhasználók hozzáadása")

1.  Az a **csapata meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![A csapat meghívása](./media/pagerduty-tutorial/ic778540.png "csapata meghívása")

    a. Írja be a **első és utolsó neve** például a felhasználó **Britta Simon**. 
   
    b. Adja meg **E-mail** felhasználó címe, például **brittasimon@contoso.com**.
   
    c. Kattintson a **Hozzáadás**, és kattintson a **szóló meghívók küldése**.
   
    >[!NOTE]
    >Minden felvett felhasználók Felkérjük egy PagerDuty-fiók létrehozásához.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés PagerDuty Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a PagerDuty, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **PagerDuty**.

    ![Az alkalmazások listáját a PagerDuty hivatkozásra](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintás után a PagerDuty csempét a hozzáférés Panelyou kell lekérése automatikusan bejelentkezett a PagerDuty-alkalmazásba.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
