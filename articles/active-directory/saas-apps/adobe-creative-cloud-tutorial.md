---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Adobe Creative Cloudszolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Adobe Creative Cloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25dd638c15fecbef787e4ceabea9ae7cb4359582
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Adobe Creative Cloudszolgáltatással

> [!NOTE]
> Ez a cikk az Adobe Admin Console egyéni SAML-alapú beállítását ismerteti az Azure Active Directoryhoz (Azure AD). Vadonatúj konfigurációk esetén azt javasoljuk, hogy használja az [Azure AD-összekötőt.](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html) Az Azure AD Connector percek alatt beállítható, és lerövidíti a tartományi jogcím, egyszeri bejelentkezés beállításának és a felhasználói szinkronizálás folyamatának.

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Adobe Creative Cloudot az Azure Active Directoryval (Azure AD). Ha integrálja az Adobe Creative Cloud szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá az Adobe Creative Cloud szolgáltatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek az Adobe Creative Cloudba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Az Adobe Creative Cloud egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Adobe Creative Cloud támogatja az **SP** által kezdeményezett sso-t





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud hozzáadása a galériából

Az Adobe Creative Cloud Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Adobe Creative Cloud-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be az **Adobe Creative Cloud** kifejezést a keresőmezőbe.
1. Válassza az **Adobe Creative Cloud** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az Adobe Creative Cloud szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t az Adobe Creative Cloud segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az Adobe Creative Cloudban.

Az Azure AD SSO adobe creative cloud szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Adobe Creative Cloud Egyszeri bejelentkezést](#configure-adobe-creative-cloud-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Adobe Creative Cloud tesztfelhasználót](#create-adobe-creative-cloud-test-user)** – ha b.Simon megfelelője az Adobe Creative Cloud ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Adobe Creative Cloud** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezés az URL-cím** mezőbe írjon be egy URL-címet:`https://adobe.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Az azonosító értéke nem valós. Frissítse ezt az értéket a tényleges azonosítóval. Az érték értéshez lépjen kapcsolatba az [Adobe Creative Cloud-ügyféltámogatási csapatával.](https://www.adobe.com/au/creativecloud/business/teams/plans.html) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az Adobe Creative Cloud alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül az Adobe Creative Cloud alkalmazás azt várja, hogy néhány további attribútum ot visszaadjon az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.

    | Név | Forrás attribútuma|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.vezetéknév |
    | E-mail | user.mail |

    > [!NOTE]
    > A felhasználóknak érvényes Office 365 ExO-licenccel kell rendelkezniük ahhoz, hogy az e-mail-jogcímértéke fel legyen töltve az SAML-válaszban.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány csoportban keresse meg az **összevonási** **adatok XML-fájlját,** majd válassza a **Letöltés** lehetőséget az XML-metaadatfájl letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **Adobe Creative Cloud beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezheti b.Simon számára, hogy egyetlen bejelentkezéssel használja az Azure-t az Adobe Creative Cloud szolgáltatáshoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Adobe Creative Cloud**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-adobe-creative-cloud-sso"></a>Az Adobe Creative Cloud sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az [Adobe Felügyeleti konzolba](https://adminconsole.adobe.com) rendszergazdaként.

1. Nyissa meg a felső navigációs sáv **Beállítások** gombját, és válassza az **Identitás**lehetőséget. Megnyílik a könyvtárak listája. Válassza ki a kívánt Federated könyvtárat.

1. A **Címtár részletei** lapon válassza a **Configure (Konfigurálás)** lehetőséget.

1. Másolja az entitásazonosítót és az ACS URL-címét (helyességi feltétel fogyasztói szolgáltatás URL-címe vagy válasz URL-címe). Adja meg az URL-címeket az Azure Portal megfelelő mezőiben.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Használja azt az Entitásazonosító-értéket, amelyet az Adobe **megadott önnek az Azonosítóhoz** az **Alkalmazásbeállítások konfigurálása** párbeszédpanelen.

    b. Használja az Adobe által az **Alkalmazásbeállítások konfigurálása** **párbeszédpanelen** megadott ACS URL-url (Helyességi feltétel fogyasztói szolgáltatás URL-címét).

1. A lap alján töltse fel az Azure Portalról letöltött **összevonási adatok XML-fájlját.** 

    ![Összevonási adatok XML-fájlja](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP metaadatok XML-fájlja")

1. Kattintson a **Mentés** gombra.


### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud-tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az Adobe Creative Cloud szolgáltatásba, ki kell építeni őket az Adobe Creative Cloud szolgáltatásba. Az Adobe Creative Cloud esetében a kiépítés manuális feladat.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Felhasználói fiókok létesítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be rendszergazdaként az [Adobe Felügyeleti konzol](https://adminconsole.adobe.com) webhelyére.

2. Adja hozzá a felhasználót az Adobe konzolján összevont azonosítóként, és rendelje hozzá egy termékprofilhoz. A felhasználók hozzáadásáról a [Felhasználók hozzáadása az Adobe Felügyeleti konzolon című témakörben](https://helpx.adobe.com/enterprise/using/users.html#Addusers) talál részletes információt. 

3. Ezen a ponton írja be az e-mail címét/upn-ját az Adobe-bejelentkezési űrlapon, nyomja meg a lapot, és vissza kell igazolnia az Azure AD-hez:
   * Webes hozzáférés:\.www adobe.com > bejelentkezés
   * Az asztali alkalmazás segédprogramjában > bejelentkezés
   * Az alkalmazáson belül > segítséget > a bejelentkezéshez

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Adobe Creative Cloud csempére kattint, automatikusan be kell jelentkeznie az Adobe Creative Cloud szolgáltatásba, amelyhez beállítja az sso-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Adobe Creative Cloud szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Identitás beállítása (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Az Azure konfigurálása az Adobe SSO -val való használatra (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

