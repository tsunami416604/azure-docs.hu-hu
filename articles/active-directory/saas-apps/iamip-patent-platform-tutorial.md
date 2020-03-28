---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az IamIP szabadalmi platformmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az IamIP szabadalmi platform között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190738"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az IamIP szabadalmi platformmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az IamIP szabadalmi platformot az Azure Active Directoryval (Azure AD). Ha integrálja az IamIP szabadalmi platformot az Azure AD-vel, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá az IamIP szabadalmi platformhoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve iamIP szabadalmi platform az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* IamIP szabadalmi platform előfizetés egyszeri bejelentkezéssel (SSO) engedélyezve.

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

Az IamIP szabadalmi platform támogatja az SP által kezdeményezett és az IDP által kezdeményezett egyszeri szolgáltató t.

Az IamIP szabadalmi platform konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Add IamIP Szabadalmi Platform a galériából

Az IamIP szabadalmi platform Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az IamIP szabadalmi platformot a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Nyissa meg a **Vállalati alkalmazások lehetőséget,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a galéria szakaszban** írja be az **IamIP szabadalmi platformot** a keresőmezőbe.
1. Válassza az **IamIP szabadalmi platform** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Az Azure AD-sSO konfigurálása és tesztelése az IamIP szabadalmi platformhoz

Konfigurálhatja és tesztelheti az Azure AD SSO-t az IamIP szabadalmi platformmal egy B.Simon nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó iamIP szabadalmi platform.

Az Azure AD Egyszeri szolgáltatás IamIP szabadalmi platformmal történő konfigurálásához és teszteléséhez az alábbi magas szintű lépéseket kell tennie:

1. **[Konfigurálja az Azure AD sso-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák a funkciót.
    * **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
    * **[Hozzáférést biztosít a tesztfelhasználó](#grant-access-to-the-test-user)** számára, hogy a felhasználó az Azure AD egyszeri bejelentkezést használhassa.

1. **[Konfigurálja az IamIP szabadalmi platform SSO-ját](#configure-iamip-patent-platform-sso)** az alkalmazás oldalán.
    * **[Hozzon létre egy IamIP szabadalmi platform teszt felhasználó,](#create-iamip-patent-platform-test-user)** mint a megfelelője az Azure AD ábrázolása a felhasználó.

1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez kövesse az alábbi lépéseket az Azure Portalon:

1. Az [Azure Portalon](https://portal.azure.com/)az **IamIP szabadalmi platform alkalmazásintegrációs** lapján, a **Kezelés** csoportban válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruzagombját a beállítások szerkesztéséhez:

   ![Ceruza gomb az alapvető SAML konfigurációhoz](common/edit-urls.png)

1. Ha az **Egyszerű SAML-konfiguráció** szakaszban rendelkezik szolgáltatói metaadatfájllal, és az Egyszeri bejelentkezést IDP által kezdeményezett módban szeretné konfigurálni, tegye az alábbi lépéseket:

    a. Válassza **a Metaadatfájl feltöltése lehetőséget:**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Jelölje ki a mappagombot, jelölje ki a metaadatfájlt, majd kattintson a **Feltöltés gombra:**

    ![Mappa és Feltöltés gombok](common/browse-upload-metadata.png)

    c. A metaadatfájl feltöltése után az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik az **Egyszerű SAML konfiguráció szakaszban:**

    ![Azonosító és válasz URL-értékei](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **válasz URL-értékei** nem kerülnek automatikusan kitöltésre, adja meg manuálisan az értékeket a követelményeknek megfelelően.

1. Válassza **a További URL-címek beállítása** lehetőséget, és hajtsa végre a következő lépést, ha az alkalmazást SP által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be a **\/https: /patents.iamip.com/login-user**kapcsolót.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** válassza a **Tanúsítvány (Raw)** **letöltési** hivatkozását a tanúsítvány letöltéséhez és a számítógépre való mentéséhez:

    ![Tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **IamIP szabadalmi platform beállítása** szakaszban másolja a megfelelő URL-t vagy URL-eket az Ön igényei nek megfelelően:

    ![Konfigurációs URL-címek másolása](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget. Válassza a **Felhasználók**lehetőséget, majd a **Minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok párbeszédpanelen hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja be a **B.Simon**értéket.  
   1. A **Felhasználónév** mezőbe \<írja be\<a>@ cégtartomány> felhasználónevet. \<> kiterjesztése. Például: `B.Simon@contoso.com`.
   1. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="grant-access-to-the-test-user"></a>Hozzáférés megadása a tesztfelhasználónak

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t, amely biztosítja a felhasználói hozzáférést az IamIP szabadalmi platform.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **IamIP szabadalmi platform**lehetőséget.
1. Az alkalmazás áttekintése lap **Kezelés** csoportjában válassza a **Felhasználók és csoportok**lehetőséget:

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget:

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a **Felhasználók** listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-iamip-patent-platform-sso"></a>Az IamIP szabadalmi platform sso-jának konfigurálása

Az IamIP szabadalmi platform oldalán történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött nyers tanúsítványt és az Azure Portalról másolt megfelelő URL-címeket az [IamIP szabadalmi platform támogatási csapatának.](mailto:info@iamip.com) Úgy konfigurálják az SAML SSO-kapcsolatot, hogy mindkét oldalon helyes legyen.

### <a name="create-iamip-patent-platform-test-user"></a>IamIP szabadalmi platform tesztfelhasználó létrehozása

Működjön együtt az [IamIP szabadalmi platform támogatási csapatával](mailto:info@iamip.com) egy B.Simon nevű felhasználó hozzáadásához az IamIP szabadalmi platformban. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD SSO-konfigurációt a Hozzáférési panel használatával teszteli.

Amikor kiválasztja az IamIP szabadalmi platform csempe access panel, akkor automatikusan bejelentkezett az IamIP Szabadalmi Platform például, amelyre beállította SSO. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az IamIP szabadalmi platformot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
