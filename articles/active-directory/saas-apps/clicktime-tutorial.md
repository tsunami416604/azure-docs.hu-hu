---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ClickTime |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ClickTime között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: f1c7c3cf850ed48412c8a232e364f927248ed9bf
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811120"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ClickTime

Ebben az oktatóanyagban elsajátíthatja, hogyan ClickTime integrálása az Azure Active Directory (Azure AD).

ClickTime integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá ClickTime Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ClickTime (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ClickTime az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy ClickTime egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ClickTime hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-clicktime-from-the-gallery"></a>ClickTime hozzáadása a katalógusból
Az Azure AD integrálása a ClickTime konfigurálásához hozzá kell ClickTime a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ClickTime hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **ClickTime**válassza **ClickTime** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában ClickTime](./media/clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés ClickTime a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ClickTime mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ClickTime hivatkozás kapcsolata kell létrehozni.

ClickTime, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az ClickTime tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy ClickTime tesztfelhasználót](#create-a-clicktime-test-user)**  – egy megfelelője a Britta Simon ClickTime, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és ClickTime alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ClickTime, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ClickTime** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/clicktime-tutorial/tutorial_clicktime_samlbase.png)

1. Az a **ClickTime tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ClickTime tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/clicktime-tutorial/tutorial_clicktime_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-cím: `https://app.clicktime.com/sp/`
    
    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minták használatával URL-címe: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/clicktime-tutorial/tutorial_clicktime_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/clicktime-tutorial/tutorial_general_400.png)

1. Az a **ClickTime konfigurációs** területén kattintson **konfigurálása ClickTime** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![ClickTime configuration](./media/clicktime-tutorial/tutorial_clicktime_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a ClickTime vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **beállítások**, és kattintson a **biztonsági beállítások**.

1. Az a **egyszeri bejelentkezési beállítások** konfigurációs szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Biztonsági beállítások](./media/clicktime-tutorial/tic777280.png "biztonsági beállítások")
   
    a.  Válassza ki **engedélyezése** jelentkezzen be egyszeri bejelentkezést (SSO) a használatával **Azure ad-ben**.
   
    b. Az a **identitás szolgáltatói végpont** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
   
    c.  Nyissa meg a **base-64 kódolású tanúsítvány** az Azure Portalról letöltött **Jegyzettömb**, és másolja a tartalmat, majd illessze be azt a **X.509-tanúsítvány** szövegmezőbe.
   
    d.  Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/clicktime-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/clicktime-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.
 
    ![A Hozzáadás gombra.](./media/clicktime-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![A felhasználó párbeszédpanel](./media/clicktime-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-clicktime-test-user"></a>ClickTime tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ClickTime szolgáltatásba való bejelentkezéshez, akkor ki kell építeni ClickTime be.  
ClickTime, esetén kiépítése a manuális feladat.

> [!NOTE]
> Eszközt is használhat bármilyen más ClickTime felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése ClickTime által biztosított API-k.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**
1. Jelentkezzen be a **ClickTime** bérlő.
1. A felső eszköztáron kattintson **vállalati**, és kattintson a **személyek**.
   
    ![Személyek](./media/clicktime-tutorial/tic777282.png "személyek")
1. Kattintson a **adja hozzá Person**.
   
    ![Adja hozzá Person](./media/clicktime-tutorial/tic777283.png "személy hozzáadása")
1. Az új személy szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Személyek](./media/clicktime-tutorial/tic777284.png "személyek")
   
    a.  Az a **teljes név** szövegmező, például a felhasználó teljes neve típusa **Britta Simon**. 
  
    b.  Az a **e-mail-cím** szövegmezőbe írja be az e-mailt, felhasználó, például **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Ha szeretné, az új személy objektum további tulajdonságokat is beállíthatja.
   
    c.  Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ClickTime Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel ClickTime, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **ClickTime**.

    ![Az alkalmazások listáját ClickTimne hivatkozásra](./media/clicktime-tutorial/tutorial_clicktime_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ClickTime csempére kattint, meg kell lekérése automatikusan bejelentkezett az ClickTime alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clicktime-tutorial/tutorial_general_01.png
[2]: ./media/clicktime-tutorial/tutorial_general_02.png
[3]: ./media/clicktime-tutorial/tutorial_general_03.png
[4]: ./media/clicktime-tutorial/tutorial_general_04.png

[100]: ./media/clicktime-tutorial/tutorial_general_100.png

[200]: ./media/clicktime-tutorial/tutorial_general_200.png
[201]: ./media/clicktime-tutorial/tutorial_general_201.png
[202]: ./media/clicktime-tutorial/tutorial_general_202.png
[203]: ./media/clicktime-tutorial/tutorial_general_203.png

