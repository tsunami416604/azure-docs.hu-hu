---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Lenses.io | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Lenses.io között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Lenses.io DataOps-portálon.

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a [Lenses.IO](https://lenses.io/) DataOps portált Azure Active Directory (Azure ad) használatával. Ha integrálja az Lenses.io-t az Azure AD-vel, a következőket teheti:

* A Lenses.io-portálhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a lencsékre az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A lencsék portál egy példánya. [Többféle módon](https://lenses.io/product/deployment/)is üzembe helyezhet egy objektívek-portált.
* Egyszeri bejelentkezést (SSO) támogató Lenses.io- [licenc](https://lenses.io/product/pricing/) .

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Lenses.io támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Lenses.io konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Lenses.io hozzáadása a gyűjteményből

A Lenses.io Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Lenses.io a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Lenses.IO** kifejezést a keresőmezőbe.
1. Válassza ki a **Lenses.IO** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Azure AD SSO konfigurálása és tesztelése a Lenses.io-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Lenses.io-portálon egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Lenses.io-ben.

Az Azure AD SSO és a Lenses.io konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Hozzon létre egy Azure ad-teszt felhasználót és csoportot](#create-an-azure-ad-test-user-and-group)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[LENSES.IO SSO konfigurálása](#configure-lensesio-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Lenses.IO-tesztelési csoport engedélyeinek létrehozása](#create-lensesio-test-group-permissions)** – annak szabályozására, hogy a B. Simon milyen mértékben férhessen hozzá a Lenses.IO (engedélyezéshez).
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Lenses.IO** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CUSTOMER_LENSES_BASE_URL>` például `https://lenses.my.company.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CUSTOMER_LENSES_BASE_URL>` például `https://lenses.my.company.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    emelkedés pl `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, a válasz URL-címével és az azonosítóval a lencsék-portál példányának alap URL-címe alapján. További információt a [LENSES.IO SSO dokumentációjában](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)talál.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Lenses.IO beállítása** szakaszban a fenti XML-fájl segítségével konfigurálja a lencséket az Azure SSO-ra.

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD-teszt felhasználó és csoport létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven. Emellett létre fog hozni egy tesztelési csoportot is a B. Simon számára, amelyet a B. Simon elérésének szabályozására használ a lencsékben.
Azt is megtudhatja, hogyan használja a lencsék csoporttagság-leképezést a [lencsék SSO-dokumentációjában](https://docs.lenses.io/install_setup/configuration/security.html#id3)

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

A csoport létrehozása:
1. Lépjen vissza a **Azure Active Directoryra**, majd válassza a **csoportok** lehetőséget.
1. Válassza az **új csoport** lehetőséget a képernyő tetején.
1. A **csoport tulajdonságainál**kövesse az alábbi lépéseket:
   1. A **csoport típusa** mezőben válassza a elemet `Security` .
   1. A **csoport neve** mezőbe írja be a `LensesUsers`
   1. Kattintson a **Create** (Létrehozás) gombra.
1. Válassza ki a csoportot `LensesUsers` , és jegyezze fel az **objektum azonosítóját** (például `f8b5c1ec-45de-4abd-af5c-e874091fb5f7` ). Ezt az azonosítót fogja használni a lencsékben a csoport felhasználóinak a [megfelelő engedélyekkel](https://docs.lenses.io/install_setup/configuration/security.html#id3)való leképezéséhez.  
   
A csoport társítása a teszt felhasználóhoz: 
1. Lépjen vissza **Azure Active Directoryre**, majd válassza a **felhasználók**lehetőséget.
1. Válassza ki a felhasználó tesztelését `B.Simon` .
1. Válassza a **csoportok**lehetőséget.
1. Válassza a **tagság hozzáadása** lehetőséget a képernyő tetején.
1. Keresse `LensesUsers` meg és jelölje ki.
1. Kattintson a **Kiválasztás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Lenses.io.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Lenses.IO**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-lensesio-sso"></a>Lenses.io SSO konfigurálása

Az egyszeri bejelentkezés az **Lenses.IO** -portálon való konfigurálásához telepítse a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** a lencsék-példányra, és [konfigurálja a lencséket az egyszeri bejelentkezés engedélyezéséhez](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io-tesztelési csoport engedélyeinek létrehozása

Ebben a szakaszban egy, a **Object Id** `LensesUsers` felhasználó [létrehozása szakaszban](#create-an-azure-ad-test-user-and-group)feljegyzett csoport objektumazonosító alapján hozzon létre egy csoportot a lencséken.
A kívánt engedélyeket a `B.Simon` lencsékben kell megadni.
További információkat az [Azure-objektívek csoport leképezéséről](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)talál.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Lenses.io csempére kattint, automatikusan be kell jelentkeznie a Lenses.io-portálra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Egyszeri bejelentkezés beállítása a Lenses.io-példányban ](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Lenses.io kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Lenses.io és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
