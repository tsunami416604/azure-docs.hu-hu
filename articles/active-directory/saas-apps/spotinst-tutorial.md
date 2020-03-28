---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Spotinsttal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Spotinst között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263285"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Spotinsttal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Spotinst-ot az Azure Active Directoryval (Azure AD). Ha integrálja a Spotinst-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Spotinst.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezhessenek a Spotinst-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Spotinst egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Spotinst támogatja az **SP-t és az IDP** által kezdeményezett sso-t

## <a name="adding-spotinst-from-the-gallery"></a>Spotinst hozzáadása a galériából

A Spotinst azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Spotinst-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Gyűjtemény hozzáadás szakaszába** írja be a **Spotinst** kifejezést a keresőmezőbe.
1. Válassza a **Spotinst** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Spotinst számára

Konfigurálja és tesztelje az Azure AD-sSO-t a Spotinst segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó spotinst.

Az Azure AD SSO konfigurálásához és teszteléséhez a Spotinst segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be a Spotinst Egyszeri bejelentkezést](#configure-spotinst-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * **[Hozzon létre Spotinst teszt felhasználó](#create-spotinst-test-user)** - egy megfelelője B.Simon a Spotinst, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Spotinst alkalmazásintegrációs** lapon keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Jelölje be **a További URL-címek beállítása jelölőnégyzetet.**

    b. A **Továbbítási állapot** mezőbe írjon be egy értéket:`<ID>`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépéseket, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:`https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > A továbbítási állapot értéke nem valós. Frissíteni fogja a továbbítási állapot értékét a tényleges továbbítási állapot értékével, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Kattintson a **Mentés** gombra.

1. A Spotinst alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Spotinst alkalmazás arra számít, hogy néhány további attribútumot vissza kell adni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | -----| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.vezetéknév |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Spotinst beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Spotinst hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Spotinst**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-spotinst-sso"></a>Spotinst sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Spotinst biztonsági rendszergazdaként.

2. Kattintson a **felhasználó ikonjára** a képernyő jobb felső részén, és kattintson a **Beállítások gombra.**

    ![Spotinst beállítások](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Kattintson **a** biztonság fülre a tetején, majd válassza **az Identitásszolgáltatók lehetőséget,** és hajtsa végre a következő lépéseket:

    ![Spotinst biztonság](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Másolja **a példány továbbítási állapot** értékét, és illessze be a **továbbítási állapot** szövegmezőbe az Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Kattintson a **TALLÓ Gombra** az Azure Portalról letöltött metaadat-XML-fájl feltöltéséhez

    c. Kattintson **a MENTÉS gombra.**

### <a name="create-spotinst-test-user"></a>Spotinst tesztfelhasználó létrehozása

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű Britta Simon spotinst.

1. Ha az alkalmazást **sp** által kezdeményezett módban konfigurálta, hajtsa végre a következő lépéseket:

   a. Egy másik böngészőablakban jelentkezzen be a Spotinst biztonsági rendszergazdaként.

   b. Kattintson a **felhasználó ikonjára** a képernyő jobb felső részén, és kattintson a **Beállítások gombra.**

    ![Spotinst beállítások](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kattintson **a Felhasználók** gombra, és válassza **a FELHASZNÁLÓ HOZZÁADÁSA**lehetőséget.

    ![Spotinst beállítások](./media/spotinst-tutorial/adduser1.png)

    d. A Felhasználó hozzáadása szakaszban hajtsa végre az alábbi lépéseket:

    ![Spotinst beállítások](./media/spotinst-tutorial/adduser2.png)

    * A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például **BrittaSimon**.

    * Az **E-mail** mezőbe írja be a `brittasimon\@contoso.com`felhasználó e-mail címét, például .

    * Válassza ki a szervezetspecifikus adatokat a **szervezeti szerepkörhöz, a fiókszerepkörhöz és a fiókokhoz.**

2. Ha az alkalmazást az **IDP** által kezdeményezett módban konfigurálta, ebben a szakaszban nincs műveletelem. A Spotinst támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van. Új felhasználó jön létre a Spotinst elérésére tett kísérlet során, ha még nem létezik.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Spotinst csempére kattint, automatikusan be kell jelentkeznie arra a Spotinst-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Spotinst-ot az Azure AD-vel](https://aad.portal.azure.com/)

