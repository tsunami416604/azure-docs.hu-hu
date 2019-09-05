---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a JAMF Pro-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a JAMF Pro között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305431"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a JAMF Pro-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a JAMF Pro-t Azure Active Directory (Azure AD) használatával. Ha integrálja az JAMF Pro-t az Azure AD-vel, a következőket teheti:

* A JAMF Pro-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a JAMF Pro-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* JAMF Pro egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A JAMF Pro támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-jamf-pro-from-the-gallery"></a>A JAMF Pro hozzáadása a gyűjteményből

A JAMF Pro Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a JAMF Pro-t a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **JAMF Pro** kifejezést.
1. Válassza az **JAMF Pro** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a JAMF Pro szolgáltatásban

Konfigurálja és tesztelje az Azure AD SSO-t a JAMF Pro-val egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a JAMF Pro-ban.

Az Azure AD SSO a JAMF Pro-val való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[JAMF Pro SSO konfigurálása](#configure-jamf-pro-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy JAMF Pro test User](#create-jamf-pro-test-user)** -t, hogy a JAMF Pro-ban található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **JAMF Pro** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A tényleges azonosító értékét a JAMF Pro portál **egyszeri bejelentkezés** szakaszában találja, amelyet az oktatóanyag későbbi részében ismertetünk. Kibonthatja a tényleges **altartomány** értéket az azonosító értékből, és az **altartomány** információit használhatja a bejelentkezési URL-cím és a válasz URL-címében. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a JAMF Pro-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **JAMF Pro**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-jamf-pro-sso"></a>JAMF Pro SSO konfigurálása

1. A JAMF Pro-n belüli konfiguráció automatizálásához a **bővítmény telepítése**lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Setup JAMF Pro** lehetőségre a JAMF Pro alkalmazásban. A JAMF Pro-ba való bejelentkezéshez adja meg a rendszergazdai hitelesítő adatokat. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a JAMF Pro-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a JAMF Pro vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a lap jobb felső sarkában található **Beállítások ikonra** .

    ![JAMF Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Kattintson az **egyszeri bejelentkezés**lehetőségre.

    ![JAMF Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Az **egyszeri bejelentkezés** oldalon hajtsa végre a következő lépéseket:

    ![JAMF Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Jelölje be az **egyszeri bejelentkezéses hitelesítés engedélyezése**című részt.

    b. Válassza az **egyéb** lehetőséget az identitás- **szolgáltató** legördülő listából.

    c. A **másik szolgáltató** szövegmezőbe írja be az **Azure ad**-t.

    d. Másolja az **entitás-azonosító** értékét, és illessze be az **alapszintű SAML-konfiguráció** szakaszának **azonosító (entity ID)** szövegmezőbe Azure Portal.

    > [!NOTE]
    > Ebben az esetben ezt az értéket kell használnia a bejelentkezési URL-cím és a válasz URL-címének végrehajtásához a Azure Portal **alapszintű SAML-konfiguráció** szakaszában. `<SUBDOMAIN>`

    e. Válassza ki a **metaadatok URL-címét** lehetőségként az **Identitáskezelés metaadatainak forrása** legördülő listából, és a következő szövegmezőbe illessze be az **alkalmazás összevonási metaadat URL-címét** , amelyet a Azure Portal másolt.

7. Ugyanazon az oldalon görgessen le a **felhasználó-hozzárendelés** szakaszra, és hajtsa végre a következő lépéseket: 

    ![JAMF Pro Single](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Válassza a **NameID** lehetőséget az **identitás-szolgáltató felhasználói leképezéséhez**. Alapértelmezés szerint ez a beállítás a **NameID** értékre van állítva, de egyéni attribútumot is meghatározhat.

    b. Válassza az **e-mail** lehetőséget a **JAMF Pro felhasználói leképezéshez**. A JAMF Pro Maps SAML-attribútumokat a identitásszolgáltató a következő módokon küldik el: felhasználók és csoportok szerint. Amikor egy felhasználó megpróbál hozzáférni a JAMF Pro-hoz, alapértelmezés szerint a JAMF Pro beolvassa a felhasználó adatait az identitás-szolgáltatótól, és megfelel a JAMF Pro felhasználói fiókjainak. Ha a bejövő felhasználói fiók nem létezik a JAMF Pro-ban, akkor a csoport neve egyeztetés történik.

    c. Illessze be az `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` értéket az **Identity Provider csoport attribútumának neve** szövegmezőbe.

    d. Ha bejelöli **az egyszeri bejelentkezéses hitelesítés felhasználói megkerülésének engedélyezése a felhasználóknak** jelölőnégyzetet, a rendszer nem irányítja át az azonosító-szolgáltató bejelentkezési oldalára a hitelesítéshez, de közvetlenül is bejelentkezhet a JAMF Pro-ba. Ha a felhasználó az JAMF keresztül próbál hozzáférni a Pro-hoz, az identitásszolgáltató által kezdeményezett egyszeri bejelentkezéses hitelesítés és engedélyezés történik.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-jamf-pro-test-user"></a>JAMF Pro test-felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a JAMF Pro-ba, a JAMF Pro-ban kell kiépíteni őket. A JAMF Pro esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JAMF Pro vállalati webhelyre rendszergazdaként.

2. Kattintson a lap jobb felső sarkában található **Beállítások ikonra** .

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Kattintson a **JAMF Pro felhasználói fiókok & csoportok**elemre.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user1.png)

4. Kattintson az **Új** lehetőségre.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user2.png)

5. Válassza a **normál fiók létrehozása**lehetőséget.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user3.png)

6. Az **új fiók** dailog hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user4.png)

    a. A **Felhasználónév** szövegmezőbe írja be a BrittaSimon teljes nevét.

    b. Válassza ki a megfelelő beállításokat a szervezete számára a **hozzáférési szint**, a **jogosultság-készlet**és a **hozzáférési állapot**számára.

    c. A **teljes név** szövegmezőbe írja be a Simon Britta teljes nevét.

    d. Az **E-mail cím** szövegmezőbe írja be a Britta Simon-fiók e-mail-címét.

    e. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    f. A **jelszó ellenőrzése** szövegmezőbe írja be a felhasználó jelszavát.

    g. Kattintson a **Save** (Mentés) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a JAMF Pro csempére kattint, automatikusan be kell jelentkeznie a JAMF Pro-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A JAMF Pro kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

