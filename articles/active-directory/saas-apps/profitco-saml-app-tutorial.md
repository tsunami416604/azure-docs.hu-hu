---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja Profit.co SAML Alkalmazással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és Profit.co SAML App között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770927"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja Profit.co SAML Alkalmazással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja Profit.co SAML-alkalmazást az Azure Active Directoryval (Azure AD). Ha Profit.co SAML alkalmazást integrálja az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér Profit.co SAML Alkalmazáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkeznek Profit.co SAML alkalmazásba az Azure AD-fiókjukkal.
* Egyetlen központi helyen, az Azure Portalon kezelheti fiókjait.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Profit.co SAML App egyszeri bejelentkezés (SSO) engedélyezve lévő előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Profit.co SAML App supports IDP initiated SSO.

* Az PROFIT.CO SAML alkalmazás konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől nyújt ki. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-profitco-saml-app-from-the-gallery"></a>Profit.co SAML alkalmazás hozzáadása a galériából

A Profit.co SAML-alkalmazás Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Profit.co SAML-alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be **Profit.co SAML alkalmazást** a keresőmezőbe.
1. Válassza **ki Profit.co SAML alkalmazást** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését Profit.co SAML alkalmazáshoz

Konfigurálja és tesztelje az Azure AD SSO-t Profit.co SAML Alkalmazással egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez hozzon létre egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó Profit.co SAML App.

Az alábbiakban az Azure AD SSO konfigurálásának és tesztelésének általános lépéseit Profit.co SAML alkalmazással:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Konfigurálja Profit.co SAML App SSO-t](#configure-profitco-saml-app-sso)** az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Profit.co SAML App teszt felhasználó,](#create-a-profitco-saml-app-test-user)** hogy egy megfelelője B.Simon Profit.co SAML App. Ez a megfelelő kapcsolódik a felhasználó Azure AD-ábrázolása.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Profit.co SAML app** alkalmazásintegrációs lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Képernyőkép: Egyszeri bejelentkezés beállítása SAML-oldallal, kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Az **egyszerű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurált, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználóknak a **Mentés** gombra kattintva kell menteniük a konfigurációt.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** csoportban kattintson a **Másolás gombra.** Ez másolja az **App Federation metaadat-címét,** és menti azt a számítógépre.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít Profit.co SAML App.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában jelölje be **Profit.co SAML App**.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép a Kezelés szakaszról, kiemelve a Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a felhasználók listájából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-profitco-saml-app-sso"></a>Profit.co SAML App SSO konfigurálása

Az egyszeri bejelentkezés konfigurálásához a Profit.co SAML alkalmazás oldalán el kell küldenie az App Federation metaadat-CÍMÉT a [Profit.co SAML alkalmazás támogatási csapatának.](mailto:support@profit.co) Úgy konfigurálják ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-a-profitco-saml-app-test-user"></a>Profit.co SAML alkalmazásteszt-felhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót Profit.co SAML alkalmazásban. Együttműködve a [Profit.co SAML App támogatási csapatával](mailto:support@profit.co) a felhasználók hozzáadása a Profit.co SAML App platformon. Az egyszeri bejelentkezés nem használható, amíg létre nem hozza és nem aktiválja a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Hozzáférési panel használatával teszteli.

Ha a hozzáférési panelen kiválasztja a Profit.co SAML App csempét, automatikusan be kell jelentkeznie a Profit.co SAML alkalmazásba, amelyhez beállítja az SSO-t. További információt a [Hozzáférési panel – bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki Profit.co SAML appot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az Profit.co SAML alkalmazás védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
