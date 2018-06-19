---
title: 'Oktatóanyag: Azure Active Directoryval integrált Clarizen |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Clarizen között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: 881f9398c1f6de4d3cc083dee34b6417a22eab29
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927573"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Oktatóanyag: Azure Active Directoryval integrált Clarizen

Ebben az oktatóanyagban Azure Active Directory (Azure AD) integrálása elsajátíthatja a Clarizen. Ez az integráció lehetővé teszi a következő előnyöket biztosítja:

- Azt is szabályozhatja, az Azure AD, aki hozzáfér Clarizen.
- Engedélyezheti a felhasználóknak, hogy automatikusan jelentkeznie Clarizen (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Ebben az oktatóanyagban a forgatókönyv két fő feladatokat foglalja magában:

1. Adja hozzá a Clarizen a gyűjteményből.
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása.

Ha szoftver további adatait, és az Azure AD egy szolgáltatott szoftverként (SaaS) alkalmazásintegráció, lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek
Konfigurálása az Azure AD-integrációs Clarizen, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az egyszeri bejelentkezés engedélyezett Clarizen előfizetés

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Az Azure AD az egyszeri bejelentkezés tesztelése egy tesztkörnyezetben. Az éles környezetben ne használjon, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD-tesztelési környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Adja hozzá a Clarizen a gyűjteményből
Az Azure AD integrálása a Clarizen megadásához vegye fel Clarizen a gyűjteményből a felügyelt SaaS-alkalmazásokhoz.

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali ablaktáblán kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory ikonra][1]

2. Kattintson a **vállalati alkalmazások**. Kattintson a **összes alkalmazás**.

    ![Kattintson a "Vállalati alkalmazások" és "Összes alkalmazás"][2]

3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![A "Hozzáadás" gombra][3]

4. Írja be a keresőmezőbe, **Clarizen**.

    ![A keresőmezőbe írja be a "Clarizen"](./media/clarizen-tutorial/tutorial_clarizen_000.png)

5. Az eredmények ablaktáblájában válassza **Clarizen**, és kattintson a **Hozzáadás** hozzáadása az alkalmazáshoz.

    ![Az eredménypanelen Clarizen kiválasztása](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
A következő szakaszokban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést a tesztfelhasználó számára Britta Simon alapján Clarizen.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Clarizen a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Clarizen közötti kapcsolat kapcsolatot kell létrehozni. Kapcsolatot hoz létre a kapcsolat kapcsolat értékének hozzárendelésével **felhasználónév** értékeként Azure AD-ben **felhasználónév** a Clarizen.

Az Azure AD egyszeri bejelentkezést a Clarizen tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Clarizen tesztfelhasználó létrehozása](#create-a-clarizen-test-user)**  való Britta Simon valami Clarizen, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása
Az Azure AD az egyszeri bejelentkezés az Azure portálon engedélyezése és konfigurálása egyszeri bejelentkezéshez az Clarizen alkalmazásban.

1. Az Azure portálon a a **Clarizen** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Kattintson az "Egyszeri bejelentkezéshez"][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanelen a **mód**, jelölje be **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    !["SAML-alapú bejelentkezés" kiválasztása](./media/clarizen-tutorial/tutorial_clarizen_01.png)

3. Az a **Clarizen tartomány és az URL-címek** területen tegye a következőket:

    ![Jelölőnégyzetéből azonosítója és a válasz URL-címe](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. Az a **azonosító** mezőbe írja be az értéket, mint: **Clarizen**

    b. Az a **válasz URL-CÍMEN** mezőbe írja be egy URL-cím használatával a következő mintát: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Ezek a megadandó nem valódi értékek. Ki kell a tényleges azonosítót és a válasz URL-CÍMÉT. Itt javasoljuk, hogy az azonosító, a karakterlánc egyedi érték használatát. Ahhoz, hogy a tényleges értékek, lépjen kapcsolatba a [Clarizen támogatási csoport](https://success.clarizen.com/hc/en-us/requests/new).

4. Az a **SAML-aláíró tanúsítványa** kattintson **hozzon létre új tanúsítvány**.

    ![Kattintson a "Hozható létre új tanúsítvány"](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

5. Az a **új tanúsítvány létrehozása** párbeszédpanel mezőben, kattintson a naptár ikonra, és válassza ki a lejárati dátummal. Ezután kattintson a **Save** (Mentés) gombra.

    ![Válassza ki, majd lejárati dátummal mentése](./media/clarizen-tutorial/tutorial_general_300.png)

6. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **új tanúsítvány aktiválásához**, és kattintson a **mentése**.

    ![Jelölje be a jelölőnégyzetet, hogy az új tanúsítvány aktív](./media/clarizen-tutorial/tutorial_clarizen_04.png)

7. Az a **helyettesítő tanúsítvány** párbeszédpanel, kattintson a **OK**.

    ![Annak ellenőrzéséhez, hogy szeretné-e a tanúsítvány aktiválásához az "OK" gombra kattintva](./media/clarizen-tutorial/tutorial_general_400.png)

8. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Kattintson a "Tanúsítvány (Base64)" a letöltés megkezdéséhez](./media/clarizen-tutorial/tutorial_clarizen_05.png)

9. Az a **Clarizen konfigurációs** területén kattintson **konfigurálása Clarizen** megnyitásához a **bejelentkezés konfigurálása** ablak.

    ![Kattintson a "Clarizen konfigurálása"](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    !["Bejelentkezés konfigurálása" ablak, beleértve a fájlok és az URL-címek](./media/clarizen-tutorial/tutorial_clarizen_07.png)

10. Egy másik webes böngészőablakban jelentkezzen be a Clarizen vállalati webhely rendszergazdaként.

11. Kattintson a felhasználónevére, majd **beállítások**.

    ![Kattintson a "Beállítások" a felhasználónév](./media/clarizen-tutorial/tutorial_clarizen_001.png "beállítások")

12. Kattintson a **globális beállítások** fülre. Ezt követően a **összevont hitelesítési**, kattintson a **szerkesztése**.

    !["Globális beállítások" lapon](./media/clarizen-tutorial/tutorial_clarizen_002.png "globális beállítások")

13. Az a **összevont hitelesítési** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A párbeszédpanel "Összevont hitelesítési"](./media/clarizen-tutorial/tutorial_clarizen_003.png "összevont hitelesítési")

    a. Válassza ki **engedélyezése összevont hitelesítési**.

    b. Kattintson a **feltöltése** a letöltött tanúsítvány feltöltése.

    c. Az a **bejelentkezési URL** mezőbe írja be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** a Azure AD alkalmazás-konfigurációs ablakból.

    d. Az a **Sign-out URL-cím** mezőbe írja be az értékét **Sign-Out URL-cím** a Azure AD alkalmazás-konfigurációs ablakból.

    e. Válassza ki **használja a FELADÁS egy vagy több**.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Az Azure-portálon Britta Simon nevű tesztfelhasználó létrehozása.

![Név és e-mail címét az Azure AD-teszt felhasználó][100]

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory ikonra](./media/clarizen-tutorial/create_aaduser_01.png)

2. Kattintson a **felhasználók és csoportok**, és kattintson a **minden felhasználó** azon felhasználók listájának megjelenítéséhez.

    ![Kattintson a "Felhasználók és csoportok" és "Minden felhasználó"](./media/clarizen-tutorial/create_aaduser_02.png)

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel megnyitásához.

    ![A "Hozzáadás" gombra](./media/clarizen-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    !["User" párbeszédpanel a nevét, e-mail címét és jelszavát kitöltve](./media/clarizen-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a Britta Simon fiók e-mail címét.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-clarizen-test-user"></a>Clarizen tesztfelhasználó létrehozása

Ez a szakasz célja Clarizen Britta Simon nevű felhasználót létrehozni. Clarizen támogatja az automatikus a felhasználók átadása, amely alapértelmezés szerint van engedélyezve. További részletek találhatók [Itt](clarizen-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

**Ha szeretne létrehozni a felhasználót manuálisan, hajtson végre a következő lépéseket:**

Ahhoz, hogy az Azure AD-felhasználók Clarizen bejelentkezni, el kell juttatnia a felhasználói fiókokat. Clarizen, ha egy kézi tevékenység.

1. Jelentkezzen be rendszergazdaként a Clarizen vállalati webhelyre.

2. Kattintson a **személyek**.

    ![Kattintson a "Személyek"](./media/clarizen-tutorial/create_aaduser_001.png "személyek")

3. Kattintson a **felhasználó meghívása**.

    !["A felhasználó meghívása" gomb](./media/clarizen-tutorial/create_aaduser_002.png "felhasználók meghívása")

4. Az a **meghívása személyek** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    !["Felkérése" párbeszédpanelen](./media/clarizen-tutorial/create_aaduser_003.png "személyek meghívása")

    a. Az a **E-mail** mezőbe írja be a Britta Simon fiók e-mail címét.

    b. Kattintson a **meghívása**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos fog egy e-maileket és hivatkozásra a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó
Britta Simon által biztosított a hozzáférés Clarizen használandó Azure egyszeri bejelentkezés engedélyezése.

![Hozzárendelt tesztfelhasználó számára][200]

1. Az Azure portál, nyissa meg az alkalmazások megtekintéséhez keresse meg a könyvtár nézetben kattintson **vállalati alkalmazások**, és kattintson a **összes alkalmazás**.

    ![Kattintson a "Vállalati alkalmazások" és "Összes alkalmazás"][201]

2. Az alkalmazások listában válassza ki a **Clarizen**.

    ![Clarizen listából történő kiválasztásakor](./media/clarizen-tutorial/tutorial_clarizen_50.png)

3. Kattintson a bal oldali ablaktáblában **felhasználók és csoportok**.

    ![Kattintson a "Felhasználók és csoportok"][202]

4. Kattintson a **Hozzáadás** gombra. Ezt követően a a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A "Hozzáadás" gombra, és a "Hozzáadás hozzárendeléshez" párbeszédpanel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listáját.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanel, kattintson a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata
Az Azure AD egyszeri bejelentkezés beállításai tesztelése a hozzáférési Panel használatával.

Ha a hozzáférési panelen Clarizen csempére kattint, be kell automatikusan jelentkeznie az Clarizen alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](clarizen-provisioning-tutorial.md)

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