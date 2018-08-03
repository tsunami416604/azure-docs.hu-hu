---
title: 'Oktatóanyag: Azure Active Directory-integráció az Box |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Box között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: f5aa724e9848c9794eef093aef15b0aaed9cae97
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435760"
---
# <a name="integrate-azure-active-directory-with-box"></a>Az Azure Active Directory integrálásának mezőbe

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Azure Active Directory (Azure AD) a mezőbe.

Az Azure AD integrálásával a jelölését, kérje le a következő előnyökkel jár:

- Szabályozhatja, aki hozzáféréssel rendelkezik a Boxba Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy az első bejelentkezett automatikusan be (egyszeri bejelentkezés, vagy az SSO) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Box, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Box SSO-kompatibilis előfizetés

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy *nem* éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. 

Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Mező hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-box-from-the-gallery"></a>Mező hozzáadása a katalógusból
A rendszerrel történő integráció konfigurálása az Azure AD-be, mező hozzáadása a katalógusból a kezelt SaaS-alkalmazások listáját az alábbiak szerint:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A "Nagyvállalati alkalmazások" ablak][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra az ablak tetején.

    ![Az "új alkalmazás" gombra][3]

1. A Keresés mezőbe írja be a **Box**, jelölje be **Box** a találatok listájában, és válassza ki a **Hozzáadás**.

    ![Az eredmények listájában mezőbe](./media/box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Box, a teszt "Britta Simon." nevű felhasználó

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell állapítania a Box felhasználó és a megfelelő Azure AD-ben. Más szóval egy Azure AD-felhasználót és ugyanaz a felhasználó a boxban hivatkozás kapcsolatának kell létrehozni.

A hivatkozás kapcsolat létrehozására, rendelje hozzá a Box, *felhasználónév* értékét a *felhasználónév* az Azure ad-ben.

Az Azure AD egyszeri bejelentkezés a Box tesztelése és konfigurálása, hajtsa végre a következő öt szakaszokban építőelemeket.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezés konfigurálása a Box alkalmazásban az alábbiak szerint:

1. Az Azure Portalon az a **Box** application integration ablakban válassza **egyszeri bejelentkezési**.

    ![Az "Egyszeri bejelentkezés" hivatkozásra][4]

1. Az a **egyszeri bejelentkezési** ablakban, a a **egyszeri bejelentkezési mód** jelölje ki **SAML-alapú bejelentkezés**.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/box-tutorial/tutorial_box_samlbase.png)

1. A **Box tartomány és URL-címek**, tegye a következőket:

    !["Mező a tartomány és URL-címek" egyszeri bejelentkezési adatait](./media/box-tutorial/url3.png)

    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-címet a következő formátumban: *https://\<altartomány >. box.com*.

    b. Az a **azonosító** szövegmezőbe írja be **box.net**.
     
    > [!NOTE] 
    > Az előző értékek nem valódi. Frissítse azokat a tényleges bejelentkezési URL-cím és azonosító. Az értékek beszerzéséhez forduljon a [Box ügyfél-támogatási csapatának](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

1. Alatt **SAML-aláíró tanúsítvány**, jelölje be **metaadatainak XML**, majd mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/box-tutorial/tutorial_box_certificate.png) 

1. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/box-tutorial/tutorial_general_400.png)
    
1. Az alkalmazás egyszeri bejelentkezés konfigurálásához hajtsa végre az eljárást a [az egyszeri bejelentkezés beállítása a saját](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Ha nem engedélyezi az egyszeri bejelentkezési beállításainak Box-fiókja, szüksége lehet kapcsolódni a [Box ügyfél-támogatási csapatának](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) , és adja meg a letöltött XML-fájlt.

> [!TIP]
> Az alkalmazás beállítása, ahogy az előző utasítások a tömör verziója olvashat a [az Azure portal](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és hozzáférhet a dokumentáció az Embedded a **konfigurációs** alul található szakaszában. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban tesztfelhasználó Britta Simon az Azure Portalon létrehozhat az alábbiak szerint:

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás](./media/box-tutorial/create_aaduser_01.png)

1. Az aktuális felhasználó megjelenítéséhez válassza **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/box-tutorial/create_aaduser_02.png)

1. Felső részén a **minden felhasználó** ablakban válassza **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/box-tutorial/create_aaduser_03.png)

    A **felhasználói** ablak nyílik meg.

1. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/box-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-box-test-user"></a>Box tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon mezőben tesztfelhasználót hoz létre. Box támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Ha a felhasználó még nem létezik, amikor megpróbálja elérni a Box egy új jön létre. Semmit nem kell az, hogy a felhasználó létrehozásához.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze felhasználói Britta Simon a hozzáférés biztosításával a Boxba Azure egyszeri bejelentkezés használatára. Ehhez tegye a következőket:

![A felhasználói szerepkör hozzárendelése][200]

1. Az Azure Portalon nyissa meg a **alkalmazások** nézet, nyissa meg a **Directory** megtekintheti, és válassza ki **vállalati alkalmazások** > **összes alkalmazások**.

    ![A "Nagyvállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

1. Az a **alkalmazások** listáról válassza ki **Box**.

    ![A Box-hivatkozás](./media/box-tutorial/tutorial_box_app.png)  

1. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki **Hozzáadás** , majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

1. Válassza ki a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** ablakban válassza **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Amikor kiválasztja a **Box** csempéje a hozzáférési panelen, akkor nyissa meg a bejelentkezési oldal, a Box alkalmazás való bejelentkezéshez.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/box-tutorial/tutorial_general_01.png
[2]: ./media/box-tutorial/tutorial_general_02.png
[3]: ./media/box-tutorial/tutorial_general_03.png
[4]: ./media/box-tutorial/tutorial_general_04.png

[100]: ./media/box-tutorial/tutorial_general_100.png

[200]: ./media/box-tutorial/tutorial_general_200.png
[201]: ./media/box-tutorial/tutorial_general_201.png
[202]: ./media/box-tutorial/tutorial_general_202.png
[203]: ./media/box-tutorial/tutorial_general_203.png

