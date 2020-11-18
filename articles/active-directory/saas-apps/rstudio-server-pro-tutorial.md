---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a RStudio Server Pro SAML-hitelesítéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és RStudio Server Pro SAML-hitelesítés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: abd65fc0cfd1f1c563ac9bd68b6ba58b741e152b
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94881197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro-saml-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a RStudio Server Pro SAML-hitelesítéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a RStudio Server Pro SAML-hitelesítést Azure Active Directory (Azure AD) használatával. Ha integrálja az RStudio Server Pro SAML-hitelesítést az Azure AD-vel, a következőket teheti:

* A RStudio Server Pro SAML-hitelesítéshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a RStudio Server Pro SAML-Hitelesítésbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* RStudio Server Pro SAML-hitelesítés egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A RStudio Server Pro SAML-hitelesítés támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-rstudio-server-pro-saml-authentication-from-the-gallery"></a>RStudio Server Pro SAML-hitelesítés hozzáadása a katalógusból

A RStudio Server Pro SAML-hitelesítés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a RStudio Server Pro SAML-hitelesítést a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **RSTUDIO Server Pro SAML-hitelesítés** kifejezést a keresőmezőbe.
1. Válassza az **RStudio Server Pro SAML-hitelesítés** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro-saml-authentication"></a>Az Azure AD SSO konfigurálása és tesztelése a RStudio Server Pro SAML-hitelesítéshez

Konfigurálja és tesztelje az Azure AD SSO-t a RStudio Server Pro SAML-hitelesítéssel egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a RStudio Server Pro SAML-hitelesítésben.

Az Azure AD SSO RStudio Server Pro SAML-hitelesítéssel történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[RStudio Server Pro SAML-hitelesítésének konfigurálása](#configure-rstudio-server-pro-saml-authentication-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy RStudio Server Pro SAML hitelesítési teszt felhasználót](#create-rstudio-server-pro-saml-authentication-test-user)** – ha a RStudio Server Pro SAML-hitelesítéssel rendelkezik, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **RStudio Server Pro SAML Authentication** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/acs`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.rstudioservices.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot a [RStudio Server Pro SAML hitelesítési](mailto:support@rstudio.com) ügyfélszolgálatával, hogy lekérje ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a RStudio Server Pro SAML-hitelesítéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **RStudio Server Pro SAML-hitelesítés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-rstudio-server-pro-saml-authentication-sso"></a>RStudio Server Pro SAML-hitelesítés konfigurálása SSO

Ha egyszeri bejelentkezést szeretne konfigurálni a **RStudio Server Pro SAML hitelesítési** oldalán, el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [RStudio Server Pro SAML-hitelesítés támogatási csapatának](mailto:support@rstudio.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-rstudio-server-pro-saml-authentication-test-user"></a>RStudio Server Pro SAML hitelesítési teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a RStudio Server Pro SAML-hitelesítésben. Együttműködik a [RStudio Server Pro SAML-hitelesítési támogatási csapatával](mailto:support@rstudio.com) , és felveheti a felhasználókat a RStudio Server Pro SAML hitelesítési platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a RStudio Server Pro SAML-hitelesítés bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a RStudio Server Pro SAML-hitelesítés bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie a RStudio Server Pro SAML-hitelesítésbe, amelyhez be kell állítania az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen a RStudio Server Pro SAML-hitelesítés csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a RStudio Server Pro SAML-Hitelesítésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Következő lépések

A RStudio Server Pro SAML-hitelesítés konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).