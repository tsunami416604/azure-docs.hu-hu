---
title: 'Oktatóanyag: Azure Active Directory integráció a Mimecast felügyeleti konzollal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Mimecast felügyeleti konzolja között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: f3029acd791e7c45eb5943d298189430ac308e99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88528471"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Mimecast felügyeleti konzollal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Mimecast felügyeleti konzolt Azure Active Directory (Azure AD) használatával. A Mimecast felügyeleti konzol Azure AD-vel való integrálásakor a következőket teheti:

* A Mimecast felügyeleti konzolhoz hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek a Mimecast felügyeleti konzolba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Mimecast felügyeleti konzol egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Mimecast felügyeleti konzol támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A Mimecast felügyeleti konzol konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Mimecast felügyeleti konzol hozzáadása a gyűjteményből

A Mimecast felügyeleti konzol Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mimecast felügyeleti konzolt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Mimecast felügyeleti konzol** kifejezést a keresőmezőbe.
1. Válassza ki a **Mimecast felügyeleti konzolját** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-admin-console"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Mimecast felügyeleti konzolon

Konfigurálja és tesztelje az Azure AD SSO-t a Mimecast felügyeleti konzollal a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Mimecast felügyeleti konzolon.

Az Azure AD SSO és a Mimecast felügyeleti konzol konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Mimecast felügyeleti konzol egyszeri bejelentkezésének konfigurálása](#configure-mimecast-admin-console-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[Mimecast felügyeleti konzoljának tesztelése felhasználó](#create-mimecast-admin-console-test-user)** – ha a Mimecast felügyeleti konzolon található, a felhasználó Azure ad-képviseletéhez csatolt B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Mimecast felügyeleti konzol** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást identitásszolgáltató kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Region  |  Érték | 
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Egyesült Államok   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Ausztrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore szoftverfejlesztési        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > A `accountcode` Mimecast felügyeleti konzolon található értéket a **Fiókbeállítások**  >  **Settings**  >  **fiók kódja**területen találja. Fűzze hozzá az `accountcode` azonosítót az azonosítóhoz.

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet: 

    | Region  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-afrikai Köztársaság    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore szoftverfejlesztési        | `https://jer-api.mimecast.com/login/saml`|

1. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet: 

    | Region  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | Egyesült Államok   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | Dél-afrikai Köztársaság    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Ausztrália       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Offshore szoftverfejlesztési        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Mimecast felügyeleti konzolhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Mimecast felügyeleti konzol**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-mimecast-admin-console-sso"></a>A Mimecast felügyeleti konzol egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mimecast felügyeleti konzolba.

1. Navigáljon a **felügyeleti**  >  **szolgáltatások**  >  **alkalmazásaihoz**.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/services.png)

1. Kattintson a **hitelesítési profilok** fülre.
    
    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Kattintson az **új hitelesítési profil** fülre.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Adjon meg egy érvényes leírást a **Leírás** szövegmezőben, és válassza az **SAML-hitelesítés kikényszerítve a felügyeleti konzolon** jelölőnégyzetet.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Az **SAML-konfiguráció az adminisztrációs konzolon** lapon hajtsa végre a következő lépéseket:

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. A **szolgáltatónál**válassza a legördülő listából a **Azure Active Directory** elemet.

    b. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    c. Kattintson az **Importálás**gombra. A metaadatok URL-címének importálása után a mezők automatikusan fel lesznek töltve, nem kell végrehajtania semmilyen műveletet ezeken a mezőkön.

    d. Ügyeljen arra, hogy szüntesse meg a **jelszóval védett környezet használatának** törlését és **az integrált hitelesítési környezet használata** jelölőnégyzeteket.

    e. Kattintson a **Mentés** gombra.

### <a name="create-mimecast-admin-console-test-user"></a>A Mimecast felügyeleti konzol tesztelési felhasználójának létrehozása

1. Egy másik böngészőablakban jelentkezzen be a Mimecast felügyeleti konzolba.

1. Navigáljon az **adminisztrációs**  >  **könyvtárak**  >  **belső könyvtáraihoz**.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Válassza ki a tartományt, ha a tartomány alább szerepel, máskülönben hozzon létre egy új tartományt az **új tartományra**kattintva.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Kattintson az **új címek** fülre.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/new-address.png)

1. Adja meg a szükséges felhasználói adatokat a következő oldalon:

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/user-information.png)

    a. Az **E-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például: `B.Simon@yourdomainname.com` .

    b. A **globális név** szövegmezőbe írja be a felhasználó **teljes nevét** .

    c. **A jelszó és** **Jelszó megerősítése** szövegmezőbe írja be a felhasználó jelszavát.

    d. Válassza **a kényszerített módosítás bejelentkezéskor** jelölőnégyzetet.

    e. Kattintson a **Mentés** gombra.

    f. Ha szerepköröket szeretne hozzárendelni a felhasználóhoz, kattintson a **szerepkör szerkesztése** lehetőségre, és rendelje hozzá a szükséges szerepkört a felhasználóhoz a szervezete követelményeinek megfelelően.

    ![Mimecast felügyeleti konzol konfigurálása](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Mimecast felügyeleti konzol csempére kattint, automatikusan be kell jelentkeznie a Mimecast felügyeleti konzolba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Mimecast felügyeleti konzol kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Mimecast felügyeleti konzoljának védetté tétele fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
