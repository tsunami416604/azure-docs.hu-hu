---
title: 'Oktatóanyag: Azure Active Directory integrálása mezőben |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a mező között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-box"></a>Az Azure Active Directory integrálása mezőbe

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Azure Active Directory (Azure AD) segítségével.

Által az Azure AD integrálása mezőben, töltse le a következő előnyöket biztosítja:

- Azt is szabályozhatja az Azure AD, aki hozzáfér-e be.
- Engedélyezheti a felhasználóknak, hogy az beszerzése aláírva automatikusan (egyszeri bejelentkezés, vagy az SSO) mezőben az Azure AD-fiókok.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Az Azure AD SaaS alkalmazásintegráció, lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a jelölését, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Előfizetés mezőben SSO engedélyezése

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, azt javasoljuk, hogy végezzen *nem* használja az éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. 

Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Mező hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-box-from-the-gallery"></a>Adja hozzá a mezőbe a gyűjteményből
A rendszerrel történő integráció konfigurálása az Azure AD mezőben, mező hozzáadása a gyűjteményből a kezelt SaaS-alkalmazások listáját a következő módon:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![A "Vállalati alkalmazások" ablak][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra az ablak tetején.

    ![Az "új alkalmazás" gomb][3]

4. Írja be a keresőmezőbe, **mezőben**, jelölje be **mezőben** az eredmények listájában, és válassza a **Hozzáadás**.

    ![Az eredmények listájában párbeszédpanel](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a mezőbe, a "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD az Azure ad-ben a mezőbe felhasználó és a megfelelő azonosítania kell. Ez azt jelenti az Azure AD-felhasználó és mezőben ugyanezen felhasználó között egy hivatkozás kapcsolatot kell létrehozni.

A hivatkozás kapcsolat létrehozására, rendelje hozzá, mint az a mező *felhasználónév* értékének a *felhasználónév* az Azure ad-ben.

Az Azure AD egyszeri bejelentkezést a jelölését tesztelése és konfigurálása, végezze el a következő öt szakaszokban építőelemeit.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és egyszeri bejelentkezés konfigurálása az mezőben alkalmazásban a következő módon:

1. Az Azure portálon a a **mezőben** alkalmazás integrációs ablakban válassza ki **egyszeri bejelentkezés**.

    ![Az "Egyszeri bejelentkezés" hivatkozásra][4]

2. Az a **egyszeri bejelentkezés** ablakban, a a **egyszeri bejelentkezés mód** mezőben válassza **SAML-alapú bejelentkezés**.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. A **mezőben tartomány és az URL-címek**, tegye a következőket:

    ![Az egyszeri bejelentkezés információkat "Mezőben a tartomány és az URL-címek"](./media/active-directory-saas-box-tutorial/url3.png)

    a. Az a **bejelentkezési URL-cím** mezőbe, írja be egy URL-címet a következő formátumban: *https://\<altartomány >. box.com*.

    b. Az a **azonosító** szövegmezőhöz típus **box.net**.
     
    > [!NOTE] 
    > Az előző értékei nem valódi. Frissítse azokat a tényleges bejelentkezési URL-cím és azonosítója. Szerezze be az értékeket, lépjen kapcsolatba a [mezőben ügyfél-támogatási csoport](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. A **SAML-aláíró tanúsítványa**, jelölje be **metaadatainak XML-kódja**, és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse a [beállítania egyszeri Bejelentkezést a saját](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Ha be fiókja nem engedélyezi az egyszeri bejelentkezési beállítások, szükség lehet kapcsolódni a [mezőben ügyfél-támogatási csoport](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) , és adja meg a letöltött XML-fájl.

> [!TIP]
> Hoz létre az alkalmazást, mert egy előző utasításait tömör verziója elolvashatja a [Azure-portálon](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a a beágyazott dokumentációjában találhatók a **konfigurációs** szakasz alján. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz az alábbi lépésekkel hoz létre tesztfelhasználó Britta Simon az Azure-portálon:

![Hozzon létre egy Azure AD-teszt felhasználó][100]

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Az aktuális felhasználó listáját jeleníti meg, jelölje be **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. Felső részén a **minden felhasználó** ablakban válassza ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    A **felhasználói** ablak nyílik meg.

4. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-box-test-user"></a>Mezőbe tesztfelhasználó létrehozása

Ebben a szakaszban hoz létre a tesztfelhasználó Britta Simon mezőbe. Mezőbe támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van. Ha a felhasználó nem létezik, egy új mező elérésére tett kísérlet során jön létre. Önnek a felhasználó létrehozásához kell semmilyen műveletet.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban a felhasználó által biztosított hozzáférés be Azure egyszeri bejelentkezéshez használandó Britta Simon engedélyezi. Ehhez tegye a következőket:

![A felhasználói szerepkör hozzárendelése][200]

1. Az Azure portálon, nyissa meg a **alkalmazások** nézet, keresse fel a **Directory** megtekintéséhez, majd válassza ki **vállalati alkalmazások** > **összes alkalmazások**.

    ![A "Vállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

2. Az a **alkalmazások** listáról válassza ki **mezőben**.

    ![A csatolás](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki **Hozzáadás** , majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

6. Válassza ki a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** ablakban válassza ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha bejelöli a **mezőben** csempére a hozzáférési panelre, akkor nyissa meg a bejelentkezési oldal való bejelentkezéshez a mezőbe alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

