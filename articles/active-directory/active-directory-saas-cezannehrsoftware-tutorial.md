---
title: "Oktatóanyag: Azure Active Directory-integráció Cezanne HR szoftverrel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Cezanne HR szoftverek között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Oktatóanyag: Azure Active Directory integrálása Cezanne HR szoftver

Ebben az oktatóanyagban elsajátíthatja Cezanne HR szoftver integrálása az Azure Active Directory (Azure AD).

Cezanne HR szoftver integrálása az Azure AD a következő előnyt biztosít. A következőket teheti:

- Szabályozhatja az Azure AD, aki hozzáfér Cezanne HR szoftver.
- Lehetővé teszi a felhasználók, az egyszeri bejelentkezés (SSO) és az Azure AD-fiókok Cezanne HR szoftver automatikusan bejelentkezhet.
- A fiók egyetlen központi helyen kezelheti: az Azure-portálon.

További információért, egy szolgáltatott szoftverként (SaaS) alkalmazás integráció az Azure ad-vel kapcsolatban lásd: [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Cezanne HR szoftverrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Cezanne HR szoftver előfizetés SSO engedélyezése

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez azt javasoljuk, hogy nem használ egy éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban a Azure AD SSO tesztkörnyezetben tesztelni. 

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

* A gyűjteményből Cezanne HR szoftver hozzáadása
* Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-cezanne-hr-software-from-the-gallery"></a>A gyűjteményből Cezanne HR szoftver hozzáadása
Az Azure AD integrálása a Cezanne HR szoftver konfigurálásához Cezanne HR szoftver hozzáadása a gyűjteményből a kezelt SaaS-alkalmazások listáját.

Cezanne HR hozzáadása a gyűjteményből, tegye a következőket:

1. Az a  **[Azure-portálon](https://portal.azure.com)**, a bal oldali panelen válassza ki a **Azure Active Directory** gombra. 

    ![Az "Azure Active Directory" gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![Az "Összes alkalmazás" hivatkozásra][2]
    
3. Egy új alkalmazás hozzáadása tetején a **összes alkalmazás** párbeszédpanelen jelölje ki **új alkalmazás**.

    ![Az "új alkalmazás" gomb][3]

4. Írja be a keresőmezőbe, **Cezanne HR szoftver**.

    ![A keresési mezőbe](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Az eredmények listájában válassza **Cezanne HR szoftver** , és válassza a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása Azure AD SSO "Britta Simon." nevű tesztfelhasználó alapján Cezanne HR szoftverrel

Az egyszeri bejelentkezés működjön az Azure AD tudnia kell, az Azure AD-felhasználó a Cezanne HR szoftvere. Ez azt jelenti a Cezanne HR szoftver egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létesítenie.

A hivatkozás kapcsolatot létesíteni, rendelje hozzá a Cezanne HR szoftver **felhasználónév** érték, mint az Azure AD **felhasználónév** érték.

Konfigurálása és tesztelése az Azure AD SSO Cezanne HR szoftvernek a használatával végezze el a következő építőelemeit.

### <a name="configure-azure-ad-sso"></a>Az Azure AD-egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri Bejelentkezést az Azure portálon, és egyszeri bejelentkezés konfigurálása a Cezanne HR alkalmazás a következő módon:

1. Az Azure portálon a a **Cezanne HR szoftver** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Az "Egyszeri bejelentkezés" parancs][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés** párbeszédpanelen jelölje ki a **mód** , **SAML-alapú bejelentkezés**.
 
    ![A "Mód" mezőt](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. A **Cezanne HR szoftver tartomány és az URL-címek**, tegye a következőket:

    ![A "Cezanne HR szoftver tartományi és URL-címek" szakasz](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely rendelkezik a következő szintaxist:`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. Az a **válasz URL-CÍMEN** mezőbe írjon be egy URL-címet, amely rendelkezik a következő szintaxist:`https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > Az előző értékei nem valódi. Frissítse azokat a tényleges válasz URL-cím és a bejelentkezési URL-CÍMÉT. Szerezze be az értékeket, lépjen kapcsolatba a [Cezanne HR szoftver ügyfél támogatási csoport](mailto:info@cezannehr.com).

4. A **SAML-aláíró tanúsítványa**, jelölje be **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt, a számítógépen.

    ![A "SAML aláíró tanúsítvány" szakasz](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![A "Mentés" gombra](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. A **Cezanne HR szoftverkonfigurációt**, jelölje be **Cezanne HR szoftver konfigurálása** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** és **SAML-alapú egyszeri bejelentkezési szolgáltatás** URL-CÍMÉT a **rövid összefoglaló** szakasz.

    ![A "Cezanne HR szoftver konfiguráció" szakasz](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be rendszergazdaként a Cezanne HR szoftver bérlő.

8. A bal oldali panelen válassza ki a **rendszerbeállítás**. Válassza ki **biztonsági beállítások** > **az egyszeri bejelentkezés konfigurációs**.

    ![Az "Egyszeri bejelentkezés konfiguráció" hivatkozásra](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. Az a **engedélyezése a felhasználóknak, hogy jelentkezzen be a következő egyszeri bejelentkezés (SSO) szolgáltatások** ablaktáblán válassza előbb a **SAML 2.0** jelölőnégyzetet, és válassza a **speciális konfiguráció** lehetőséget.

    ![Egyszeri bejelentkezés szolgáltatás beállításai](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Válassza ki **hozzáadhat új**.

    ![Az "Új hozzáadása" gombra](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. A **SAML 2.0 identitás-szolgáltatóktól**, tegye a következőket:

    ![A "SAML 2.0 identitás-szolgáltatóktól" szakasz](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Az a **megjelenített név** mezőben adja meg az identitás-szolgáltató neve.

    b. Az a **entitásazonosító** mezőbe illessze be a **SAML Entitásazonosító** másolt Azure-portálról. 

    c. Az a **SAML kötés** listáján jelölje ki **POST**.

    d. Az a **biztonsági jogkivonat szolgáltatásvégpont** mezőbe illessze be a **SAML-alapú egyszeri bejelentkezési szolgáltatás** Azure-portálról másolt URL-CÍMÉT. 
    
    e. Az a **felhasználói azonosító attribútum neve** adja meg a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Töltse fel a letöltött az Azure AD, válassza ki a **feltöltése** gombra.
    
    g. Kattintson az **OK** gombra. 

12. Kattintson a **Mentés** gombra.

    ![A "Mentés" gombra](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Állít be az alkalmazást, mert egy előző utasításait tömör verziója elolvashatja a [Azure-portálon](https://portal.azure.com). Miután hozzáadta az alkalmazásból a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** fülre. A beágyazott dokumentációjának majd hozzáférni a **konfigurációs** szakasz. 

A beágyazott dokumentáció szolgáltatással kapcsolatos további tudnivalókért lásd: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ebben a szakaszban az Azure portálon Britta Simon tesztfelhasználó hoz létre.

![A tesztfelhasználó számára Britta Simon][100]

Tesztfelhasználó létrehozása az Azure ad-ben, tegye a következőket:

1. Az a **Azure-portálon**, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az "Azure Active Directory" gomb](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Válassza ki azon felhasználók listájának megjelenítéséhez **felhasználók és csoportok** > **minden felhasználó**.
    
    ![A "Minden felhasználó" hivatkozásra](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    A **minden felhasználó** párbeszédpanel.

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![A "Hozzáadás" gombra](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:
 
    ![A "User" párbeszédpanel](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon **e-mail cím**.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értéket, amelyet hozták létre a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR szoftver tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Cezanne HR szoftver bejelentkezni, akkor ki kell építenie Cezanne HR szoftver. Cezanne HR szoftvert, ha egy kézi tevékenység.

A felhasználói fiók kiépítése a következő módon:

1.  Jelentkezzen be rendszergazdaként a Cezanne HR szoftver vállalati webhelyre.

2.  A bal oldali panelen válassza ki a **rendszerbeállítás** > **felhasználók kezelése** > **új felhasználó hozzáadása**.

    ![Az "Új felhasználó hozzáadása" hivatkozást](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "új felhasználó")

3.  A **személy adatai**, tegye a következőket:

    ![A "Személy részletei" szakasz](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "új felhasználó")
    
    a. Állítsa be **belső felhasználói** , **OFF**.
    
    b. Az a **Utónév** mezőbe írja be például a felhasználó utónevét, **Britta**.  
 
    c. Az a **Vezetéknév** mezőbe írja be például a felhasználó vezetékneve **Simon**.
    
    d. Az a **E-mail** mezőbe írja be a felhasználó e-mail címét, például Brittasimon@contoso.com.

4.  A **fiókadatok**, tegye a következőket:

    ![A "Fiók információk" című](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "új felhasználó")
    
    a. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címét, például Brittasimon@contoso.com.
    
    b. Az a **jelszó** mezőbe írja be a jelszót.
    
    c. Az a **biztonsági szerepkör** mezőben válassza **HR Professional**.
    
    d. Kattintson az **OK** gombra.

5. Az a **egyszeri bejelentkezés** lap a **SAML 2.0 azonosítók** szakaszban jelölje be **új hozzáadása**.

    ![Az "Új hozzáadása" gombra](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "felhasználó")

6. Az a **identitásszolgáltató** listán, válassza ki az identitásszolgáltató. Az a **felhasználói azonosító** mezőbe írja be az e-mail cím teszt felhasználó Britta Simon fiók.

    ![A "Identitásszolgáltató" és "Felhasználói azonosítója" mezőben](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "felhasználó")
    
7. Kattintson a **Mentés** gombra.

    ![A "Mentés" gombra](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "felhasználó")

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Azure SSO Cezanne HR szoftver való hozzáférés biztosítása által használandó Britta Simon tesztfelhasználó engedélyezi.

![Felhasználói hozzáférés tesztelése][200] 

1. Az Azure portálon az alkalmazások nézet megnyitásához, és keresse meg a könyvtár nézet. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![Az "Összes alkalmazás" hivatkozásra][201] 

2. Az alkalmazások listában válassza ki a **Cezanne HR szoftver**.

    ![Az "Alkalmazás" lista](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Válassza a **Hozzáadás** lehetőséget. Ezt a a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    !["Felhasználók és csoportok" hivatkozásra][203]

5. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

6. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **válasszon**.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési Panel segítségével tesztelheti.

Ha bejelöli a Cezanne HR szoftver csempe a hozzáférési panelen, amikor bejelentkezik automatikusan a Cezanne HR alkalmazás.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

