---
title: 'Oktatóanyag: Azure Active Directory-integráció az Cisco Webex |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco Webex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 4dcf487afdad899853c97d3d2a1493a6123b3bab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440713"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Oktatóanyag: Cisco Webex-Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Cisco Webex integrálása az Azure Active Directory (Azure AD).

Cisco Webex integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Cisco Webex Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Cisco Webex az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Webex, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egyetlen Cisco Webex sign-a-kompatibilis előfizetéshez

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Cisco Webex hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a katalógusból
Az Azure AD integrálása a Cisco Webex konfigurálásához hozzá kell Cisco Webex a galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco Webex hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Cisco Webex**. 

1. Válassza ki **Cisco Webex** az eredmények panelen. Válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Cisco Webex a találatok listájában](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Cisco Webex egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, akik a Cisco Webex tartozó felhasználót, hogy egy felhasználó Azure AD-ben. Más szóval kell létesítenie egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat, a Cisco Webex.

Cisco Webex, adjon az érték **felhasználónév** azonos értéket **felhasználónév** az Azure ad-ben. Most hozott létre a kapcsolatot a két olyan felhasználó között. 

Az Azure AD egyszeri bejelentkezés a Cisco Webex tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. [Hozzon létre egy Cisco Webex tesztfelhasználót](#create-a-cisco-webex-test-user) van egy megfelelője a Britta Simon Cisco Webex, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Cisco Webex alkalmazásban egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Cisco Webex, tegye a következőket:**

1. Az Azure Portalon az a **Cisco Webex** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Egyszeri bejelentkezéssel, engedélyeznie a **egyszeri bejelentkezési** párbeszédpanel a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

1. Az a **Cisco Webex tartomány és URL-címek** területén az alábbi lépéseket:

    ![Cisco Webex tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<subdomain>.webex.com`

    b. Az a **azonosító** mezőbe írja be az URL-cím `http://www.webex.com`.

    c. Az a **válasz URL-cím** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezeket az értékeket frissítse a tényleges válasz URL-cím és bejelentkezés URL-címe. Kapcsolattartó [Cisco Webex ügyfél-támogatási csapatának](https://www.webex.co.in/support/support-overview.html) beolvasni ezeket az értékeket. 

1. A a **SAML-aláíró tanúsítvány** szakaszban válassza **metaadatainak XML**, majd mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

1. Kattintson a **Mentés** gombra.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
1. Az a **Cisco Webex konfigurációs** szakaszban jelölje be **konfigurálása Cisco Webex** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** a a **rövid összefoglaló** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Cisco Webex vállalati hely rendszergazdaként.

1. A felső menüben válassza ki a **helyfelügyelet**.

    ![Hely felügyelete](./media/cisco-webex-tutorial/ic777621.png "hely felügyelete")

1. Az a **kezelése hely** szakaszban jelölje be **egyszeri bejelentkezési konfiguráció**.
   
    ![Egyszeri bejelentkezés konfigurációja](./media/cisco-webex-tutorial/ic777622.png "egyszeri bejelentkezés konfigurálása")

1. Az a **összevont webes egyszeri bejelentkezés konfigurálása** területén az alábbi lépéseket:
   
    ![Összevont egyszeri bejelentkezési konfiguráció](./media/cisco-webex-tutorial/ic777623.png "összevont egyszeri bejelentkezés konfigurálása")  

    a. Az a **Federation protokollal** listáról válassza ki **SAML 2.0**.

    b. A **egyszeri bejelentkezési profil**válassza **SP által kezdeményezett**.

    c. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, és másolja a tartalmat.

    d. Válassza ki **SAML-metaadatok importálása**, majd illessze be a tanúsítvány másolt tartalmát.

    e. Az a **(IdP-azonosító) SAML-kibocsátó** mezőbe illessze be az értékét a **SAML Entitásazonosító** , az Azure Portalról másolt.

    f. Az a **ügyfél egyszeri bejelentkezési szolgáltatás bejelentkezési URL-cím** mezőbe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    g. Az a **nameid-formátumához** listáról válassza ki **E-mail-cím**.

    h. Az a **AuthnContextClassRef** mezőbe írja be **urn: oasis: nevek: tc: SAML:2.0:ac:classes:Password**.

    i. Az a **ügyfél egyszeri bejelentkezési szolgáltatás kijelentkezési URL-címe** mezőbe illessze be **kijelentkezéses URL-cím**, az Azure Portalról másolt.
   
    j. Válassza ki **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazás. Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és hozzáférhet a beágyazott dokumentáció a **konfigurációs** alul található szakaszában. További tudnivalók a beágyazott dokumentáció funkció [Azure ad-ben a beágyazott dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/cisco-webex-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/cisco-webex-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/cisco-webex-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Cisco Webex, akkor ki kell építeni a Cisco Webex. Cisco Webex, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Cisco Webex** bérlő.

1. Lépjen a **felhasználók kezelése** > **felhasználó hozzáadása**.
   
    ![Felhasználók hozzáadása](./media/cisco-webex-tutorial/ic777625.png "felhasználók hozzáadása")

1. Az a **felhasználó hozzáadása** területén az alábbi lépéseket:
   
    ![Felhasználó hozzáadása](./media/cisco-webex-tutorial/ic777626.png "felhasználó hozzáadása")   

    a. A **fióktípus**válassza **gazdagép**.

    b. Az a **Utónév** mezőbe írja be a felhasználó utónevét (ebben az esetben **Britta**).

    c. Az a **Vezetéknév** mezőbe írja be a felhasználó vezetékneve (ebben az esetben **Simon**).

    d. Az a **felhasználónév** mezőbe írja be az e-mail a felhasználó (ebben az esetben **Brittasimon@contoso.com**).

    e. Az a **E-mail** mezőbe írja be a felhasználó e-mail-címét (ebben az esetben **Brittasimon@contoso.com**).

    f. Az a **jelszó** mezőbe írja be a felhasználó jelszavát.

    g. Az a **megerősítése** jelszó mezőbe írja be újra a jelszót.

    h. Válassza a **Hozzáadás** lehetőséget.

>[!NOTE]
>Cisco Webex felhasználói fiók létrehozása eszközöket és az Azure AD-felhasználói fiókok kiépítése Cisco Webex által biztosított API-k is használhatja. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze a felhasználó által a Cisco Webex hozzáférést adna Azure egyszeri bejelentkezés használatára Britta Simon.

![A felhasználói szerepkör hozzárendelése][200] 

**Cisco Webex Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Következő, nyissa meg a könyvtár nézetre, majd a **vállalati alkalmazások**.  

1. Válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Cisco Webex**.

    ![Az alkalmazások listáját a Cisco Webex hivatkozás](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

1. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

1. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra.

1. Válassza ki a **hozzárendelése** gombra a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

A Cisco Webex csempe kiválasztásakor a hozzáférési panelen, automatikusan első bejelentkezett a Cisco Webex-alkalmazásban.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

