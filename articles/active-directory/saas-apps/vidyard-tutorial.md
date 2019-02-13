---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Vidyard |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Vidyard között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d25520f88869025f7e262d81295ee64f8e12d21
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163594"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Vidyard

Ebben az oktatóanyagban elsajátíthatja, hogyan Vidyard integrálása az Azure Active Directory (Azure AD).

Vidyard integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Vidyard Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Vidyard (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Vidyard az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Vidyard egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Vidyard hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-vidyard-from-the-gallery"></a>Vidyard hozzáadása a katalógusból
Az Azure AD integrálása a Vidyard konfigurálásához hozzá kell Vidyard a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Vidyard hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Vidyard**válassza **Vidyard** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Vidyard](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Vidyard a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Vidyard mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Vidyard hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Vidyard tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Vidyard tesztfelhasználót](#create-a-vidyard-test-user)**  – egy megfelelője a Britta Simon Vidyard, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Vidyard alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Vidyard, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Vidyard** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Az a **Vidyard tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Vidyard tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Vidyard tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezeket az értékeket a tényleges azonosítója, válasz URL-cím és a bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett frissíteni

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/vidyard-tutorial/tutorial_general_400.png)

1. Az a **Vidyard konfigurációs** területén kattintson **konfigurálása Vidyard** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Vidyard konfiguráció](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Vidyard szoftver vállalati hely rendszergazdaként.

1. A Vidyard irányítópultján válassza ki **csoport** > **biztonsági**

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure1.png)

1. Kattintson a **új profil** fülre.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure2.png)

1. Az a **SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure3.png)

    a. Adja meg az általános profil nevét a **profilnév** szövegmezőbe.

    b. Másolás **egyszeri bejelentkezési felhasználói bejelentkezési oldal** értékét, és illessze be azt **bejelentkezési URL-cím** szövegmezőjébe **Vidyard tartomány és URL-címek szakaszt** az Azure Portalon.

    c. Másolás **ACS URL** értékét, és illessze be azt **válasz URL-cím** szövegmezőjébe **Vidyard tartomány és URL-címek szakaszt** az Azure Portalon.

    d. Másolás **kibocsátó/metaadatok URL-címe** értékét, és illessze be azt **azonosító** szövegmezőjébe **Vidyard tartomány és URL-címek szakaszt** az Azure Portalon.

    e. Nyissa meg a letöltött tanúsítvány-fájlt a Jegyzettömbben az Azure Portalról, és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.

    f. Az a **SAML-végpont URL-címe** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    g. Kattintson a **Megerősítés** gombra.

1. Válassza az egyszeri bejelentkezés lap **hozzárendelése** mellett egy meglévő profilt

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Miután létrehozta az egyszeri bejelentkezési profil, rendelje hozzá minden olyan csoportot, amelyhez a felhasználók hozzáférést Azure-on keresztül kell. Ha a felhasználó nem létezik, amelyhez hozzá lettek rendelve a csoporton belül, Vidyard automatikusan hozzon létre egy felhasználói fiókot, és a valós idejű szerepkör hozzárendelése.

1. Válassza ki a szervezet csoport, amely látható a **rendelje hozzá a rendelkezésre álló csoportok**.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure5.png)

1. A hozzárendelt csoportokat láthatja a **jelenleg hozzárendelt csoportok**. Válasszon egy szerepkört beállításával a szervezet, és kattintson a csoport **megerősítése**.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > További információkért tekintse meg [feljegyzett](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/vidyard-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/vidyard-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/vidyard-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/vidyard-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-vidyard-test-user"></a>Vidyard tesztfelhasználó létrehozása

Ez a szakasz célja Vidyard Britta Simon nevű felhasználó létrehozásához. Vidyard támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Vidyard elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Vidyard támogatási csapatának](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Vidyard Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Vidyard, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Vidyard**.

    ![Az alkalmazások listáját a Vidyard hivatkozásra](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Vidyard csempére kattint, meg kell lekérése automatikusan bejelentkezett az Vidyard alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

