---
title: 'Oktatóanyag: Azure Active Directory integráció a RunMyProcess-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és RunMyProcess között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880373"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Oktatóanyag: A RunMyProcess integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a RunMyProcess a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az RunMyProcess-t az Azure AD-vel, a következőket teheti:

* A RunMyProcess-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a RunMyProcess az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* RunMyProcess egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A RunMyProcess támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-runmyprocess-from-the-gallery"></a>RunMyProcess hozzáadása a gyűjteményből

A RunMyProcess Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a RunMyProcess a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **RunMyProcess** kifejezést a keresőmezőbe.
1. Válassza ki a **RunMyProcess** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a RunMyProcess a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a RunMyProcess-ben.

Az Azure AD SSO és a RunMyProcess konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[RUNMYPROCESS SSO konfigurálása](#configure-runmyprocess-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre RunMyProcess-teszt felhasználót](#create-runmyprocess-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-RunMyProcess rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **RunMyProcess** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a [RunMyProcess](mailto:support@runmyprocess.com) ügyfélszolgálati csapatához. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **RunMyProcess beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>RunMyProcess SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a RunMyProcess-bérlőbe.

1. A bal oldali navigációs panelen kattintson a **fiók** elemre, és válassza a **konfiguráció**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Nyissa meg a **hitelesítési módszer** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. As **metódusként**válassza **az egyszeri bejelentkezés a Samlv2**lehetőséget.

    b. Az **SSO-átirányítás** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    c. A kijelentkezési **átirányítás** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím**értékét, amelyet a Azure Portalból másolt.

    d. A **név azonosítójának formázása** szövegmezőbe írja be a **név azonosító formátumát** **urn: Oasis: Names: TC: SAML: 1.1: nameid-Format: emailAddress**értékre.

    e. Nyissa meg a letöltött tanúsítványfájl Azure Portal a Jegyzettömbben, másolja ki a tanúsítványfájl tartalmát, majd illessze be a **tanúsítvány** szövegmezőbe.

    f. Kattintson a **Mentés** ikonra.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a RunMyProcess.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **RunMyProcess**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-runmyprocess-test-user"></a>RunMyProcess-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a RunMyProcess, a RunMyProcess-ben kell kiépíteni őket. RunMyProcess esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a RunMyProcess vállalati webhelyre rendszergazdaként.

1. Kattintson a **fiók** lehetőségre, és válassza a **felhasználók** lehetőséget a bal oldali navigációs panelen, majd kattintson az **új felhasználó**lehetőségre.

    ![Új felhasználó](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Új felhasználó")

1. A **felhasználói beállítások** szakaszban hajtsa végre a következő lépéseket:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Írja be egy érvényes Azure AD-fiók **nevét** és **e-mail-címét** , amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

    b. Válassza ki az **ide nyelvét**, **nyelvét**és **profilját**.

    c. Válassza **a fiók létrehozása E-mail küldése nekem**lehetőséget.

    d. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A RunMyProcess által biztosított egyéb RunMyProcess-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a RunMyProcess csempére kattint, automatikusan be kell jelentkeznie arra a RunMyProcess, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)