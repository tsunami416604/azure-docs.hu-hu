---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Coda-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Coda között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acaf2012-ef2e-4ce0-8467-ceece3bae50e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74da278dbbc0ac32407c345524e224ca5f7616da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77194634"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Coda-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Coda-t az Azure Active Directoryval (Azure AD). Ha integrálja a Coda-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Coda.Control in Azure AD who has access to Coda.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Coda az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Coda egyszeri bejelentkezés (SSO) kompatibilis előfizetés (Enterprise) a GDrive-integráció le tiltva. Lépjen kapcsolatba [a Coda támogatási csapatával,](mailto:support@coda.io) és tiltsa le a GDrive-integrációt a szervezetnél, ha az jelenleg engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Coda támogatja az **IDP** által kezdeményezett sso-t

* A Coda támogatja **a Just In Time** felhasználói kiépítést

* A Coda konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-coda-from-the-gallery"></a>Coda hozzáadása a galériából

A Coda azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Coda-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Coda** kifejezést a keresőmezőbe.
1. Válassza a **Coda** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Coda-hoz

Konfigurálja és tesztelje az Azure AD-sSO-t a Coda-val egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a codai kapcsolódó felhasználó között.

Az Azure AD-sSO konfigurálásához és teszteléséhez a Coda segítségével hajtsa végre a következő építőelemeket:

1. **[Kezdődik konfiguráció Coda SSO](#begin-configuration-of-coda-sso)** - kezdeni konfiguráció sso A Coda.
1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
   * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
   * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Coda SSO-t](#configure-coda-sso)** - az egyszeri bejelentkezési beállítások teljes konfigurálásához a Coda-ban.
   * **[Hozzon létre Coda teszt felhasználó](#create-coda-test-user)** - egy megfelelője B.Simon a Coda, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="begin-configuration-of-coda-sso"></a>A Coda SSO konfigurációjának megkezdése

A kezdéshez kövesse a Coda alábbi lépéseit.

1. A Coda alkalmazásban nyissa meg a **Szervezet beállításai** panelt.

   ![Szervezeti beállítások megnyitása](media/coda-tutorial/org-settings.png)

1. Győződjön meg arról, hogy a szervezetnél ki van kapcsolva a GDrive-integráció. Ha jelenleg engedélyezve van, lépjen kapcsolatba a [Coda támogatási csapatával,](mailto:support@coda.io) hogy segítséget nyújthasson a GDrive-ról való áttelepítéshez.

   ![GDrive letiltva](media/coda-tutorial/gdrive-off.png)

1. A **Hitelesítés sso-val (SAML)** csoportban válassza az **SAML konfigurálása** lehetőséget.

   ![Saml beállítások](media/coda-tutorial/saml-settings-link.png)

1. Vegye figyelembe az **entitásazonosító** és az **SAML-válasz URL-címének**értékeit, amelyekre a következő lépésekben szüksége lesz.

   ![Az Azure-ban használandó entitásazonosító és SAML-válasz URL-címe](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Coda** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon adja meg a következő mezők értékeit:

   a. Az **Azonosító** mezőbe írja be felülről az "Entitásazonosító" kifejezést. Meg kell követni a mintát:`https://coda.io/samlId/<CUSTOMID>`

   b. A **Válasz URL-cím** mezőjébe írja be felülről az "SAML-válasz URL-címét". Meg kell követni a mintát:`https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Az Ön értékei eltérnek a fentiektől; az értékeket a Coda "SAML konfigurálása" konzolján találja. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Coda beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Coda hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Coda**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

   ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-coda-sso"></a>Coda SSO konfigurálása

A telepítés befejezéséhez adja meg az Azure Active Directory ból származó értékeket a Coda **Configure Saml** panelen.

1. A Coda alkalmazásban nyissa meg a **Szervezet beállításai** panelt.
1. A **Hitelesítés sso-val (SAML)** csoportban válassza az **SAML konfigurálása** lehetőséget.
1. Állítsa **az SAML-szolgáltatót** az **Azure Active Directory ra.**
1. Az **identitásszolgáltató bejelentkezési URL-címét**illessze be a **bejelentkezési URL-címet** az Azure konzolról.
1. Az **identitásszolgáltató kiállítója**illessze be az **Azure AD-azonosítót** az Azure-konzolról.
1. Az **Identitásszolgáltató nyilvános tanúsítványa csoportban**válassza a **Tanúsítvány feltöltése** lehetőséget, és válassza ki a korábban letöltött tanúsítványfájlt.
1. Kattintson a **Mentés** gombra.

Ezzel befejeződött az SAML SSO-kapcsolat beállításához szükséges munka.

### <a name="create-coda-test-user"></a>Coda-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Coda-ban. A Coda támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Coda, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Coda csempére kattint, automatikusan be kell jelentkeznie arra a Coda-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Coda-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védjük meg coda fejlett láthatóság és ellenőrzések](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
