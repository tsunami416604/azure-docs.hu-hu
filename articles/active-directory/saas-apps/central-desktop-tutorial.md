---
title: 'Oktatóanyag: Azure Active Directory-integráció központi desktoppal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a központi Desktop között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 82a6911c85dd1438aa8f60cb36194a2916bc91e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429046"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Oktatóanyag: Azure Active Directory-integráció központi desktoppal

Ebben az oktatóanyagban elsajátíthatja, hogyan központi asztali integrálása az Azure Active Directory (Azure AD).

Központi asztali integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a központi asztali Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérése megtörténik a központi asztali az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a központi Desktop, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egyetlen központi asztali bejelentkezési-a-kompatibilis előfizetéshez

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha még nem rendelkezik egy Azure AD-próba környezetet [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Központi asztali hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-central-desktop-from-the-gallery"></a>Központi asztali hozzáadása a katalógusból
Az Azure AD-be központi Desktop integráció konfigurálásához, hozzá kell központi asztali a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Központi asztali hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **központi asztali**. Válassza ki **központi asztali** az eredmények panelen, és válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában központi asztali](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés központi desktoppal egy "Britta Simon." nevű tesztelési felhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, akik a központi Desktop tartozó felhasználó, hogy egy felhasználó Azure AD-ben. Más szóval kell létesítenie egy Azure AD-felhasználót és a egy kapcsolódó felhasználó közötti kapcsolat a központi Desktopban.

A központi Desktop adjon **felhasználónév** azonos értéket **felhasználónév** az Azure ad-ben. Most hozott létre a kapcsolatot a két olyan felhasználó között.

Az Azure AD egyszeri bejelentkezés központi desktoppal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. [Hozzon létre egy központi asztali tesztfelhasználót](#create-a-central-desktop-test-user) van egy megfelelője a Britta Simon központi asztali, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és központi asztali alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a központi asztal, tegye a következőket:**

1. Az Azure Portalon az a **központi asztali** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Egyszeri bejelentkezéssel, engedélyeznie a **egyszeri bejelentkezési** párbeszédpanel a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. Az a **központi asztali tartomány és URL-címek** területén az alábbi lépéseket:

    ![Központi asztali tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<companyname>.centraldesktop.com`

    b. Az a **azonosító** mezőbe írja be egy URL-CÍMÉT a következő mintának:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Az a **válasz URL-cím** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval, válasz URL-cím és bejelentkezés URL-címe. Forduljon a [központi asztali ügyfél-támogatási csapatának](https://imeetcentral.com/contact-us) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **tanúsítvány**. Mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. Válassza ki a **Mentés** gombot.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. Az a **központi asztali konfigurálása** szakaszban jelölje be **központi asztali konfigurálása** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **rövid összefoglaló** szakaszban.

    ![Központi asztali konfigurálása](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. Jelentkezzen be a **központi asztali** bérlő.

1. Lépjen a **beállítások**. Válassza ki **speciális**, majd válassza ki **az egyszeri bejelentkezést**.

    ![A telepítő - speciális](./media/central-desktop-tutorial/ic769563.png "beállítása – speciális")

1. Az a **egyszeri bejelentkezést a beállítások** lapon, tegye a következőket:

    ![Egyszeri bejelentkezés beállításai](./media/central-desktop-tutorial/ic769564.png "egyszeri bejelentkezést a beállítások")
    
    a. Válassza ki **engedélyezése SAML v2 az egyszeri bejelentkezés**.
    
    b. Az a **egyszeri bejelentkezési URL-cím** mezőbe illessze be a **SAML Entitásazonosító** , az Azure Portalról másolt érték.
    
    c. Az a **Egyszeri bejelentkezési URL-cím** mezőbe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** , az Azure Portalról másolt érték.
    
    d. Az a **Egyszeri kijelentkezési URL-címe** mezőbe illessze be a **kijelentkezéses URL-cím** , az Azure Portalról másolt érték.

1. Az a **aláírás ellenőrzési módszert** területén az alábbi lépéseket:

    ![Aláírás-ellenőrzési módszert üzenet](./media/central-desktop-tutorial/ic769565.png "aláírás ellenőrzési módszert") egy. Válassza a **Tanúsítvány** elemet.
    
    b. Az a **SSO-tanúsítvány** listáról válassza ki **RSH SHA256**.
    
    c. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben. Ezután másolja a tanúsítvány, és illessze be azt a **SSO-tanúsítvány** mező.
        
    d. Válassza ki **jeleníti meg a hivatkozást a SAMLv2 bejelentkezési**.
    
    e. Válassza ki **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazás. Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és hozzáférhet a beágyazott dokumentáció a **konfigurációs** alul található szakaszában. További tudnivalók a beágyazott dokumentáció funkció [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/central-desktop-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/central-desktop-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/central-desktop-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-central-desktop-test-user"></a>Központi asztali tesztfelhasználó létrehozása

Az Azure AD-felhasználók tudnak bejelentkezni akkor ki kell építeni a központi Desktop alkalmazásban. Ez a szakasz ismerteti az Azure AD felhasználói fiók létrehozása a központi asztali.

> [!NOTE]
> Az Azure AD-felhasználói fiókok kiépítése, használhatja a központi asztali felhasználói fiók létrehozása eszközöket és központi asztali által biztosított API-k.

**Felhasználói fiókok központi asztali kiépítéséhez:**

1. Jelentkezzen be a központi asztali bérlő.

1. Lépjen a **személyek** > **belső tagokat**.

1. Válassza ki **belső tagok hozzáadása**.

    ![Személyek](./media/central-desktop-tutorial/ic781051.png "személyek")
    
1. Az a **E-mail címet az új tagok** mezőbe írja be az Azure AD-fiók kiépítése, és válassza ki a kívánt **tovább**.

    ![E-mail-címek az új tagok](./media/central-desktop-tutorial/ic781052.png "E-mail-címek az új tagok")

1. Válassza ki **belső hozzáadása tag(ok)**.

    ![Belső tag hozzáadása](./media/central-desktop-tutorial/ic781053.png "belső tag hozzáadása")
   
   >[!NOTE]
   >Adja hozzá a felhasználók kapnak egy e-mailt, amelyben a megerősítő hivatkozást a fiókjaikat aktiválásához.
   
### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze felhasználói Britta Simon központi asztali hozzáférés biztosításával őket Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése központi asztali, az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Nyissa meg a könyvtár nézet, és folytassa a **vállalati alkalmazások**.

1. Válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **központi asztali**.

    ![A központi asztali hivatkozásra az alkalmazások listáját](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

1. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A központi asztali csempe kiválasztásakor a hozzáférési panelen, automatikusan első bejelentkezett központi asztali alkalmazását.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

