---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az NS1 Egyszeri bejelentkezés azure-hoz | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az NS1 Egyszeri bejelentkezés között az Azure-hoz.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az NS1 Egyszeri bejelentkezés az Azure-hoz

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az NS1 SSO-t az Azure-hoz az Azure Active Directoryval (Azure AD). Ha integrálja az NS1 SSO-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Azure-beli NS1-sSO-hoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve NS1 SSO for Azure az Azure-fiókok.
* Egyetlen központi helyen, az Azure Portalon kezelheti fiókjait.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* NS1 egyszeri bejelentkezés az Azure egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az NS1 SSO for Azure támogatja az SP-t és az IDP által kezdeményezett sso-t.
* Az NS1 SSO azure-beli konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől nyújt ki. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>NS1 SSO hozzáadása az Azure-hoz a galériából

Az NS1 SSO Azure-hoz való integrálásának konfigurálásához hozzá kell adnia az NS1 SSO for Azure-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja be az **NS1 SSO for Azure-t** a keresőmezőbe.
1. Válassza az **NS1 SSO for Azure az** eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az Azure-hoz való NS1 egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t az NS1 SSO for Azure-ral egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez hozzon létre egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Azure-beli NS1-sSO-ban.

Az Azure AD SSO konfigurálásának és tesztelésének általános lépéseit az Azure-hoz való NS1-sSO-val:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.

    a. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.

    b. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Konfigurálja az NS1 Egyszeri bejelentkezés az Azure Egyszeri bejelentkezés](#configure-ns1-sso-for-azure-sso)** konfigurálásához az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.

    a. **[Hozzon létre egy NS1 SSO azure-teszt felhasználó,](#create-an-ns1-sso-for-azure-test-user)** hogy egy megfelelője B.Simon NS1 SSO az Azure-hoz. Ez a megfelelő kapcsolódik a felhasználó Azure AD-ábrázolása.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **NS1 SSO for Azure alkalmazásintegrációs** lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Képernyőkép: Egyszeri bejelentkezés beállítása SAML-oldallal, kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be a következő URL-címet:`https://api.nsone.net/saml/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet, amely a következő mintát használja:`https://api.nsone.net/saml/sso/<ssoid>`

1. Válassza **a További URL-ek beállítása**lehetőséget, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be a következő URL-címet:`https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > A Válasz URL-értéke nem valós. A válasz URL-értékének frissítése a tényleges Válasz URL-címmel. Lépjen kapcsolatba az [NS1 SSO azure-ügyfél támogatási csapatának](mailto:techops@nsone.net) az érték lekérni. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az NS1 SSO azure-alkalmazás elvárja az SAML állításokat egy adott formátumban. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumoknak az értékeit az alkalmazásintegrációs lap **Felhasználói attribútumok & jogcímek** szakaszában kezelheti. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon jelölje ki a ceruza ikont a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![Képernyőkép a Felhasználói attribútumok & Jogcímek szakaszról, kiemelve a ceruza ikonnal](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Válassza ki a jogcím szerkesztéséhez az attribútum nevét.

    ![Képernyőkép a Felhasználói attribútumok & jogcímek szakaszról, kiemelve az attribútum nevét](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Válassza **az Átalakítás**lehetőséget.

    ![Képernyőkép: Jogcím kezelés szakasza, kiemelve az Átalakítás lehetőséggel](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Az **Átalakítás kezelése szakaszban** hajtsa végre az alábbi lépéseket:

    ![Képernyőkép: Az átalakítás kezelése szakasz, a kijelölt különböző mezőkkel](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Válassza a **ExactMailPrefix()** lehetőséget **átalakításként.**

    1. Válassza ki **a user.userprincipalname-t** **az 1.**

    1. Válassza a **Hozzáadás** lehetőséget.

    1. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** válassza a másolás gombot. Ez másolja az **App Federation metaadat-címét,** és menti azt a számítógépre.

    ![Képernyőkép az SAML aláíró tanúsítványról, a kiemelve a másolás gomb](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon B.Simon nevű.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:

   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben látható értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, az Azure-beli NS1 egyszeri bejelentkezés engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **NS1 SSO for Azure**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép a Kezelés szakaszról, kiemelve a Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a felhasználók listájából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 sso konfigurálása az Azure SSO-hoz

Az Azure-hoz való egyszeri bejelentkezés konfigurálásához el kell küldenie az App Federation metaadat-URL-címét az [NS1 Egyszeri bejelentkezés azure-beli támogatási csapatnak.](mailto:techops@nsone.net) Úgy konfigurálják ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>NS1 SSO létrehozása az Azure tesztfelhasználója számára

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót az NS1 SSO azure-ban. Együttműködve az NS1 SSO azure támogatási csapatával, hogy hozzáadja a felhasználókat az NS1 SSO for Azure platformon. Az egyszeri bejelentkezés nem használható, amíg létre nem hozza és nem aktiválja a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Hozzáférési panel használatával teszteli.

Amikor kiválasztja az NS1 SSO azure-hoz csempe access panel, automatikusan be kell jelentkeznie az NS1 SSO for Azure, amelyhez beállítja az SSO. További információt a [Hozzáférési panel – bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az NS1 SSO for Azure-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)