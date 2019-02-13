---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Cezanne HR szoftverrel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Cezanne HR szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb6fb443440ce9af26a3152f7dcc536c4e881cd9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165433"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Oktatóanyag: Az Azure Active Directory-integráció Cezanne HR szoftverrel

Ebben az oktatóanyagban elsajátíthatja, hogyan Cezanne HR szoftver integrálása az Azure Active Directory (Azure AD).

Cezanne HR szoftver integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Cezanne HR szoftverek az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Cezanne HR-szoftverek (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Cezanne HR-szoftverekkel rendelkező, a következőkre van szükség:

- Azure AD-előfizetés
- A HR-szoftver Cezanne egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Cezanne HR szoftver hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Cezanne HR szoftver hozzáadása a katalógusból
Az Azure AD-be a HR-szoftver Cezanne integráció konfigurálásához, kell Cezanne HR szoftver hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Cezanne HR szoftver hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Cezanne HR szoftver**, jelölje be **Cezanne HR szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Cezanne HR szoftver](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Cezanne HR szoftver úgynevezett "Britta Simon" tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Cezanne HR szoftverfrissítési mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Cezanne HR szoftveres hivatkozás kapcsolata kell létrehozni.

Cezanne HR szoftver, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Cezanne HR szoftverrel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **Hozzon létre egy Cezanne HR szoftver tesztfelhasználót** – egy megfelelője a Britta Simon Cezanne HR szoftver, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Cezanne HR alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Cezanne HR szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Cezanne HR szoftver** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

1. Az a **Cezanne HR szoftver tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Cezanne HR szoftver tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be az URL-cím: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és válasz URL-cím. Kapcsolattartó [Cezanne HR Szoftverügyfél támogatási csapatának](https://cezannehr.com/services/support/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

1. Az a **Cezanne HR szoftverfrissítési konfiguráció** területén kattintson **Cezanne HR szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak.

    ![Cezanne HR szoftverfrissítési konfiguráció](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

1. Görgessen le a **rövid összefoglaló** szakaszban. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím és a SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    ![Cezanne HR szoftverfrissítési konfiguráció](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

1. Egy másik böngészőablakban, a bejelentkezés a Cezanne HR szoftver bérlői rendszergazdaként.

1. A bal oldali navigációs panelén kattintson **rendszerbeállítás**. Lépjen a **biztonsági beállítások**. Majd keresse meg a **egyszeri bejelentkezési konfigurációjának**.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

1. Az a **engedélyezése a felhasználók számára, hogy jelentkezzen be a következő egyszeri bejelentkezéses (SSO) szolgáltatás** panel, ellenőrizze a **SAML 2.0** mezőbe, majd válassza ki a **speciális konfiguráció** lehetőséget.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

1. Kattintson a **új hozzáadása** gombra.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

1. Hajtsa végre a következő lépéseket **SAML 2.0 IDENTITÁSSZOLGÁLTATÓ** szakaszban.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Adja meg a nevét, az identitásszolgáltató a **megjelenítendő név**.

    b. Az a **entitásának azonosítója** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** az Azure Portalról másolt. 

    c. Módosítsa a **SAML kötés** a "POST".

    d. A a **biztonsági jogkivonat szolgáltatás végpontját** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt.

    e. Adja meg a felhasználói azonosító attribútum neve szövegmező `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Kattintson a **feltöltése** ikonra, töltse fel az Azure Portalról letöltött tanúsítványt.
    
    g. Kattintson az **OK** gombra. 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR szoftver tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Cezanne HR szoftver-ba való bejelentkezéshez, akkor ki kell építeni Cezanne HR szoftver. Cezanne HR szoftverek esetén a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1.  Jelentkezzen be a Cezanne HR szoftver vállalati hely rendszergazdaként.

1.  A bal oldali navigációs panelén kattintson **rendszerbeállítás**. Lépjen a **felhasználók kezelése**. Majd keresse meg a **új felhasználó hozzáadása**.

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "új felhasználó")

1.  A **személy adatai** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "új felhasználó")
    
    a. Állítsa be **belső felhasználói** , ki.
    
    b. Az a **Utónév** szövegmező, például a felhasználó utónevét típusa **Britta**.  
 
    c. Az a **Vezetéknév** szövegmezőbe, például a felhasználó vezetéknevét típusa **Simon**.
    
    d. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

1.  A **fiókadatok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "új felhasználó")
    
    a. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.
    
    b. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.
    
    c. Válassza ki **HR Professional** , **biztonsági szerepkör**.
    
    d. Kattintson az **OK** gombra.

1. Navigáljon a **egyszeri bejelentkezés** lapra, és válassza **új hozzáadása** a a **SAML 2.0-s azonosítók** területen.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "felhasználó")

1. Válassza ki az identitásszolgáltatót a **identitásszolgáltató** és a szövegmezőbe, **felhasználói azonosító**, adja meg a Britta Simon fiók e-mail-címe.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "felhasználó")
    
1. Kattintson a **mentése** gombra.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "felhasználó")

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cezanne HR szoftver Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Cezanne HR szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Cezanne HR szoftver**.

    ![A Cezanne HR szoftverhivatkozás alkalmazásainak listájában](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cezanne HR szoftver csempére kattint, meg kell lekérése automatikusan bejelentkezett az Cezanne HR szoftver alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

