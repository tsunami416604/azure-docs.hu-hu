---
title: 'Oktatóanyag: Azure Active Directory integráció a Atlassian-felhővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Atlassian-felhő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: bdd0530aa580f8f1a1cfdffb81d070827a714115
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686071"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Oktatóanyag: a Atlassian-felhő integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Atlassian-felhőt Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Atlassian-felhőt, a következőket teheti:

* A Atlassian-felhőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Atlassian felhőbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Atlassian-alapú Felhőbeli egyszeri bejelentkezés (SSO) engedélyezett előfizetés.
* A Atlassian Cloud Products szolgáltatáshoz való Security Assertion Markup Language (SAML) egyszeri bejelentkezés engedélyezéséhez be kell állítania a Atlassian-hozzáférést. További információ a [Atlassian-hozzáférésről]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

* A Atlassian Cloud támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A Atlassian Cloud támogatja [a felhasználók automatikus üzembe](atlassian-cloud-provisioning-tutorial.md) helyezését és megszüntetését
* A Atlassian-felhő konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian-felhő hozzáadása a gyűjteményből

A Atlassian Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Atlassian-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Atlassian Cloud** kifejezést a keresőmezőbe.
1. Válassza a **Atlassian Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Atlassian-felhővel egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Atlassian-felhőben.

Az Azure AD SSO Atlassian-felhővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad konfigurálása a Atlassian Cloud SSO](#configure-azure-ad-sso)** használatával – lehetővé teszi, hogy a felhasználók az Azure ad-alapú SAML SSO-t használják a Atlassian-felhővel.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Hozzon létre egy Atlassian Cloud test User](#create-atlassian-cloud-test-user)** -t, hogy a Atlassian-felhőben található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Atlassian-felhőben lévő konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, a **Atlassian-felhő beállítása** elemre kattintva megnyithatja a Atlassian Cloud alkalmazást. Itt adja meg a rendszergazdai hitelesítő adatokat a Atlassian-felhőbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Atlassian-felhőt, jelentkezzen be a Atlassian Cloud vállalati webhelyre rendszergazdaként, és hajtsa végre az alábbi lépéseket.

1. Mielőtt elkezdené a Atlassian, és másolja/mentse a példány URL-címét
   > [!NOTE]
   > az URL-címnek illeszkedő `https://<instancename>.atlassian.net` minta

   ![image](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Nyissa meg a [Atlassian felügyeleti portált](https://admin.atlassian.com/) , és kattintson a szervezet nevére

   ![image](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Az egyszeri bejelentkezés konfigurálása előtt ellenőriznie kell a tartományt. További információ: [Atlassian tartomány-ellenőrzési](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentum.
1. A Atlassian felügyeleti portál képernyőjén válassza a **Biztonság** lehetőséget a bal oldali navigációs ablakban.

   ![image](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. A Atlassian felügyeleti portál biztonsági képernyőjén válassza az **SAML egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablakban

   ![image](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Kattintson az **SAML-konfiguráció hozzáadása** elemre, és hagyja megnyitva a lapot

   ![image](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![image](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. A [Azure Portal](https://portal.azure.com/)a **Atlassian Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés beállítása**lehetőséget.

   ![image](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.

   ![image](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon görgessen le a Atlassian- **felhő beállításához**
   
   a. Kattintson a **konfigurációs URL-címek** elemre.

   ![image](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Másolja az **Azure ad-azonosító** értékét a Azure Portalból, illessze be az **Identity Provider entitás-azonosító** szövegmezőbe a Atlassian
   
   c. Másolja a **bejelentkezési URL-címet** a Azure Portalból, illessze be az **Identity Provider SSO URL** szövegmezőbe a Atlassian

   ![image](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![image](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![image](./media/atlassian-cloud-tutorial/certificate.png)

   ![image](./media/atlassian-cloud-tutorial/certificate-1.png)

1. SAML-konfiguráció **hozzáadása/mentése** a Atlassian-ben

1. Ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, szerkessze **Az Azure** -beli **egyszeri bejelentkezés beállítása az SAML-vel** lapon, és nyissa meg az **SAML egyszeri bejelentkezési oldalt** a Atlassian felügyeleti portálján.

   a. Másolja az **SP-entitás azonosítójának** értékét a Atlassian-ből, illessze be az Azure **azonosító (Entity ID)** mezőjébe, és állítsa be alapértelmezettként.
   
   b. Másolja az SP-t a **fogyasztói szolgáltatás URL-** címének értéke Atlassian, illessze be a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe az Azure-ban, és állítsa be alapértelmezettként
   
   c. Másolja ki a **példány URL-címét** , amelyet az 1. lépésben másolt, majd illessze be az Azure **Relay State (továbbítási állapot** ) mezőjébe

   ![image](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/urls.png)
   
1. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, szerkessze az Azure **-beli egyszeri bejelentkezés beállítása az SAML-vel** című **alapszintű SAML-konfiguráció** szakaszát. Másolja a **példány URL-címét** (az 1. lépésből), és illessze be a **bejelentkezési URL-cím** mezőbe az Azure-ban

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. A Atlassian Cloud Application egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az attribútum-hozzárendelés szerkesztéséhez kattintson a **Szerkesztés** ikonra. 

   ![image](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. Attribútum-hozzárendelés egy Office 365-licenccel rendelkező Azure AD-bérlőhöz
      
      a. Kattintson az **egyedi felhasználói azonosítóra (Name ID)** vonatkozó jogcímre

      ![image](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. A Atlassian Cloud elvárja, hogy a **NameIdentifier** (**egyedi felhasználói azonosító**) a felhasználó e-mail-címére (**User. e-mail**) legyen leképezve. Szerkessze a **forrás attribútumot** , és változtassa meg a **User. mail**-ben. Mentse a jogcím módosításait.

      ![image](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. A végső attribútum-hozzárendeléseknek a következőképpen kell kinéznie.

      ![image](common/default-attributes.png)
      
   1. Attribútum-hozzárendelés egy Office 365-licenccel nem rendelkező Azure AD-bérlőhöz 

      a. Kattintson a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` jogcímre.

      ![image](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Míg az Azure nem tölti fel a **User. mail** attribútumot az Azure ad-bérlők által az Office 365-licencek nélkül létrehozott felhasználók számára, és az e-maileket a **userPrincipalName** attribútumban tárolja az ilyen felhasználók számára. A Atlassian Cloud elvárja, hogy a **NameIdentifier** (**egyedi felhasználói azonosító**) a felhasználó e-mail-címére legyen leképezve (**User. userPrincipalName**).  Szerkessze a **forrás attribútumot**  , és módosítsa a **User. userPrincipalName**értékre. Mentse a jogcím módosításait.

      ![image](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. A végső attribútum-hozzárendeléseknek a következőképpen kell kinéznie.

      ![image](common/default-attributes.png)
     
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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Atlassian-felhőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Atlassian-felhő**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](./media/atlassian-cloud-tutorial/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud test-felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a Atlassian-felhőbe, a következő módon helyezze üzembe a felhasználói fiókokat manuálisan a Atlassian-felhőben:

1. Az **Adminisztráció** ablaktáblán válassza a **felhasználók**lehetőséget.

    ![A Atlassian Cloud Users hivatkozás](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Ha felhasználót szeretne létrehozni a Atlassian-felhőben, válassza a **felhasználó meghívása**lehetőséget.

    ![Atlassian felhőalapú felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Az **e-mail cím** mezőbe írja be a felhasználó e-mail-címét, majd rendelje hozzá az alkalmazáshoz való hozzáférést.

    ![Atlassian felhőalapú felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Ha e-mailben szeretne meghívót küldeni a felhasználónak, válassza a **felhasználók meghívása**lehetőséget. A rendszer elküld egy e-mailes meghívót a felhasználónak, és a meghívót elfogadva a felhasználó aktív a rendszeren.

> [!NOTE]
> A felhasználókat a **tömeges létrehozás** gomb kiválasztásával is létrehozhatja a **felhasználók** szakaszban.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Atlassian felhő csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Atlassian-felhőbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Atlassian-felhőt az Azure AD-vel](https://aad.portal.azure.com/)

- [A Atlassian-felhő elleni védelem speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)