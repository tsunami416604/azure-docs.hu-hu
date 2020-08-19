---
title: 'Oktatóanyag: Azure Active Directory-integráció az AHA-nal! | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és aha! között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.openlocfilehash: b11955c040225f66bc3eda9fe762cb1cbfc2d308
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542853"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Oktatóanyag: az AHA integrálása! Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az AHA-t! Azure Active Directory (Azure AD) szolgáltatással. Az AHA integrálásával! Az Azure AD-vel a következőket teheti:

* Az AHA-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezzenek az AHA-be! Az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Aha! egyszeri bejelentkezéses (SSO) engedélyezett előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Aha! az **SP** által kezdeményezett egyszeri bejelentkezés támogatása
* Aha! **a** felhasználó üzembe helyezésének támogatása

## <a name="adding-aha-from-the-gallery"></a>Aha hozzáadása! a katalógusból

Az AHA integrációjának konfigurálásához! Az Azure AD-ben fel kell vennie az AHA-t! a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **aha!** a keresőmezőbe.
1. Válassza az **aha lehetőséget!** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az AHA-ben!

Az Azure AD SSO konfigurálása és tesztelése az AHA-sel egy **B. Simon**nevű teszt felhasználó használata. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az AHA-beli kapcsolódó felhasználó között.

Az Azure AD SSO az AHA használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. Az **[aha konfigurálása! EGYSZERI](#configure-aha-sso)** bejelentkezés – az egyszeri bejelentkezési beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre aha! felhasználó tesztelése](#create-aha-test-user)** – hogy a B. Simon partnere legyen az AHA-ben! Ez a felhasználó Azure AD-képviseletéhez van társítva.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **aha!** az alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.aha.io/session/new`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.aha.io`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Contact [aha! Ügyfél-támogatási csapat](https://www.aha.io/company/contact) ezen értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **aha beállítása!** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az AHA-hez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **aha!** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-aha-sso"></a>Az AHA konfigurálása! SSO

1. Az AHA-en belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **beállítás aha gombra.** irányítja Önt az AHA-re! alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat az AHA-be való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha az AHA-et szeretné beállítani! Nyisson meg manuálisan egy új böngészőablakot, és jelentkezzen be az AHA-be! vállalati webhely rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A felső menüben kattintson a **Beállítások**elemre.

    ![Beállítások](./media/aha-tutorial/IC798950.png "Beállítások")

5. Kattintson a **fiók**lehetőségre.
  
    ![Profil](./media/aha-tutorial/IC798951.png "Profil")

6. Kattintson **a biztonság és az egyszeri bejelentkezés**lehetőségre.

    ![Biztonság és egyszeri bejelentkezés](./media/aha-tutorial/IC798952.png "Biztonság és egyszeri bejelentkezés")

7. Az **egyszeri bejelentkezés** szakaszban, az identitás- **szolgáltató**területen válassza az **SAML 2.0**elemet.

    ![Biztonság és egyszeri bejelentkezés](./media/aha-tutorial/IC798953.png "Biztonság és egyszeri bejelentkezés")

8. Az **egyszeri bejelentkezés** konfigurálása lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/aha-tutorial/IC798954.png "Egyszeri bejelentkezés")

    a. A **név** szövegmezőbe írja be a konfiguráció nevét.

    b. A **használatával történő konfiguráláshoz**válassza a **metaadat-fájl**lehetőséget.

    c. A letöltött metaadat-fájl feltöltéséhez kattintson a **Tallózás**gombra.

    d. Kattintson a **Frissítés** parancsra.

### <a name="create-aha-test-user"></a>Aha létrehozása teszt felhasználó

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre az AHA!-ben. Aha! támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik az AHA-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor rákattint az aha! csempe a hozzáférési panelen automatikusan be kell jelentkeznie az AHA-be! , amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

