---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Clarizen |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Clarizen között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: a7280111856f9cb2a20ebb7b52be04818c4b43c9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195273"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Clarizen

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Azure Active Directory (Azure AD) rendelkező Clarizen. Ez az integráció a következő előnyöket biztosítja:

- Szabályozhatja, ki férhet hozzá Clarizen Azure AD-ben.
- Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be Clarizen (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Ebben az oktatóanyagban a forgatókönyv két fő feladat áll:

1. Clarizen hozzáadása a katalógusból.
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása.

Ha az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek
Clarizen az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Clarizen előfizetést, amelyen engedélyezve van az egyszeri bejelentkezés

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Az Azure AD egyszeri bejelentkezés teszteléséhez tesztkörnyezetben. Az éles környezetben ne használjon, kivéve, ha erre szükség.
- Ha nem rendelkezik egy Azure AD-tesztelési környezetet, akkor az [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Clarizen hozzáadása a katalógusból
Clarizen integrálása az Azure AD beállításához adja hozzá a katalógus Clarizen a felügyelt SaaS-alkalmazások listájában.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali ablaktáblán kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory ikonra][1]

1. Kattintson a **vállalati alkalmazások**. Kattintson a **minden alkalmazás**.

    ![Kattintson a "Nagyvállalati alkalmazások" és "Összes alkalmazás"][2]

1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![A "Hozzáadás" gombra.][3]

1. A Keresés mezőbe írja be a **Clarizen**.

    ![A keresőmezőbe írja be a "Clarizen"](./media/clarizen-tutorial/tutorial_clarizen_000.png)

1. Az eredménypanelen jelölje ki a **Clarizen**, és kattintson a **Hozzáadás** , vegye fel az alkalmazást.

    ![Az eredmények ablaktábláján Clarizen kiválasztása](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
A következő szakaszokban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Clarizen a tesztfelhasználó számára Britta Simon alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Clarizen mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Clarizen hivatkozás kapcsolata kell létrehozni. A hivatkozás kapcsolatot hozhat létre, rendelhet a értékét **felhasználónév** értékeként az Azure AD-ben **felhasználónév** Clarizen a.

Az Azure AD egyszeri bejelentkezés az Clarizen tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Clarizen tesztfelhasználót](#create-a-clarizen-test-user)**  van egy megfelelője a Britta Simon Clarizen, amely kapcsolódik az Azure ad-ben ábrázolása számára.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása
Az Azure AD egyszeri bejelentkezés az Azure Portalon engedélyezze és konfigurálja az egyszeri bejelentkezés Clarizen alkalmazásában.

1. Az Azure Portalon az a **Clarizen** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    !["Egyszeri" parancsra][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen a **mód**, jelölje be **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    !["Az SAML-alapú bejelentkezés" kiválasztása](./media/clarizen-tutorial/tutorial_clarizen_01.png)

1. Az a **Clarizen tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Mezők azonosítóját, valamint a válasz URL-címe](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. Az a **azonosító** mezőbe írja be az értéket: **Clarizen**

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-cím használatával a következő mintának: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Ezek eltérnek a valós értékeket. Akkor tényleges azonosítóját használja, és a válasz URL-címe. Itt javasoljuk, hogy használja-e a karakterlánc egyedi értékét azonosítóként. A tényleges értékek beszerzéséhez forduljon a [Clarizen támogatási csapatának](https://success.clarizen.com/hc/en-us/requests/new).

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **új tanúsítvány létrehozása**.

    ![Kattintson az "Új tanúsítvány létrehozása"](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

1. Az a **új tanúsítvány létrehozása** párbeszédpanel párbeszédpanel, kattintson a naptár ikonra, és válassza ki a lejárati dátum. Ezután kattintson a **Save** (Mentés) gombra.

    ![Jelölje ki, és a lejárati dátum mentése](./media/clarizen-tutorial/tutorial_general_300.png)

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **új tanúsítvány aktívvá**, és kattintson a **mentése**.

    ![Jelölje be a jelölőnégyzetet, hogy az új tanúsítvány aktív](./media/clarizen-tutorial/tutorial_clarizen_04.png)

1. Az a **helyettesítő tanúsítvány** párbeszédpanelen kattintson a **OK**.

    ![Annak ellenőrzéséhez, hogy szeretné-e a tanúsítvány aktívvá tétele az "OK" gombra kattintva](./media/clarizen-tutorial/tutorial_general_400.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Kattintson a "Tanúsítvány (Base64)" letöltésének megkezdéséhez](./media/clarizen-tutorial/tutorial_clarizen_05.png)

1. Az a **Clarizen konfigurációs** területén kattintson **konfigurálása Clarizen** megnyitásához a **bejelentkezés konfigurálása** ablak.

    ![Kattintson a "Clarizen konfigurálása"](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    !["Bejelentkezés konfigurálása" ablakban, beleértve a fájlok és az URL-címek](./media/clarizen-tutorial/tutorial_clarizen_07.png)

1. Egy másik böngészőablakban jelentkezzen be a Clarizen vállalati hely rendszergazdaként.

1. Kattintson a felhasználónevére, majd **beállítások**.

    ![A felhasználónév alatt kattintson a "Beállítások"](./media/clarizen-tutorial/tutorial_clarizen_001.png "beállításai")

1. Kattintson a **globális beállítások** fülre. Ezt követően a **összevont hitelesítési**, kattintson a **szerkesztése**.

    !["Globális beállítások" lapon](./media/clarizen-tutorial/tutorial_clarizen_002.png "globális beállítások")

1. Az a **összevont hitelesítési** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A párbeszédpanel "Összevont hitelesítés"](./media/clarizen-tutorial/tutorial_clarizen_003.png "összevont hitelesítési")

    a. Válassza ki **engedélyezése összevont hitelesítési**.

    b. Kattintson a **feltöltése** a letöltött tanúsítvány feltöltéséhez.

    c. Az a **bejelentkezési URL** mezőbe írja be az értékét **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure AD alkalmazás-konfigurációs ablakából.

    d. Az a **kijelentkezéses URL-cím** mezőbe írja be az értékét **kijelentkezéses URL-cím** az Azure AD alkalmazás-konfigurációs ablakából.

    e. Válassza ki **használja a POST**.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Az Azure Portalon hozzon létre egy tesztfelhasználót Britta Simon nevű.

![Az Azure ad-ben tesztfelhasználó és az e-mail-címe][100]

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory ikonra](./media/clarizen-tutorial/create_aaduser_01.png)

1. Kattintson a **felhasználók és csoportok**, és kattintson a **minden felhasználó** felhasználók listájának megjelenítéséhez.

    ![Kattintson a "Felhasználók és csoportok" és "Minden felhasználó"](./media/clarizen-tutorial/create_aaduser_02.png)

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel bezárásához.

    ![A "Hozzáadás" gombra.](./media/clarizen-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Kitölti a rendszer "User" párbeszédpanel, amelyben a nevét, e-mail-cím és jelszó](./media/clarizen-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a Britta Simon fiók e-mail-címét.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-clarizen-test-user"></a>Clarizen tesztfelhasználó létrehozása

Ez a szakasz célja Clarizen Britta Simon nevű felhasználó létrehozásához.

**Ha szeretné manuálisan létrehozni a felhasználói, hajtson végre a következő lépéseket:**

Ahhoz, hogy jelentkezzen be a Clarizen az Azure AD-felhasználók, felhasználói fiókokat kell kiépítenie. Clarizen, esetén kiépítése a manuális feladat.

1. Jelentkezzen be rendszergazdaként a Clarizen vállalati webhely.

2. Kattintson a **személyek**.

    ![Kattintson a "Azok, akik"](./media/clarizen-tutorial/create_aaduser_001.png "személyek")

3. Kattintson a **felhasználó meghívása**.

    !["A felhasználó meghívása" gomb](./media/clarizen-tutorial/create_aaduser_002.png "felhasználó meghívása")

1. Az a **meghívása személyek** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    !["Felkérése" párbeszédpanelen](./media/clarizen-tutorial/create_aaduser_003.png "személyek meghívása")

    a. Az a **E-mail** mezőbe írja be a Britta Simon fiók e-mail-címét.

    b. Kattintson a **meghívása**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozásra kattintva a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése
Britta Simon számára a hozzáférés biztosításával a Clarizen Azure egyszeri bejelentkezés használata lehetővé.

![Hozzárendelt tesztfelhasználó számára][200]

1. Az Azure portál, nyissa meg az alkalmazások megtekintéséhez tallózással keresse meg a könyvtár nézetben, a kattintson **vállalati alkalmazások**, és kattintson a **minden alkalmazás**.

    ![Kattintson a "Nagyvállalati alkalmazások" és "Összes alkalmazás"][201]

1. Az alkalmazások listájában jelölje ki a **Clarizen**.

    ![Clarizen listából történő kiválasztásakor](./media/clarizen-tutorial/tutorial_clarizen_50.png)

1. A bal oldali ablaktáblán kattintson a **felhasználók és csoportok**.

    ![Kattintson a "Felhasználók és csoportok"][202]

1. Kattintson a **Hozzáadás** gombra. Ezt követően a a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A "Hozzáadás" gombra, és a "Hozzárendelés hozzáadása" párbeszédpanel][203]

1. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** felhasználók listájában.

1. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése
Az Azure AD egyszeri bejelentkezés beállításainak ellenőrzéséhez a hozzáférési panelen.

Ha a hozzáférési panelen a Clarizen csempére kattint, akkor kell lehet automatikusan bejelentkezett Clarizen alkalmazását.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clarizen-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
[4]: ./media/clarizen-tutorial/tutorial_general_04.png

[100]: ./media/clarizen-tutorial/tutorial_general_100.png

[200]: ./media/clarizen-tutorial/tutorial_general_200.png
[201]: ./media/clarizen-tutorial/tutorial_general_201.png
[202]: ./media/clarizen-tutorial/tutorial_general_202.png
[203]: ./media/clarizen-tutorial/tutorial_general_203.png
