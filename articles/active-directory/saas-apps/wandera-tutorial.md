---
title: 'Oktatóanyag: Azure Active Directory-integráció a Wanda szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Wanda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.openlocfilehash: 4af2fa415c22abe5cd8ac98d510feb44abab0e97
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523983"
---
# <a name="tutorial-integrate-wandera-with-azure-active-directory"></a>Oktatóanyag: a Bolyongás integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Wanderers-t Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Wanda-t, a következőket teheti:

* A Bolyongás elérését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való Barangolásba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A vándor egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Wanda támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-wandera-from-the-gallery"></a>Bolyongás hozzáadása a katalógusból

A Bolyongás Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Bolyongás** kifejezést a keresőmezőbe.
1. Válassza a **Bolyongás** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Wander használatával egy **B. Simon**nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a Bolyongás kapcsolódó felhasználója között.

Az Azure AD SSO és a Wander használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. A **[Bolyongás SSO konfigurálása](#configure-wandera-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. A **[Wanda-tesztelési felhasználó létrehozása](#create-wandera-test-user)** – ha a Wanda-ben a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli párja van.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Wanda** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a [Wanda ügyfél-támogatási csapatával](https://www.wandera.com/about-wandera/contact/#supportsection) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **SAML-aláíró tanúsítvány** szerkesztése/toll ikonjára a beállítások szerkesztéséhez.

    ![Aláírási beállítás](common/signing-option.png)

    1. Válassza az **aláírás lehetőséget** az **SAML-válasz és az állítás aláírásához**.

    1. Válassza az **aláírási algoritmust** **SHA-256ként**.

1. A **Bolyongás beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-wandera-sso"></a>A Bolyongás SSO konfigurálása

1. A Bolyongás konfigurációjának automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a bekapcsolás lehetőségre **a Wanda** alkalmazásban. Itt adja meg a Barangolásba való bejelentkezéshez szükséges rendszergazdai hitelesítő adatokat. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-4-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a barangolást, nyisson meg egy új böngészőablakot, és jelentkezzen be a Wanda vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső sarkában kattintson a **Beállítások**  >  **felügyelet**  >  **egyszeri bejelentkezés** elemre, majd jelölje be a **SAML 2,0 engedélyezése** a következő lépések végrehajtásához lehetőséget.

    ![A Wanda konfigurálása](./media/wandera-tutorial/config01.png)

    a. Kattintson **vagy manuálisan adja meg a kötelező mezőket**.

    b. A **identitásszolgáltató EntityId** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    c. Nyissa meg az összevonási metaadatok XML-fájlját a Jegyzettömbben, másolja a tartalmát, és illessze be a **identitásszolgáltató Public X. 509 tanúsítvány** szövegmezőbe.

    d. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Wanda elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Bolyongás**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-wandera-test-user"></a>A Wanda-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Wanda-ben. A Wanda- [támogatási csapattal](https://www.wandera.com/about-wandera/contact/#supportsection) együtt veheti fel a felhasználókat a Wanda platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Wanda csempére kattint, automatikusan be kell jelentkeznie arra a vándorra, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

