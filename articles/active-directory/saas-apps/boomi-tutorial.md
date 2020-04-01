---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Boomival | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Boomi között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77086446"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Boomival

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Boomi szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Boomi-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Boomihoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve boomi az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Boomi egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Boomi támogatja az **IDP** által kezdeményezett SSO-t
* A Boomi konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-boomi-from-the-gallery"></a>Boomi hozzáadása a galériából

A Boomi azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Boomi-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Boomi** kifejezést a keresőmezőbe.
1. Válassza a **Boomi** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Boomi számára

Konfigurálja és tesztelje az Azure AD SSO-t a Boomi-val egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a boomi kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Boomi segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Boomi SSO-t](#configure-boomi-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Boomi tesztfelhasználó létrehozása](#create-boomi-test-user)** – b.Simon megfelelője a Boomiban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Boomi alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha **a** **Service Provider metaadatfájlja a Service Provider metaadatfájlja,** és **IDP** által kezdeményezett módban szeretne konfigurálni, hajtsa végre a következő lépéseket:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **Azonosító** és a **Válasz URL-értékei** automatikusan feltöltődnek az Egyszerű SAML-konfiguráció szakaszban.

    ![image](common/idp-intiated.png)

    > [!Note]
    > A Szolgáltató **metaadatfájlját** a **Boomi SSO konfigurálása** szakaszból kapja, amelyet az oktatóanyag későbbi részében ismertetünk. Ha az **azonosító** és a **válasz URL-értékei** nem kapnak automatikus polulated, majd töltse ki az értékeket manuálisan a követelménynek megfelelően.

1. A Boomi alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. Amellett, hogy a fenti, Boomi alkalmazás elvárja, hogy néhány további attribútumok at kell átadni vissza SAML választ, amelyek az alábbiakban látható. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név |  Forrás attribútuma|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Boomi beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t engedve a Boomi.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Boomi**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-boomi-sso"></a>Boomi SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Boomi vállalati webhelyére rendszergazdaként.

1. Nyissa meg a **Vállalat nevét,** és válassza a **Beállítás című akta.**

1. Kattintson az **SSO-beállítások fülre,** és hajtsa végre az alábbi lépéseket.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Jelölje **be az SAML egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.

    b. Kattintson az **Importálás** gombra a letöltött tanúsítvány Feltöltéséhez az Azure AD-ből **az identitásszolgáltatói tanúsítványba**.

    c. Az **identitásszolgáltató bejelentkezési URL-címmezőjében** helyezze a **bejelentkezési URL-cím** értékét az Azure AD alkalmazás konfigurációs ablakából.

    d. Az **összevonási azonosító helye**mezőben válassza ki, hogy az **Összevonási azonosító FEDERATION_ID Attribútumelem választógombjában található.**

    e. Másolja az **AtomSphere metaadat URL-jét**, lépjen a **MetaData URL-re** az Ön által választott böngészőn keresztül, és mentse a kimenetet egy fájlba. Töltse fel a **metaadat URL-címét** az Azure Portal **alapszintű SAML-konfigurációszakaszában.**

    f. Kattintson a **Mentés** gombra.

### <a name="create-boomi-test-user"></a>Boomi tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Boomiba, ki kell építeni őket a Boomiba. Boomi esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a Boomi cég webhelyére rendszergazdaként.

1. A bejelentkezés után nyissa meg a **Felhasználókezelés** lehetőséget, és nyissa meg a **Felhasználók lehetőséget.**

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_001.png "Felhasználók")

1. Kattintson **+** az ikonra, és megnyílik a **Felhasználói szerepkörök hozzáadása/karbantartása** párbeszédpanel.

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_002.png "Felhasználók")

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_003.png "Felhasználók")

    a. A **Felhasználói e-mail cím** mezőbe írja be B.Simon@contoso.coma felhasználó e-mail címét, például .

    b. Az **Utónév** mezőbe írja be a felhasználó előnevét, például B.

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simont.

    d. Adja meg a felhasználó **összevonási azonosítóját**. Minden felhasználónak rendelkeznie kell egy összevonási azonosítóval, amely egyedileg azonosítja a felhasználót a fiókon belül.

    e. Rendelje hozzá az **Általános jogú szerepkört** a felhasználóhoz. Ne rendelje hozzá a rendszergazdai szerepkört, mert ez normális légköri hozzáférést és egyszeri bejelentkezési hozzáférést biztosítana számukra.

    f. Kattintson az **OK** gombra.

    > [!NOTE]
    > A felhasználó nem kap üdvözlő értesítést e-mailt, amely tartalmazza az AtomSphere fiókba való bejelentkezéshez használható jelszót, mivel a jelszavát az identitásszolgáltató kezeli. A Boomi felhasználói fiók-létrehozási eszközeit vagy API-jait a Boomi az AAD felhasználói fiókok kiépítéséhez használhatja.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelboomi csempéjére kattint, automatikusan be kell jelentkeznie a Boomi-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Próbálja ki a Boomi-t az Azure AD-vel](https://aad.portal.azure.com/)
