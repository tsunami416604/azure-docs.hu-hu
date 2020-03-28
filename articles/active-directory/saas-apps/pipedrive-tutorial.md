---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Pipedrive-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Pipedrive között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3b97628d-9d0c-45ac-b8ef-7480cf7ec602
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 235169efdcd415df055885d325394c942a2a8d88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Pipedrive-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Pipedrive-ot az Azure Active Directoryval (Azure AD). Ha integrálja a Pipedrive-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Pipedrive-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a Pipedrive az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Pipedrive egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Pipedrive támogatja az **SP és az IDP** által kezdeményezett SSO-t
* A Pipedrive egyszeri bejelentkezés konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


## <a name="adding-pipedrive-from-the-gallery"></a>Pipedrive hozzáadása a galériából

A Pipedrive azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Pipedrive-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Pipedrive** kifejezést a keresőmezőbe.
1. Válassza a **Pipedrive** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Pipedrive-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Pipedrive-val egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Pipedrive-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Pipedrive segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Pipedrive Egyszeri bejelentkezést](#configure-pipedrive-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Pipedrive-tesztfelhasználót](#create-pipedrive-test-user)** – hogy b.Simon megfelelője legyen a Pipedrive-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Pipedrive-alkalmazásintegrációs** lapon keresse meg a Kezelés **szakaszt,** és válassza az egyszeri **bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [a Pipedrive ügyféltámogatási csapatával,](mailto:support@pipedrive.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Pipedrive alkalmazás elvárja az SAML-állítások egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Pipedrive alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez, valamint az **Alkalmazásösszevonás metaadat-url-címének** másolásához és mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](./media/pipedrive-tutorial/certificate-data.png)

1. A **Pipedrive beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Pipedrive-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Pipedrive**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-pipedrive-sso"></a>Pipedrive sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Pipedrive webhelyére.

1. Kattintson a **Felhasználói profil elemre,** és válassza a **Beállítások lehetőséget.**

    ![Pipedrive konfigurációja](./media/pipedrive-tutorial/configure1.png)

1. Görgessen le a biztonsági központba, és válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Pipedrive konfigurációja](./media/pipedrive-tutorial/configure2.png)

1. A **Pipedrive SAML konfigurációján** hajtsa végre a következő lépéseket:

    ![Pipedrive konfigurációja](./media/pipedrive-tutorial/configure3.png)

    a. A **Kiállító** szövegmezőbe illessze be az **App Federation metaadat-url-címét,** amelyet az Azure Portalról másolt.

    b. Az **egyszeri bejelentkezés (SSO) url-cím** szövegdobozába illessze be a **bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    c. Az **egykijelentkezési (SLO) url-cím** szövegmezőbe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    d. Az **x.509 tanúsítvány** szövegmezőjében nyissa meg a letöltött **tanúsítványfájlt (Base64)** az Azure Portalról a Jegyzettömbbe, és másolja be annak tartalmát, és illessze be az **x.509 tanúsítvány** szövegdobozába, és mentse a módosításokat.

### <a name="create-pipedrive-test-user"></a>Pipedrive tesztfelhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Pipedrive webhelyére.

1. Görgessen le a vállalathoz, és válassza **a Felhasználók kezelése**lehetőséget.

    ![Pipedrive konfigurációja](./media/pipedrive-tutorial/user1.png)

1. Kattintson a **Felhasználók hozzáadása**lehetőségre.
    
    ![Pipedrive konfigurációja](./media/pipedrive-tutorial/user2.png)

1. A **Felhasználók kezelése** csoportban hajtsa végre az alábbi lépéseket:

    ![Pipedrive konfigurációja](./media/pipedrive-tutorial/user3.png)

    a. Az **E-mail** mezőbe írja be a `B.Simon@contoso.com`felhasználó e-mail címét, például .

    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét.

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét.

    d. Kattintson **a Megerősítés gombra, és hívja meg a felhasználókat.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Pipedrive csempére kattint, automatikusan be kell jelentkeznie arra a Csőmeghajtóba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Pipedrive-ot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)