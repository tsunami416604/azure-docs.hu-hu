---
title: 'Oktatóanyag: Tudásbázis bárhol segítségével az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az ismeretek bárhol LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34553c92f54992698a7e3930ac302ef970edd7c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236733"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Oktatóanyag: Integráció Tudásbázis bárhol segítségével az Azure Active Directory

Ebben az oktatóanyagban elsajátíthatja a Tudásbázis bárhol LMS integrálása az Azure Active Directory (Azure AD) lesz. Tudásbázis bárhol LMS integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a Tudásbázis bárhol segítségével az Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett a Tudásbázis bárhol segítségével az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Tudásbázis bárhol LMS egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Tudásbázis bárhol LMS **SP** által kezdeményezett egyszeri Bejelentkezést, és támogatja a **igény szerinti** felhasználóátadást készíthet elő.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Tudásbázis bárhol LMS hozzáadása a katalógusból

Az Azure AD integrálása a Tudásbázis bárhol LMS konfigurálásához hozzá kell Tudásbázis bárhol LMS a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Tudásbázis bárhol LMS** kifejezést a keresőmezőbe.
1. Válassza ki **Tudásbázis bárhol LMS** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO Tudásbázis bárhol LMS nevű tesztfelhasználó használata **b Simon**. Az SSO működjön kell a Tudásbázis bárhol segítségével egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO Tudásbázis bárhol LMS tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Tudásbázis bárhol LMS konfigurálása](#configure-knowledge-anywhere-lms)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Tudásbázis bárhol LMS tesztfelhasználót](#create-knowledge-anywhere-lms-test-user)**  egy megfelelője a b Simon kell a Tudásbázis bárhol segítségével, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Tudásbázis bárhol LMS** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    1. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját és a válasz URL-cím, az oktatóanyag későbbi részében ismertetett ezeket az értékeket.

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Tudásbázis bárhol LMS ügyfél-támogatási csapatának](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **állítsa be a Tudásbázis bárhol LMS** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Bárhol LMS Tudásbázis konfigurálása

1. Automatizálhatja a Tudásbázis bárhol LMS konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Tudásbázis bárhol LMS** fog irányítja át a Tudásbázis bárhol LMS-alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatait, jelentkezzen be a Tudásbázis bárhol segítségével. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3-7 lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha szeretné manuálisan beállítani a Tudásbázis bárhol LMS, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Tudásbázis bárhol LMS webhelyét, és hajtsa végre az alábbi lépéseket:

4. Válassza ki a a **hely** fülre.

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Válassza ki a a **SAML-beállítások** fülre.

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Kattintson a **hozzáadhat új**.

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Az a **Add/Update SAML-beállítások** lapon, a következő lépésekkel:

    ![Tudásbázis bárhol LMS-konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Adja meg az Identitásszolgáltató nevét beállításával a szervezet. Az ex:- `Azure`.

    b. Az a **Identitásszolgáltató Entitásazonosító** szövegmezőjébe illessze be **az Azure AD-azonosító** érték, amely az Azure Portalról másolta.

    c. Az a **Identitásszolgáltató URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    d. Nyissa meg a letöltött tanúsítvány-fájlt az Azure Portalról a Jegyzettömbbe, másolja a tartalmat a tanúsítvány, és illessze be azt **tanúsítvány** szövegmezőbe.

    e. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezési URL-címe** érték, amely az Azure Portalról másolta.

    f. Válassza ki **fő hely** lehetőséget a legördülő menüből a **tartomány**.

    g. Másolás a **SP Entitásazonosító** értékét, és illessze be azt **azonosító** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    h. Másolás a **SP Response(ACS) URL-cím** értékét, és illessze be azt **válasz URL-cím** szöveget szövegmezőbe a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    i. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával a Tudásbázis bárhol LMS Azure egyszeri bejelentkezés használatára b Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Tudásbázis bárhol LMS**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tudásbázis bárhol LMS tesztfelhasználó létrehozása

Ebben a szakaszban egy b Simon nevű felhasználó a Tudásbázis bárhol LMS jön létre. Tudásbázis bárhol LMS támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Tudásbázis bárhol segítségével, a hitelesítés után egy új jön létre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Tudásbázis bárhol LMS csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Tudásbázis bárhol segítségével, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)