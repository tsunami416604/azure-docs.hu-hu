---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Cloud for Customer szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP-felhő között az ügyfelek számára.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72264061"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Cloud-nal az ügyfelek számára

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Cloud ügyfelet Azure Active Directory (Azure AD) használatával. Az SAP Cloud Azure AD-vel való integrálásakor a következőket teheti:

* Az SAP Cloud for Customer szolgáltatáshoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával rendelkező ügyfelek számára automatikusan bejelentkezzenek az SAP-felhőbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP-felhő az ügyfél egyszeri bejelentkezési (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP Cloud for Customer támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP-felhő hozzáadása az ügyfélhez a katalógusból

Az SAP Cloud az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Cloud for Customer szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SAP Cloud for Customer** kifejezést a keresőmezőbe.
1. Válassza az **SAP Cloud az ügyfelek számára** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az SAP Cloud ügyfél számára

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Cloud for Customer használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Cloud for Customer szolgáltatásban.

Az Azure AD SSO és az SAP Cloud az ügyfél számára történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[SAP Cloud beállítása az ügyfél egyszeri](#configure-sap-cloud-for-customer-sso)** bejelentkezéséhez – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
    1. **[SAP-felhő létrehozása az ügyfél-tesztelési felhasználó](#create-sap-cloud-for-customer-test-user)** számára – ha az SAP-felhőben található, a felhasználó Azure ad-képviseletéhez kapcsolódó ügyfélhez tartozó B. Simon partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **SAP Cloud for Customer** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server name>.crm.ondemand.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot [az SAP Cloud ügyfélszolgálatával az ügyfelek ügyfélszolgálati csapatával](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az ügyfél-alkalmazáshoz készült SAP-felhő adott formátumban elvárja az SAML-állításokat, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A felhasználói **attribútumok** **& a jogcímek** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Válassza **Transformation** a transzformáció **forrásként**lehetőséget.

    c. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    d. Az **1. paraméter** listából válassza ki azt a felhasználói attribútumot, amelyet a megvalósításához használni kíván.
    Ha például az Alkalmazottkód-et egyedi felhasználói azonosítóként szeretné használni, és az attribútum értékét a ExtensionAttribute2 tárolja, válassza a User. ExtensionAttribute2 elemet.

    e. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP-felhő beállítása ügyfél számára** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést az SAP Cloud for Customer elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **SAP Cloud az ügyfél számára**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP-felhő konfigurálása az ügyfél egyszeri bejelentkezéséhez

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP-felhőbe az ügyfél vállalati webhelyén rendszergazdaként.

2. A menü bal oldalán kattintson az **Identitáskezelő** > **vállalati identitás-szolgáltatók** > **Hozzáadás** elemre, majd az előugró ablakban adja meg az identitás-szolgáltató nevét (például az **Azure ad**-t), kattintson a **Mentés** , majd a **SAML 2,0-konfiguráció**elemre.

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure01.png)

3. A **SAML 2,0 konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Kattintson a **Tallózás** gombra az összevonási metaadatok XML-fájljának feltöltéséhez, amelyet a Azure Portal letöltött.

    b. Az XML-fájl feltöltése után az alábbi értékek automatikusan feltöltve lesznek automatikusan, majd a **Mentés**gombra kattintanak.

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP-felhő létrehozása az ügyfél-tesztelési felhasználó számára

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az SAP-felhőbe az ügyfelek számára, az SAP-felhőben kell kiépíteni az ügyfelet. Az SAP Cloud for Customer esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az SAP-felhőbe az ügyfél biztonsági rendszergazdájaként.

2. A menü bal oldalán kattintson a **felhasználók & engedélyek** > **felhasználói kezelés** > felhasználó**hozzáadása**elemre.

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure03.png)

3. Az **új felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Az **Utónév** szövegmezőbe írja be a felhasználó nevét (például **B**).

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó nevét, például **Simon**.

    c. Az **e-mail** szövege mezőben adja meg a felhasználóhoz hasonló `B.Simon@contoso.com`e-mail címet.

    d. A **bejelentkezési név** szövegmezőbe írja be a (z **) B. Simon**nevű felhasználó nevét.

    e. Adja meg a **felhasználó típusát** a követelménynek megfelelően.

    f. Igény szerint válassza a **fiók aktiválása** lehetőséget.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panel SAP-felhő ügyfél csempére kattint, automatikusan be kell jelentkeznie az SAP-felhőbe az ügyfél számára, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az SAP Cloud kipróbálása az Azure AD-vel rendelkező ügyfelek számára](https://aad.portal.azure.com/)

