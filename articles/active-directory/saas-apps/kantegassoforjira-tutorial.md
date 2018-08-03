---
title: 'Oktatóanyag: Azure Active Directory-integráció a JIRA Kantega SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a JIRA Kantega SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: b498c0406c70da253ae79d4fbb98d4af1d954175
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440694"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Oktatóanyag: Azure Active Directory-integráció a JIRA Kantega SSO-val

Ebben az oktatóanyagban elsajátíthatja a JIRA Kantega SSO integrálása az Azure Active Directory (Azure AD).

A JIRA Kantega SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Kantega SSO JIRA az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Kantega SSO a JIRA (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a JIRA Kantega SSO-val, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Kantega egyszeri bejelentkezés az JIRA egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A JIRA Kantega SSO hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>A JIRA Kantega SSO hozzáadása a katalógusból
Az Azure AD-be a JIRA Kantega SSO-integráció konfigurálásához, hozzá kell Kantega SSO a JIRA a galériából a felügyelt SaaS-alkalmazások listájára.

**A JIRA Kantega SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Kantega SSO a JIRA**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_search.png)

1. Az eredmények panelen válassza ki a **Kantega SSO a JIRA**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a JIRA a teszt "Britta Simon" nevű felhasználó Kantega SSO-val.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Kantega SSO a JIRA mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Kantega SSO a JIRA hivatkozás kapcsolatát kell létrehozni.

JIRA Kantega egyszeri Bejelentkezést, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a JIRA Kantega SSO-val tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy Kantega egyszeri bejelentkezés az JIRA tesztfelhasználó létrehozása](#creating-a-kantega-sso-for-jira-test-user)**  – Kantega egyszeri Bejelentkezést a felhasználó Azure ad-ben reprezentációja kapcsolódó JIRA-megfelelője a Britta Simon van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Kantega SSO JIRA-alkalmazáshoz az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a JIRA Kantega SSO-val, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Kantega SSO a JIRA** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_samlbase.png)

1. A **Identitásszolgáltató** módban indul a **Kantega SSO JIRA tartomány és URL-címek** szakasz hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. A **SP** kezdeményezett mód, a jelölőnégyzet **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/tutorial_general_400.png)
    
1. Egy másik böngészőablakban jelentkezzen be a JIRA helyszíni kiszolgálón rendszergazdaként.

1. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon1.png)

1. A bővítmények lapon szakasz alatt kattintson **új bővítmények keresése**. Keresés **Kantega SSO a JIRA (SAML & Kerberos)** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon2.png)

1. A beépülő modul telepítése elindul.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon3.png)

1. A telepítés befejezése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon33.png)

1.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Új beépülő modul részen **INTEGRÁCIÓK**. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon35.png)

1. Az a **SAML** szakaszban. Válassza ki **Azure Active Directory (Azure AD)** származó a **Hozzáadás identitásszolgáltató** legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon4.png)

1. Válassza ki az előfizetés szintjén, **alapszintű**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon5.png)       

1. Az a **alkalmazás tulajdonságai** szakaszban, hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon6.png)

    a. Másolás a **Alkalmazásazonosító URI-t** értékét, és használja azt **azonosítóját, a válasz URL-cím és a bejelentkezési URL-** a a **Kantega SSO JIRA tartomány és URL-címek** szakaszban az Azure Portalon.

    b. Kattintson a **Tovább** gombra.

1. Az a **metaadatok importálása** szakaszban, hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon7.png)

    a. Válassza ki **metaadatait tartalmazó fájl a számítógépen**, és az Azure Portalról letöltött feltöltési metaadatait tartalmazó fájl.

    b. Kattintson a **Tovább** gombra.

1. Az a **nevét és az egyszeri bejelentkezés helyét** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon8.png)
    
    a. Adja hozzá az identitásszolgáltató nevét **identitásszolgáltató neve** (például: az Azure AD) szövegmezőbe.

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon9.png)

1. Az a **JIRA felhasználói fiókok** szakaszban, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon10.png)

    a. Válassza ki **felhasználók létrehozása a JIRA a belső könyvtár szükség esetén** , és adja meg a felhasználók számára a megfelelő nevet a csoport (lehet több nem. a vesszővel elválasztott csoportok).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.    

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon11.png)

1. Az a **ismert tartományok az Azure ad** szakaszban, hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/addon12.png)

    a. Válassza ki **tartományok ismert** az oldal bal oldali panelen.

    b. Adja meg a tartomány nevét a **tartományok ismert** szövegmezőbe.

    c. Kattintson a **Save** (Mentés) gombra. 

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kantegassoforjira-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kantegassoforjira-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kantegassoforjira-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kantegassoforjira-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-kantega-sso-for-jira-test-user"></a>Egy Kantega egyszeri bejelentkezés az JIRA tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jira Szolgáltatásba való bejelentkezéshez, akkor ki kell építeni JIRA be. JIRA Kantega egyszeri Bejelentkezést a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA helyileg működtetett kiszolgálón.

1. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user1.png) 

1. A **felhasználókezelés** szakasz lapra, majd **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user2.png) 

1. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user3.png) 

    a. Az a **E-mail-cím** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó például Britta Simon teljes neve.

    c. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása**.   

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés a JIRA Kantega SSO Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a JIRA Kantega SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Kantega SSO a JIRA**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a Kantega SSO JIRA csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett a Kantega egyszeri Bejelentkezéssel való JIRA-alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kantegassoforjira-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforjira-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforjira-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforjira-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforjira-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforjira-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforjira-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforjira-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforjira-tutorial/tutorial_general_203.png

