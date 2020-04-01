---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a LinkedIn Learningprogrammal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a LinkedIn Learning között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15509866980cdf85f54fc03cb77eed36f83c982f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983367"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a LinkedIn Learninggel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a LinkedIn Learninget az Azure Active Directoryval (Azure AD). Ha integrálja a LinkedIn Learninget az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a LinkedIn Learninghez.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezhessenek a LinkedIn Learningbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* LinkedIn Learning egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A LinkedIn Learning támogatja az **SP és az IDP** által kezdeményezett sso-t
* A LinkedIn Learning támogatja **a Just In Time** felhasználói kiépítést
* A LinkedIn Learning konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>LinkedIn Learning hozzáadása a galériából

A LinkedIn Learning Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a LinkedIn Learninget a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **LinkedIn Learning** kifejezést a keresőmezőbe.
1. Válassza a **LinkedIn Learning** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a LinkedIn Learning hez

Konfigurálja és tesztelje az Azure AD SSO-t a LinkedIn Learning segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a linkedin learning kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a LinkedIn Learning segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a LinkedIn Learning Egyszeri bejelentkezési kiszolgálót](#configure-linkedin-learning-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[LinkedIn Learning-tesztfelhasználó létrehozása](#create-linkedin-learning-test-user)** – b.Simon megfelelője a LinkedIn Learningben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **LinkedIn Learning alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

     a. Az **Azonosító** mezőbe írja be a LinkedIn Portalról másolt **entitásazonosítót.** 

    b. A **Válasz URL-cím** mezőjébe írja be a LinkedIn Portalról másolt **helyességifeltétel-fogyasztói szolgáltatás (ACS) URL-címét.**

    c. Ha az alkalmazást SP kezdeményezett módban szeretné **konfigurálni,** kattintson a **További URL-ek beállítása** elemre az Egyszerű **SAML-konfiguráció** szakaszban, ahol meg kell adnia a bejelentkezési URL-címet. A bejelentkezési URL létrehozásához másolja a **helyességi feltétel fogyasztói szolgáltatás (ACS) URL-címét,** és cserélje le a /saml/ kapcsolót a /login/ értékre. Miután ez megtörtént, a bejelentkezési URL-címnek a következő mintával kell rendelkeznie:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn tanulási tartomány és URL-címek egyszeri bejelentkezési információi](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Ezek az értékek nem valós értékek. Ezeket az értékeket a tényleges azonosító és válasz URL-címével fogja frissíteni, amelyet később az oktatóanyag **LinkedIn Learning SSO konfigurálása** című szakaszában ismertetünk.

1. A LinkedIn Learning alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. A LinkedIn Learning alkalmazás elvárja, hogy a **névazonosító** le legyen képezve **a user.mail programmal,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva és az attribútumleképezés módosításával kell módosítania.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **LinkedIn Learning beállítása csoportban** másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a LinkedIn Learning hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **LinkedIn Learning**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-linkedin-learning-sso"></a>Linkedin learning sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LinkedIn Learning-bérlőhöz rendszergazdaként.

2. A **Fiókközpontban**kattintson a **Globális beállítások** elemre a Beállítások **csoportban.** A legördülő listából válassza a **Tanulás – Alapértelmezett** lehetőséget is.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Kattintson **a VAGY ide gombra az egyes mezők betöltéséhez és másolásához az űrlapról,** és másolja az Entitásazonosító és az **ACS** **url-címét,** majd illessze be az Azure Portal **alapszintű SAML-konfigurációja** szakaszába.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Nyissa meg a **LinkedIn felügyeleti beállítások szakaszát.** Töltse fel az Azure Portalról letöltött XML-fájlt az **XML-fájl feltöltése** beállításra kattintva.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Kattintson **a Be gombra** az SSO engedélyezéséhez. Az sso állapota a **nincs csatlakoztatva** állapotról **a csatlakoztatottra változik**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>LinkedIn-learning tesztfelhasználó létrehozása

LinkedIn Learning alkalmazás támogatja a Just in time felhasználói kiépítés és hitelesítés után a felhasználók jönnek létre az alkalmazásban automatikusan. A LinkedIn Learning portál rendszergazdai beállítások lapján kapcsolja be a **licencek automatikus hozzárendelése** az aktív Just in time provisioning kapcsolót, és ez licencet is hozzárendel a felhasználóhoz.

   ![Azure AD-tesztfelhasználó létrehozása](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a LinkedIn Learning csempére kattint, automatikusan be kell jelentkeznie a LinkedIn Learningbe, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a LinkedIn Learning et az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)