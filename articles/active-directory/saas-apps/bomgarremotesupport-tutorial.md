---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a BeyondTrust remote supporttal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a BeyondTrust remote-támogatás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74082067"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a BeyondTrust távoli támogatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a BeyondTrust távtámogatást az Azure Active Directoryval (Azure AD). Ha integrálja a BeyondTrust remote supportot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a BeyondTrust távoli támogatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve BeyondTrust távoli támogatás az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* BeyondTrust Remote Support egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A BeyondTrust remote support támogatja az **SP** által kezdeményezett sso-t
* A BeyondTrust távtámogatás támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>BeyondTrust távoli támogatás hozzáadása a galériából

A BeyondTrust remote support azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BeyondTrust távoli támogatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **BeyondTrust remote support kifejezést** a keresőmezőbe.
1. Válassza **a BeyondTrust remote support elemet** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a BeyondTrust távoli támogatáshoz

Konfigurálja és tesztelje az Azure AD SSO szolgáltatást a BeyondTrust távoli támogatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a BeyondTrust remote-támogatásban.

Az Azure AD SSO BeyondTrust remote support szolgáltatással történő konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[A BeyondTrust Remote Support SSO konfigurálása](#configure-beyondtrust-remote-support-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * **[Hozzon létre BeyondTrust remote support teszt felhasználó](#create-beyondtrust-remote-support-test-user)** - egy megfelelője B.Simon a BeyondTrust távoli támogatás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **BeyondTrust remote-támogatási** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<HOSTNAME>.bomgar.com/saml`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<HOSTNAME>.bomgar.com`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Ezeket az értékeket később az oktatóanyagban ismertetheti.

1. A BeyondTrust remote support alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a BeyondTrust Remote Support alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név |  Forrás attribútuma|
    | ---------------| ----------|
    | Utónév | user.givenname |
    | E-mail cím | user.mail |
    | Név | user.userprincipalname |
    | Felhasználónév | user.userprincipalname |
    | Csoportok | user.csoportok |
    | Egyedi felhasználói azonosító | user.userprincipalname |

    > [!NOTE]
    > Amikor Azure AD-csoportokat rendel a BeyondTrust távoli támogatási alkalmazáshoz, a "Jogcímben visszaadott csoportok" beállítást módosítani kell a Nincs és a SecurityGroup beállításhoz. A csoportok objektumazonosítóként lesznek importálva az alkalmazásba. Az Azure AD-csoport objektumazonosítója az Azure Active Directory-felület tulajdonságainak ellenőrzésével található. Erre az Azure AD-csoportokhivatkozására és hozzárendelésére van szükség a megfelelő csoportházirendekhez.

1. Az egyedi felhasználói azonosító beállításakor ezt az értéket NameID-Format: **Persistent**értékre kell állítani. Azt követeljük meg, hogy ez egy állandó azonosító legyen, hogy helyesen azonosíthassa és társítsa a felhasználót a megfelelő csoportházirendekhez az engedélyekhez. Kattintson a szerkesztés ikonra a **Felhasználói attribútumok & Jogcímek** párbeszédpanel megnyitásához az egyedi felhasználói azonosító értékének szerkesztéséhez.

1. A **Jogcím kezelése** csoportban kattintson a **Névazonosító kiválasztása formátumra,** és állítsa az értéket Állandó értékre, majd kattintson a Mentés **gombra.** **Persistent**

    ![Felhasználói attribútumok és jogcímek](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **BeyondTrust távoli támogatás beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t beyondtrust távoli támogatás engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **BeyondTrust Remote Support lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-beyondtrust-remote-support-sso"></a>BeyondTrust távoli támogatási sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a BeyondTrust távtámogatásba.

1. Kattintson az **ÁLLAPOT** menüre, és másolja az **azonosítót**, a **Válasz URL-címet** és **a Bejelentkezési URL-t,** és használja ezeket az értékeket az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    ![BeyondTrust távoli támogatás konfigurálása](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Nyissa meg a BeyondTrust remote support `https://support.example.com/login` /login felületet, ahol support.example.com a készülék elsődleges állomásneve, és hitelesítse magát a rendszergazdai hitelesítő adatokkal. **support.example.com**

1. Keresse meg a **Felhasználók & a biztonsági** > **szolgáltatók at.**

1. A legördülő menüben válassza az **SAML** lehetőséget, majd kattintson a **Szolgáltató létrehozása** gombra.

1. Az Identitásszolgáltató beállításai szakaszban lehetőség van az identitásszolgáltató metaadatainak feltöltésére. Keresse meg az Azure Portalról letöltött metaadat-XML-fájlt, és kattintson a **Feltöltés gombra.** Az **entitásazonosító,** **az egyszeri bejelentkezési szolgáltatás URL-címe** és tanúsítványa automatikusan feltöltődik, és a **protokollkötést** **HTTP POST-ra**kell módosítani. Lásd az alábbi képernyőképet:

    ![BeyondTrust távoli támogatás konfigurálása](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust távtámogatás tesztfelhasználójának létrehozása

Itt konfiguráljuk a felhasználói kiépítési beállításokat. Az ebben a szakaszban használt értékekre az Azure Portal **Felhasználói attribútumok & jogcímek** szakaszhivatkozik. Ezt úgy konfiguráltuk, hogy ez legyen az alapértelmezett értékek, amelyeket a létrehozás kor már importáltak, azonban az érték szükség esetén testreszabható.

![Felhasználó létrehozása](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> A csoportok és az e-mail attribútum nem szükségesek ehhez a megvalósításhoz. Ha az Azure AD-csoportok at használó, és hozzárendeli őket BeyondTrust távoli támogatási csoport házirendek engedélyek, a csoport objektumazonosítója kell hivatkozni a tulajdonságokon keresztül az Azure Portalon, és elhelyezni a "Rendelkezésre álló csoportok" szakaszban. Miután ez befejeződött, az objektumazonosító/AD-csoport mostantól elérhető lesz egy csoportházirendhez való hozzárendeléshez.

![Felhasználó létrehozása](./media/bomgarremotesupport-tutorial/config-user2.png)

![Felhasználó létrehozása](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Másik lehetőségként az SAML2 biztonsági szolgáltatóalapértelmezett csoportházirendje is beállítható. Ezzel a beállítással az SAML-en keresztül hitelesítést végző összes felhasználó hoz hatja hozzá a csoportházirendben megadott engedélyeket. A Általános tagok házirend a BeyondTrust távoli támogatás/kiemelt távoli hozzáférés korlátozott engedélyekkel rendelkező része, amely a hitelesítés tesztelésére és a felhasználók megfelelő házirendekhez rendelésére használható. A felhasználók az első sikeres hitelesítési kísérletig nem lépnek fel az SAML2-felhasználók listájára a /login > Users & Security kapcsolón keresztül. A csoportházirendekkel kapcsolatos további információk az alábbi linken találhatók:`https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelBeyondTrust távoli támogatás csempéjére kattint, a rendszer automatikusan bejelentkezik a BeyondTrust távoli támogatásba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a BeyondTrust távoli támogatását az Azure AD-vel](https://aad.portal.azure.com/)
