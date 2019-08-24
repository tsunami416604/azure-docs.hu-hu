---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a hevederrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a hám között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37a527e8cd83d292d8af8af5acd0c903c63081f2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014075"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integrációja a hevederrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a hevedert Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a hevedert, a következőket teheti:

* Az Azure AD-ben való hozzáférés-vezérlési szolgáltatás.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való kihasználása érdekében.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A hám támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO

## <a name="adding-harness-from-the-gallery"></a>Hám hozzáadása a gyűjteményből

A heveder Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **hám** a keresőmezőbe.
1. Válassza ki a **hám** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a hám számára

Konfigurálja és tesztelje az Azure AD SSO-t a hám használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között.

Az Azure AD SSO és a hám használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[HÁM SSO konfigurálása](#configure-harness-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre](#create-harness-test-user)** egy felhasználói hám-tesztelési felhasználót – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-ügyfélként.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **hám** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://app.harness.io/`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A tényleges válasz URL-címét a következő oktatóanyagban ismertetett, a **hám egyszeri bejelentkezésének konfigurálása** című szakaszban találja. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A készlet beállítása szakaszban másolja **ki** a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a hám számára.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **hám**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-harness-sso"></a>A hám SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a hám vállalati webhelyre rendszergazdaként.

1. A lap jobb felső részén kattintson a **folyamatos biztonsági** > **hozzáférés-kezelési** > **hitelesítési beállítások**elemre.

    ![A hám konfigurálása](./media/harness-tutorial/configure01.png)

1. Az **SSO-szolgáltatók** szakaszban kattintson az SSO **-szolgáltatók** > **SAML**hozzáadása lehetőségre.

    ![A hám konfigurálása](./media/harness-tutorial/configure03.png)

1. Az **SAML-szolgáltató** előugró ablakában hajtsa végre a következő lépéseket:

    ![A hám konfigurálása](./media/harness-tutorial/configure02.png)

    a. Másolja be az **SSO-szolgáltatót, engedélyezze az SAML-alapú bejelentkezést, majd adja meg a következő URL-példányt,** és illessze be a válasz URL-címe szövegmezőbe a Azure Portal alapszintű **SAML-konfiguráció** szakaszában.

    b. A **megjelenítendő név** szövegmezőbe írja be a megjelenítendő nevet.

    c. Kattintson a **fájl kiválasztása** lehetőségre az összevonási metaadatok XML-fájljának feltöltéséhez, amelyet az Azure ad-ből töltött le.

    d. Kattintson a **Submit (Küldés**) gombra.

### <a name="create-harness-test-user"></a>Hám tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a kihasználni, ki kell építeni őket a kiépítés során. A-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a kihasználni rendszergazdaként.

1. A lap jobb felső részén kattintson a> **folyamatos biztonsági**  **hozzáférés-kezelő** > **felhasználók**elemre.

    ![A hám konfigurálása](./media/harness-tutorial/configure04.png)

1. A lap jobb oldalán kattintson a **+ felhasználó hozzáadása**lehetőségre.

    ![A hám konfigurálása](./media/harness-tutorial/configure05.png)

1. A **felhasználó hozzáadása** előugró ablakban végezze el a következő lépéseket:

    ![A hám konfigurálása](./media/harness-tutorial/configure06.png)

    a. Az **e-mail-cím (ek)** szövegmezőbe írja be a felhasználóhoz hasonló `B.simon@contoso.com`e-mail címet.

    b. Válassza ki a **felhasználói csoportokat**.

    c. Kattintson a **Submit** (Küldés) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a hám csempére kattint, automatikusan be kell jelentkeznie arra a hevederre, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A hám kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

