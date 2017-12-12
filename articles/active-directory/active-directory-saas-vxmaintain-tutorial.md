---
title: "Oktatóanyag: Azure Active Directory integrálása vxMaintain |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és vxMaintain között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: a987247d2c8a76161f9c8a5a027e34b3a8e25b30
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-vxmaintain"></a>Oktatóanyag: Azure Active Directory integrálása vxMaintain

Ebben az oktatóanyagban elsajátíthatja vxMaintain integrálása az Azure Active Directory (Azure AD).

Ez az integráció kínál számos fontos előnnyel jár. A következőket teheti:

- Ellenőrzés vxMaintain hozzáféréssel rendelkező Azure AD-ben.
- Engedélyezze a felhasználóknak automatikusan jelentkezzen be az egyszeri bejelentkezés (SSO) vxMaintain az Azure AD-fiókok használatával.
- A fiók egyetlen központi helyen kezelheti: az Azure-portálon.

Az Azure AD SaaS alkalmazásintegráció kapcsolatos további információkért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs vxMaintain, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy vxMaintain előfizetés SSO engedélyezése

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, azt javasoljuk, hogy nem használ egy éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. 

Ez az oktatóanyag ismerteti a forgatókönyv két fő építőelemeket áll:

* A gyűjteményből vxMaintain hozzáadása
* És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-vxmaintain-from-the-gallery"></a>Adja hozzá a vxMaintain a gyűjteményből
Az Azure AD integrálása vxMaintain konfigurálásához kell hozzáadnia vxMaintain a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

A gyűjteményből vxMaintain hozzáadásához tegye a következőket:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** gombra. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![A "Vállalati alkalmazások" ablak][2]
    
3. Hozzáadhat egy alkalmazást, az a **összes alkalmazás** párbeszédpanelen jelölje ki **új alkalmazás**.

    ![Az "új alkalmazás" gomb][3]

4. Írja be a keresőmezőbe, **vxMaintain**.

    ![Az "Egyszeri bejelentkezés mód" legördülő lista](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Az eredmények listájában válassza **vxMaintain**, majd válassza ki **Hozzáadás**.

    ![A vxMaintain hivatkozás](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban konfigurálása és tesztelése az Azure AD SSO vxMaintain "Britta Simon." nevű tesztfelhasználó alapján használatával

Az egyszeri bejelentkezés működjön az Azure AD tudnia kell, a vxMaintain megfelelője az Azure AD-felhasználó számára. Ez azt jelenti, hogy az Azure AD-felhasználó és a megfelelő vxMaintain felhasználó közötti kapcsolat kapcsolatot kell létesítenie.

A hivatkozás kapcsolatot létesíteni, rendelje hozzá a vxMaintain **felhasználónév** érték, mint az Azure AD **felhasználónév** érték.

Konfigurálása és tesztelése az Azure AD SSO vxMaintain használatával végezze el a következő építőelemeit.

### <a name="configure-azure-ad-sso"></a>Az Azure AD-egyszeri bejelentkezés konfigurálása

Ebben a szakaszban is engedélyezheti Azure AD egyszeri bejelentkezés az Azure portálon és egyszeri bejelentkezés konfigurálása az vxMaintain alkalmazásban a következő módon:

1. Az Azure portálon a a **vxMaintain** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Az "Egyszeri bejelentkezés" parancs][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![A "SAML-alapú bejelentkezés" parancs](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. A **vxMaintain tartomány és az URL-címek**, tegye a következőket:

    ![A tartomány és az URL-címek szakasz vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. Az a **azonosító** mezőbe írjon be egy URL-címet, amely rendelkezik a következő szintaxist:`https://<company name>.verisae.com`

    b. Az a **válasz URL-CÍMEN** mezőbe írjon be egy URL-címet, amely rendelkezik a következő szintaxist:`https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Az előző értékei nem valódi. A tényleges azonosítójú frissítheti, illetve válasz URL-CÍMÉT. Szerezze be az értékeket, lépjen kapcsolatba a [vxMaintain támogatási csoport](http://www.verisae.com/contact-us).
 
4. A **SAML-aláíró tanúsítványa**, jelölje be **metaadatainak XML-kódja**, majd mentse a metaadat-fájlt a számítógépre.

    ![A "SAML aláíró tanúsítvány" szakasz](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![A Mentés gombra](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Konfigurálása **vxMaintain** SSO, küldjön a letöltött **metaadatainak XML-kódja** fájlt a [vxMaintain támogatási csoport](http://www.verisae.com/contact-us).

> [!TIP]
> Állít be az alkalmazást, mert egy előző utasításait tömör verziója elolvashatja a [Azure-portálon](https://portal.azure.com). Miután hozzáadta az alkalmazásból a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a beágyazott dokumentáció a **konfigurációs** szakasz. 
>
>A beágyazott dokumentáció szolgáltatással kapcsolatos további tudnivalókért lásd: [egyszeri bejelentkezéshez a vállalati alkalmazásokat kezeléséhez](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz az alábbi lépésekkel hoz létre tesztfelhasználó Britta Simon az Azure-portálon:

![Az Azure AD-teszt felhasználó][100]

1. Az a **Azure-portálon**, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az "Azure Active Directory" gomb](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Megjeleníti azoknak a felhasználóknak, keresse fel **felhasználók és csoportok** > **minden felhasználó**.
    
    ![A "Minden felhasználó" hivatkozásra](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    A **minden felhasználó** párbeszédpanel. 

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a tesztfelhasználó Britta Simon e-mail címét.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értéket, amelyet hozták létre a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-vxmaintain-test-user"></a>VxMaintain tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon tesztfelhasználó vxMaintain hoz létre. Felhasználók hozzáadása a vxMaintain platform, dolgozni a [vxMaintain támogatási csoport](http://www.verisae.com/contact-us). Mielőtt használná az egyszeri Bejelentkezést, hozzon létre, és a felhasználók aktiválása.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Azure SSO vxMaintain való hozzáférés biztosítása által használandó Britta Simon tesztfelhasználó engedélyezi. Ehhez tegye a következőket:

![A megjelenítendő név listában a tesztfelhasználó számára][200] 

1. Az Azure portálon **alkalmazások** megtekintéséhez lépjen **Directory** Nézet > **vállalati alkalmazások** > **összes alkalmazás**.

    ![Az "Összes alkalmazás" hivatkozásra][201] 

2. Az a **alkalmazások** listáról válassza ki **vxMaintain**.

    ![A vxMaintain hivatkozás](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza ki **Hozzáadás** , majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][203]

5. A a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**, majd válassza ki a **válassza** gomb.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési Panel segítségével tesztelheti.

Válassza a **vxMaintain** csempe a hozzáférési panelen kell jelentkezhet be a vxMaintain alkalmazás automatikusan.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Következő lépések

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

