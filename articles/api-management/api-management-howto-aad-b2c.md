---
title: Fejlesztői fiókok engedélyezése az Azure Active Directory B2C használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan engedélyezheti a felhasználókat az Azure Active Directory B2C használatával az API Management ben.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475493"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Fejlesztői fiókok engedélyezése az Azure Active Directory B2C használatával az Azure API Managementben

## <a name="overview"></a>Áttekintés

Az Azure Active Directory B2C egy felhőalapú identitáskezelési megoldás a fogyasztók számára néző webes és mobilalkalmazások számára. Segítségével kezelheti a fejlesztői portálhoz való hozzáférést. Ez az útmutató bemutatja az API Management szolgáltatásban az Azure Active Directory B2C-vel való integrációhoz szükséges konfigurációt. A fejlesztői portálhoz való hozzáférés klasszikus Azure Active Directory használatával történő engedélyezéséről a [Fejlesztői fiókok engedélyezése]az Azure Active Directory használatával című témakörben olvashat.

> [!NOTE]
> Az útmutatóban leírt lépések végrehajtásához először rendelkeznie kell egy Azure Active Directory B2C-bérlővel egy alkalmazás létrehozásához. Emellett készen kell készítenie a regisztrációs és a bejelentkezési házirendeket. További információt az [Azure Active Directory B2C – áttekintés című témakörben talál.]

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Fejlesztői fiókok engedélyezése az Azure Active Directory B2C használatával

1. Első lépésekhez jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az API Management-példányt.

   > [!NOTE]
   > Ha még nem hozott létre API Management szolgáltatáspéldányt, olvassa el az [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] az Azure API Management oktatóanyag első lépések című [témakörében.][Get started with Azure API Management]

1. Az **Identitások**csoportban. Kattintson a **+Hozzáadás** gombra a tetején.

   Az **Identitásszolgáltató hozzáadása** ablaktábla a jobb oldalon jelenik meg. Válassza **az Azure Active Directory B2C**lehetőséget.
    
   ![AAD B2C hozzáadása identitásszolgáltatóként][api-management-howto-add-b2c-identity-provider]

1. Másolja az **átirányítási URL-t**.

   ![AAD B2C identitásszolgáltató átirányítási URL-címe][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Egy új lapon érheti el az Azure Active Directory B2C-bérlőaz Azure Portalon, és nyissa meg az **alkalmazások** panelen.

   ![Új alkalmazás regisztrálása 1][api-management-howto-aad-b2c-portal-menu]

1. Kattintson a **Hozzáadás** gombra egy új Azure Active Directory B2C alkalmazás létrehozásához.

   ![Új alkalmazás regisztrálása 2][api-management-howto-aad-b2c-add-button]

1. Az **Új alkalmazás** panelen adja meg az alkalmazás nevét. Válassza az **Igen** lehetőséget a **Web App/Web API csoportban,** és válassza az **Igen** lehetőséget az Implicit **folyamat engedélyezése**csoportban. Ezután illessze be **a** 3. **Reply URL**

   ![Új alkalmazás regisztrálása 3][api-management-howto-aad-b2c-app-details]

1. Ha az új fejlesztői portált használja (nem az örökölt fejlesztői portált), adja meg az **utónév,** a **vezetéknév**és a **felhasználó objektumazonosítóját** az alkalmazásjogcímekben.

    ![Alkalmazásjogcímek](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Kattintson a **Létrehozás** gombra. Az alkalmazás létrehozásakor megjelenik az **Alkalmazások** panelen. Kattintson az alkalmazás nevére a részletek megtekintéséhez.

   ![Új alkalmazás regisztrálása 4][api-management-howto-aad-b2c-app-created]

1. A **Tulajdonságok** panelről másolja az **alkalmazásazonosítót** a vágólapra.

   ![1. alkalmazásazonosító][api-management-howto-aad-b2c-app-id]

1. Váltson vissza az API Management **Identitásszolgáltató** ablaktáblájára, és illessze be az azonosítót az **ügyfélazonosító** szövegmezőjébe.
    
1.  Váltson vissza a B2C alkalmazás regisztrációjára, kattintson a **Kulcsok** gombra, majd a **Kulcs generálása parancsra.** A konfiguráció mentéséhez és az Alkalmazáskulcs megjelenítéséhez kattintson a **Mentés** **gombra.** Másolja a kulcsot a vágólapra.

    ![Alkalmazáskulcs 1][api-management-howto-aad-b2c-app-key]

1.  Váltson vissza az API Management **Identitásszolgáltató** ablaktáblájára, és illessze be a kulcsot az **ügyféltitkos** kulcs mezőbe.
    
1.  Adja meg az Azure Active Directory B2C-bérlő tartománynevét a **Signin-bérlőben.**

1.  A **Hatóság** mező ben szabályozhatja az Azure AD B2C bejelentkezési URL-címét. Állítsa az értéket **<your_b2c_tenant_name>.b2clogin.com**.

1. Adja meg a **regisztrációs szabályzatot** és **a bejelentkezési szabályzatot** a B2C bérlői házirendekből. Tetszés szerint megadhatja a **Profilszerkesztési házirendet** és a **Jelszó-visszaállítási házirendet**is.

1. Miután megadta a kívánt konfigurációt, kattintson a **Mentés gombra.**

    A módosítások mentése után a fejlesztők új fiókokat hozhatnak létre, és az Azure Active Directory B2C használatával bejelentkezhetnek a fejlesztői portálra.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Fejlesztői portál – Azure AD B2C-fiók hitelesítésének hozzáadása

A fejlesztői portálon az AAD B2C-vel való bejelentkezés a **Bejelentkezés gombbal lehetséges: OAuth** widget. A widget már szerepel az alapértelmezett fejlesztői portál tartalom bejelentkezési oldalán.

Bár egy új fiók automatikusan létrejön, amikor egy új felhasználó bejelentkezik az AAD B2C-vel, érdemes lehet ugyanazt a widgetet hozzáadni a regisztrációs oldalhoz.

A **Regisztrációs űrlap: Az OAuth** widget az OAuth-ra való regisztrációhoz használt űrlapot jelöli.

> [!IMPORTANT]
> Az AAD-módosítások érvénybe léptetéséhez újra közzé kell tennie [a portált.](api-management-howto-developer-portal-customize.md#publish)

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Örökölt fejlesztői portál – hogyan regisztrálható az Azure AD B2C szolgáltatással

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Ha az Azure Active Directory B2C használatával szeretne regisztrálni egy fejlesztői fiókra, nyisson meg egy új böngészőablakot, és nyissa meg a fejlesztői portált. Kattintson a **Regisztráció** gombra.

   ![Fejlesztői portál 1][api-management-howto-aad-b2c-dev-portal]

2. Válassza ki, hogy regisztrál az **Azure Active Directory B2C szolgáltatással.**

   ![Fejlesztői portál 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. A visszairányítást az előző szakaszban konfigurált regisztrációs házirendre irányítja át. Válassza ki, hogy az e-mail címével vagy valamelyik meglévő közösségi fiókjával szeretne regisztrálni.

   > [!NOTE]
   > Ha az Azure Active Directory B2C az egyetlen lehetőség, amely engedélyezve van az **Identitások** lapon a közzétevői portálon, akkor közvetlenül a regisztrációs szabályzatra lesz irányítva.

   ![Fejlesztői portál][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Amikor a regisztráció befejeződött, a szoftver visszairányítja a fejlesztői portálra. Most már be van jelentkezve az API Management szolgáltatáspéldány fejlesztői portáljára.

    ![Regisztráció kész][api-management-registration-complete]

## <a name="next-steps"></a>További lépések

*  [Az Azure Active Directory B2C áttekintése]
*  [Azure Active Directory B2C: Bővíthető házirendkeretrendszer]
*  [Microsoft-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]
*  [Google-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]
*  [LinkedIn-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]
*  [Facebook-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Az Azure Active Directory B2C áttekintése]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Fejlesztői fiókok engedélyezése az Azure Active Directory használatával]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Bővíthető házirendkeretrendszer]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Microsoft-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Google-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Facebook-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[LinkedIn-fiók használata identitásszolgáltatóként az Azure Active Directory B2C-ben]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
