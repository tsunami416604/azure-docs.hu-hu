---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Salesforce | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a875cee7e6796a2c865bde4a62f2f0463eb12130
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967734"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Salesforce

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Salesforce a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Salesforce-t az Azure AD-vel, a következőket teheti:

* A Salesforce-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Salesforce az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Salesforce egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Salesforce támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Salesforce támogatja [a felhasználók **automatikus** üzembe](salesforce-provisioning-tutorial.md) helyezését és megszüntetését (ajánlott)

* A Salesforce **csak időben támogatja a** felhasználók kiépítési folyamatát

* Az Salesforce Mobile Application mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.
* A Salesforce konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce hozzáadása a gyűjteményből

A Salesforce Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Salesforce a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Salesforce** kifejezést a keresőmezőbe.
1. Válassza ki a **Salesforce** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Salesforce

Konfigurálja és tesztelje az Azure AD SSO-t a Salesforce a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Salesforce-ben.

Az Azure AD SSO és a Salesforce konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SALESFORCE SSO konfigurálása](#configure-salesforce-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Salesforce-teszt felhasználót](#create-salesforce-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Salesforce rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Salesforce** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Az **azonosító** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a Salesforce ügyfélszolgálati [csapatához](https://help.salesforce.com/support) .

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A **Salesforce beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Salesforce.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Salesforce**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-salesforce-sso"></a>Salesforce SSO konfigurálása

1. A Salesforce belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, a **Salesforce beállítása** lehetőségre kattintva megnyithatja az Salesforce egyszeri bejelentkezés alkalmazást. Itt adja meg a rendszergazdai hitelesítő adatokat az Salesforce egyszeri bejelentkezésre való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-13-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Salesforce, nyisson meg egy új böngészőablakot, és jelentkezzen be a Salesforce vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson **az oldal** jobb felső sarkában található **Beállítások ikonra** .

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/configure1.png)

1. Görgessen le a **beállításokhoz** a navigációs ablaktáblán, és kattintson az **Identity (identitás** ) elemre a kapcsolódó szakasz kibontásához. Ezután kattintson az **egyszeri bejelentkezési beállítások**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso.png)

1. Az **egyszeri bejelentkezési beállítások** lapon kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni az egyszeri bejelentkezési beállításokat a Salesforce-fiókjához, előfordulhat, hogy kapcsolatba kell lépnie a [Salesforce-ügyfél támogatási csoportjával](https://help.salesforce.com/support).

1. Válassza az **SAML engedélyezve**lehetőséget, majd kattintson a **Mentés**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-enable-saml.png)

1. Az SAML egyszeri bejelentkezés beállításainak konfigurálásához kattintson **az új elemre a metaadat-fájlból**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portal letöltött metaadat XML-fájl feltöltéséhez, majd kattintson a **Létrehozás**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/xmlchoose.png)

1. Az **SAML egyszeri bejelentkezés beállításai** lapon a mezők automatikusan frissülnek, válassza ki a felhasználó által **engedélyezett kiépítés** elemet, majd kattintson a **Mentés**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/salesforcexml.png)

1. A Salesforce bal oldali navigációs paneljén kattintson a **Vállalati beállítások** elemre a kapcsolódó szakasz kibontásához, majd kattintson **a saját tartomány**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-my-domain.png)

1. Görgessen le a **hitelesítési konfiguráció** szakaszhoz, és kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. A **hitelesítési konfiguráció** szakaszban tekintse meg az SAML SSO-konfiguráció **AzureSSO** **hitelesítési szolgáltatásként** , majd kattintson a **Mentés**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás van kiválasztva, a rendszer a felhasználókat arra kéri, hogy válassza ki, hogy melyik hitelesítési szolgáltatást szeretnék bejelentkezni a Salesforce-környezetbe való egyszeri bejelentkezés kezdeményezése során. Ha nem szeretné, hogy megtörténjen, az **összes többi hitelesítési szolgáltatást ne jelölje be**.

### <a name="create-salesforce-test-user"></a>Salesforce-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Salesforce-ben. A Salesforce támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Salesforce-ben, akkor létrejön egy új, amikor megpróbál hozzáférni a Salesforce. A Salesforce az automatikus felhasználó-kiépítés használatát is támogatja, további részleteket [itt](salesforce-provisioning-tutorial.md) talál az automatikus felhasználó-kiépítés konfigurálásával kapcsolatban.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Salesforce csempére kattint, automatikusan be kell jelentkeznie arra a Salesforce, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Salesforce-teszt (SSO) tesztelése (mobil)

1. Nyissa meg a Salesforce Mobile Application alkalmazást. A bejelentkezés lapon kattintson az **egyéni tartomány használata**lehetőségre.

    ![Salesforce Mobile-alkalmazás](media/salesforce-tutorial/mobile-app1.png)

1. Az **egyéni tartomány** szövegmezőbe írja be a regisztrált egyéni tartománynevet, és kattintson a **Folytatás**gombra.

    ![Salesforce Mobile-alkalmazás](media/salesforce-tutorial/mobile-app2.png)

1. Adja meg az Azure AD-beli hitelesítő adatait, hogy bejelentkezzen a Salesforce alkalmazásba, és kattintson a **tovább**gombra.

    ![Salesforce Mobile-alkalmazás](media/salesforce-tutorial/mobile-app3.png)

1. A **hozzáférés engedélyezése** lapon az alább látható módon kattintson az **Engedélyezés** lehetőségre a Salesforce alkalmazáshoz való hozzáféréshez.

    ![Salesforce Mobile-alkalmazás](media/salesforce-tutorial/mobile-app4.png)

1. Végül a sikeres bejelentkezés után megjelenik az alkalmazás kezdőlapja.

    ![Salesforce Mobile App](media/salesforce-tutorial/mobile-app5.png) ![Salesforce Mobile App](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A felhasználók üzembe helyezésének konfigurálása](salesforce-provisioning-tutorial.md)

- [A Salesforce kipróbálása az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Salesforce és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/protect-salesforce)