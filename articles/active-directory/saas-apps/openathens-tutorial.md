---
title: 'Oktatóanyag: Azure Active Directory-integráció az OpenAthens |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és OpenAthens között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 269b216a94b1233c5f9f9a634fda3c05e46cac90
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435906"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Oktatóanyag: Azure Active Directory-integráció az OpenAthens

Ebben az oktatóanyagban elsajátíthatja, hogyan OpenAthens integrálása az Azure Active Directory (Azure AD).

OpenAthens integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá OpenAthens Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan bejelentkezik OpenAthens (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

OpenAthens az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy OpenAthens egyszeri bejelentkezés engedélyezve van az előfizetés

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. OpenAthens hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens hozzáadása a katalógusból
Az Azure AD integrálása a OpenAthens konfigurálásához hozzá kell OpenAthens a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A katalógus OpenAthens adhat hozzá**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Keresse meg a **vállalati alkalmazások**, majd lépjen **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **OpenAthens**válassza **OpenAthens** az eredmények panelen, és válassza ki a a **Hozzáadás** gombra.

    ![Az eredmények listájában OpenAthens](./media/openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó OpenAthens az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó OpenAthens Mi az a felhasználó Azure AD-ben. Más szóval kell OpenAthens az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

OpenAthens, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az OpenAthens tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on), amely lehetővé teszi a felhasználók a funkció használatához.
1. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user), az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. [Hozzon létre egy OpenAthens tesztfelhasználót](#create-a-openathens-test-user), van egy megfelelője a Britta Simon OpenAthens, amely a felhasználó Azure ad-ben reprezentációja van csatolva.
1. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user), amely lehetővé teszi az Azure AD egyszeri bejelentkezés használatára Britta Simon.
1. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on), hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és OpenAthens alkalmazását az egyszeri bejelentkezés konfigurálása.

**OpenAthens az Azure AD egyszeri bejelentkezés konfigurálása**

1. Az Azure Portalon az a **OpenAthens** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Az egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Egyszeri bejelentkezéssel, engedélyeznie a **egyszeri bejelentkezési** párbeszédpanelen jelölje ki **SAML-alapú bejelentkezés** , a **mód**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/openathens-tutorial/tutorial_openathens_samlbase.png)

1. Az a **OpenAthens tartomány és URL-címek** területén írja be az értéket `https://login.openathens.net/saml/2/metadata-sp` a a **azonosító** szövegmezőben.

    ![OpenAthens tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/openathens-tutorial/tutorial_openathens_url.png)

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **metaadatainak XML**, majd mentse a fájlt a számítógépen a metaadat.

    ![A letöltési hivatkozás a AMSL aláíró tanúsítvány](./media/openathens-tutorial/tutorial_openathens_certificate.png) 

1. Válassza ki a **Mentés** gombot.

    ![Az egyszeri bejelentkezés Mentés gomb](./media/openathens-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban jelentkezzen be a OpenAthens vállalati hely rendszergazdaként.

1. Válassza ki **kapcsolatok** alatti listából a **felügyeleti** fülre. 

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Válassza ki **SAML 1.1-es és 2.0-s**, majd válassza ki a **konfigurálása** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
1. A konfiguráció hozzáadásához válassza a **Tallózás** gombra kattintva töltse fel az Azure Portalról letöltött metaadatok .xml fájlt, és válassza **Hozzáadás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Az alábbi lépések szerint a **részletek** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. A **megjelenített névleképezés**válassza **attribútum használata**.

    b. Az a **megjelenítési név attribútum** szöveget adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. A **egyedi felhasználóleképezést**válassza **attribútum használata**.

    d. Az a **egyedi felhasználói attribútum** szöveget adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. A **állapot**, jelölje be mind a három jelölőnégyzetet.

    f. A **helyi felhasználói fiókok létrehozása**válassza **automatikusan**.

    g. Válassza ki **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás. Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez dokumentáció a **konfigurációs** alul található szakaszában. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: a [Azure ad-ben a beágyazott dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az, hogy hozzon létre egy tesztfelhasználót "Britta Simon." nevű, az Azure Portalon

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure ad-ben**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/openathens-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/openathens-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/openathens-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/openathens-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőben **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be az e-mail-cím a Britta Simon.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** szövegmezőben.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="create-an-openathens-test-user"></a>Hozzon létre egy OpenAthens tesztfelhasználó számára

OpenAthens támogatja a just-in-time-kiépítés, és a felhasználók automatikusan létrehozza a sikeres hitelesítés után. Bármely művelet elvégzésére ebben a szakaszban nem kell.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés OpenAthens saját hozzáférésének engedélyezésére használja.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése OpenAthens**

1. Az Azure Portalon, nyissa meg az alkalmazások megtekintése, keresse meg a könyvtár nézet, és nyissa meg **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az a **alkalmazások** listáról válassza ki **OpenAthens**.

    ![Az alkalmazások listáját a OpenAthens hivatkozásra](./media/openathens-tutorial/tutorial_openathens_app.png)  

1. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** ablaktáblán.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** listáról válassza ki **Britta Simon**.

1. Válassza ki a **kiválasztása** gombra a **felhasználók és csoportok** listája.

1. Válassza ki a **hozzárendelése** gombra a **hozzárendelés hozzáadása** ablaktáblán.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Amikor kiválasztja a **OpenAthens** csempéje a hozzáférési panelen, meg kell automatikusan bejelentkeznie az OpenAthens alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* Az SaaS-alkalmazások integrálása az Azure Active Directory oktatóanyagok listáját lásd: [az Azure ad-vel használható SaaS alkalmazások integrációjának oktatóanyagai](tutorial-list.md).
* Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

<!--Image references-->

[1]: ./media/openathens-tutorial/tutorial_general_01.png
[2]: ./media/openathens-tutorial/tutorial_general_02.png
[3]: ./media/openathens-tutorial/tutorial_general_03.png
[4]: ./media/openathens-tutorial/tutorial_general_04.png

[100]: ./media/openathens-tutorial/tutorial_general_100.png

[200]: ./media/openathens-tutorial/tutorial_general_200.png
[201]: ./media/openathens-tutorial/tutorial_general_201.png
[202]: ./media/openathens-tutorial/tutorial_general_202.png
[203]: ./media/openathens-tutorial/tutorial_general_203.png

