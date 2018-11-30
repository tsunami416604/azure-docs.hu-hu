---
title: Fejlesztői fiókok engedélyezése az Azure Active Directory B2C – az Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a felhasználók az API Management az Azure Active Directory B2C használatával.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 1e2d809ed476b1789736f0d61dfea528b090da50
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447129"
---
> [!WARNING]
> Az Azure Active Directory B2C-integráció érhető el a [fejlesztői, Standard és prémium szintű](https://azure.microsoft.com/pricing/details/api-management/) csak szint esetében.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Hogyan lehet Azure Active Directory B2C használatával az Azure API Management fejlesztői fiókok engedélyezése

## <a name="overview"></a>Áttekintés
Az Azure Active Directory B2C egy felhőbeli identitáskezelő megoldás a felhasználók felé néző webes és mobilalkalmazások. A fejlesztői portál hozzáférés kezelésére használható. Ez az útmutató bemutatja, a konfiguráció szükséges az API Management szolgáltatás integrálása az Azure Active Directory B2C a. A fejlesztői portálra való hozzáférés engedélyezése a klasszikus Azure Active Directory használatával kapcsolatos információkért lásd: [Hogyan lehet az Azure Active Directory fejlesztői fiókok engedélyezése].

> [!NOTE]
> Hajtsa végre a jelen útmutató lépéseit, először szüksége van az alkalmazás létrehozása az Azure Active Directory B2C-bérlő. Emellett szüksége lesz készen áll a regisztráció és bejelentkezés házirendeket. További információkért lásd: [Az Azure Active Directory B2C – áttekintés].

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Fejlesztői fiókok engedélyezése az Azure Active Directory B2C használatával

1. Első lépésként jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az API Management-példány.

   > [!NOTE]
   > Ha még még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg [az API Management szolgáltatáspéldány létrehozása] [ Create an API Management service instance] a a [Ismerkedés az Azure API Management oktatóanyag] [Get started with Azure API Management].

2. A **biztonsági**válassza **identitások**. Kattintson a **+ Hozzáadás** tetején.

   A **Hozzáadás identitásszolgáltató** ablaktáblán a jobb oldalon jelenik meg. Válasszon **az Azure Active Directory B2C**.
    
   ![Identitásszolgáltató hozzáadása AAD B2C-vel][api-management-howto-add-b2c-identity-provider]

3. Másolás a **átirányítási URL-cím**.

  ![AAD B2C identitásszolgáltató szolgáltató átirányítási URL-címe][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Új lapon, az az Azure Portalon, és nyissa meg az Azure Active Directory B2C-bérlő eléréséhez a **alkalmazások** panelen.

  ![1 új alkalmazás regisztrálása][api-management-howto-aad-b2c-portal-menu]

5. Kattintson a **Hozzáadás** gombra kattintva hozzon létre egy új Azure Active Directory B2C-alkalmazást.

  ![2 új alkalmazás regisztrálása][api-management-howto-aad-b2c-add-button]

6. Az a **új alkalmazás** panelen adja meg az alkalmazás nevét. Válassza a **Igen** alatt **Web App/Web API**, és válassza a **Igen** alatt **implicit engedélyezési folyamat engedélyezése**. Illessze be a **átirányítási URL-cím** , 3. lépésben másolt a **válasz URL-cím** szövegmezőben.

  ![3 új alkalmazás regisztrálása][api-management-howto-aad-b2c-app-details]

7. Kattintson a **Létrehozás** gombra. Amikor az alkalmazás létrejött, megjelenik a **alkalmazások** panelen. Kattintson az alkalmazás nevére a részletek megtekintéséhez.

  ![4 új alkalmazás regisztrálása][api-management-howto-aad-b2c-app-created]

8. Az a **tulajdonságok** panelen, a Másolás a **Alkalmazásazonosító** a vágólapra.

  ![1 alkalmazás azonosítója][api-management-howto-aad-b2c-app-id]

9. Váltson vissza az API Management **Hozzáadás identitásszolgáltató** ablaktábla és a Beillesztés Azonosítóját, a **ügyfél-azonosító** szövegmezőben.

  ![Kérelem azonosítója 2][api-management-howto-aad-b2c-client-id]

10. Váltson vissza a B2C-alkalmazás regisztrációja, kattintson a **kulcsok** gombra, majd **kulcs generálása**. Kattintson a **mentése** a konfiguráció mentéséhez és megjelenítéséhez a **Alkalmazáskulcs**. Másolja a vágólapra a kulcsot.

  ![Alkalmazáskulcs 1][api-management-howto-aad-b2c-app-key]

11. Váltson vissza az API Management **Hozzáadás identitásszolgáltató** ablaktáblán, majd illessze be a kulcsot a **titkos Ügyfélkód** szövegmezőben.

  ![2 alkalmazás-kulcs][api-management-howto-aad-b2c-client-secret]

12. Adja meg a tartomány nevét az Azure Active Directory B2C-bérlő **engedélyezett bérlő**.

  ![Az engedélyezett bérlő][api-management-howto-aad-b2c-allowed-tenant]

13. Adja meg a **előfizetési szabályzat** és **bejelentkezési házirend** a B2C-bérlő szabályzatokból származó. Igény szerint is megadhatja a **szerkesztési Profilházirendet** és **jelszó-visszaállítási házirend**.

  ![Házirendek][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > További információ a házirendek, lásd: [Az Azure Active Directory B2C: Bővíthető házirend-keretrendszer].

14. A szükséges konfiguráció megadása után kattintson a **mentése**.

  A módosítások mentése után a fejlesztők fog tudni új fiókok létrehozásához, és jelentkezzen be a fejlesztői portálra az Azure Active Directory B2C használatával.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C használatával egy fejlesztői fiók regisztrálása

1. Azure Active Directory B2C használatával egy fejlesztői fiókot regisztrálni, nyisson meg egy új böngészőablakot, és a fejlesztői portálon. Kattintson a **regisztráció** gombra.

   ![1 fejlesztői portálon][api-management-howto-aad-b2c-dev-portal]

2. Válassza ki a regisztráció **Azure Active Directory B2C**.

   ![Fejlesztői portál 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. A program átirányítja a regisztrációs szabályzat, amely az előző szakaszban konfigurált. Válassza ki az e-mail-címét vagy a meglévő közösségi fiókjaik egyikét a regisztrációhoz.

   > [!NOTE]
   > Az egyetlen lehetőség, hogy engedélyezve van az Azure Active Directory B2C-e a **identitások** fülre a közzétevő portálon átirányítjuk az előfizetési szabályzat közvetlenül.

   ![Fejlesztői portál][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Ha a regisztráció befejeződött, a program átirányítja vissza a fejlesztői portál. Ön már bejelentkezett a a fejlesztői portál az API Management szolgáltatáspéldányhoz tartozó.

    ![Regisztráció kész][api-management-registration-complete]

## <a name="next-steps"></a>További lépések

*  [Az Azure Active Directory B2C – áttekintés]
*  [Az Azure Active Directory B2C: Bővíthető házirend-keretrendszer]
*  [Microsoft-fiókot használni Identitásszolgáltatóként az Azure Active Directory B2C-vel]
*  [Identitás-szolgáltatóként az Azure Active Directory B2C egy Google-fiók használata]
*  [A LinkedIn-fiók használata Identitásszolgáltatóként az Azure Active Directory B2C-vel]
*  [Egy Facebook-fiók használata Identitásszolgáltatóként az Azure Active Directory B2C]



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

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Az Azure Active Directory B2C – áttekintés]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Hogyan lehet az Azure Active Directory fejlesztői fiókok engedélyezése]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Az Azure Active Directory B2C: Bővíthető házirend-keretrendszer]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Microsoft-fiókot használni Identitásszolgáltatóként az Azure Active Directory B2C-vel]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Identitás-szolgáltatóként az Azure Active Directory B2C egy Google-fiók használata]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Egy Facebook-fiók használata Identitásszolgáltatóként az Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[A LinkedIn-fiók használata Identitásszolgáltatóként az Azure Active Directory B2C-vel]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
