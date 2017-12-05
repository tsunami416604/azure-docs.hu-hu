---
title: "Fejlesztői fiókok hitelesítéséhez az Azure Active Directory B2C - Azure API Management használatával |} Microsoft Docs"
description: "Tudnivalók a felhasználók hitelesítése az Azure Active Directory B2C segítségével az API Management."
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: d843757c2ed63c4f8cff09d809c2de382b2aeb59
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
> [!WARNING]
> Azure Active Directory B2C-integráció érhető el a [fejlesztői és a prémium](https://azure.microsoft.com/en-us/pricing/details/api-management/) csak tiers.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory B2C segítségével az Azure API Management
## <a name="overview"></a>Áttekintés
Az Azure Active Directory B2C egy felhőbeli identitáskezelő megoldás a felhasználók felé néző webes és mobilalkalmazásokhoz. A fejlesztői portálján hozzáférés kezelésére használható. Ez az útmutató bemutatja, a által előírt konfiguráció szerint az Azure Active Directory B2C-vel integrálni az API Management szolgáltatásban. A fejlesztői portálhoz való hozzáférés engedélyezése a klasszikus Azure Active Directory használatával kapcsolatos információkért lásd: [hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával].

> [!NOTE]
> Ez az útmutató lépéseinek végrehajtásához először az alkalmazás létrehozása az Azure Active Directory B2C-bérlő kell rendelkeznie. Emellett kell készen áll a regisztráció és bejelentkezés házirendet. További információkért lásd: [Azure Active Directory B2C áttekintése].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C segítségével hitelesíthetők a fejlesztői fiókok

1. A kezdéshez kattintson **Publisher portal** az API Management szolgáltatás az Azure portálon. Ezzel továbblép az API Management közzétevő portáljára.

   ![Közzétevő portál][api-management-management-console]

   > [!NOTE]
   > Ha még nem hozott az API Management szolgáltatáspéldány, lásd: [hozzon létre egy API-kezelés szolgáltatás példányt] [ Create an API Management service instance] a a [Ismerkedés az Azure API Management oktatóanyag][Get started with Azure API Management].

2. Az a **API Management** menüben kattintson a **biztonsági**. Az a **identitások** lapra, majd **Azure Active Directory B2C**.

  ![Külső identitások 1][api-management-howto-aad-b2c-security-tab]

3. Jegyezze fel a **átirányítási URL-cím** és Azure Active Directory B2C kapcsoljon át az Azure portálon.

  ![Külső identitások 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Kattintson a **alkalmazások** gombra.

  ![Egy új alkalmazás 1 regisztrálása][api-management-howto-aad-b2c-portal-menu]

5. Kattintson a **Hozzáadás** gombra kattintva hozzon létre egy új Azure Active Directory B2C-alkalmazást.

  ![Egy új alkalmazás 2 regisztrálása][api-management-howto-aad-b2c-add-button]

6. Az a **új alkalmazás** panelen adjon meg egy nevet az alkalmazás. Válasszon **Igen** alatt **Web App/Web API**, és válassza a **Igen** alatt **implicit engedélyezési folyamat engedélyezése**. Másolja a **átirányítási URL-cím** a a **Azure Active Directory B2C** szakasza a **identitások** a közzétevő portál lapot, és illessze be azt a **válasz URL-CÍMEN** szövegmezőben.

  ![Egy új alkalmazás 3 regisztrálása][api-management-howto-aad-b2c-app-details]

7. Kattintson a **Létrehozás** gombra. Ha az alkalmazás létrehozása az megjelenik a **alkalmazások** panelen. Kattintson a részletek megtekintéséhez az alkalmazás nevét.

  ![Egy új alkalmazás 4 regisztrálása][api-management-howto-aad-b2c-app-created]

8. Az a **tulajdonságok** panelen, a Másolás a **Alkalmazásazonosító** a vágólapra.

  ![1 alkalmazás azonosítója][api-management-howto-aad-b2c-app-id]

9. Lépjen vissza a közzétevő portálon, és illessze be az azonosító a **ügyfél-azonosító** szövegmezőben.

  ![Kérelem azonosítója 2][api-management-howto-aad-b2c-client-id]

10. Váltson vissza az Azure-portálon, kattintson a **kulcsok** gombra, majd **kulcs**. Kattintson a **mentése** a konfiguráció mentéséhez, majd megjeleníti a **Alkalmazáskulcs**. Másolja a vágólapra a kulcsot.

  ![1 alkalmazás kulcs][api-management-howto-aad-b2c-app-key]

11. Váltás a közzétevő portal, majd illessze be a kulcs a **Ügyfélkulcs** szövegmezőben.

  ![Alkalmazáskulcs 2][api-management-howto-aad-b2c-client-secret]

12. Adja meg a tartomány nevét az Azure Active Directory B2C-bérlő **engedélyezett bérlői**.

  ![Engedélyezett bérlői][api-management-howto-aad-b2c-allowed-tenant]

13. Adja meg a **előfizetési házirend** és **Signin házirend**. Szükség esetén is megadhatja a **profil szerkesztése házirend** és **jelszó-visszaállítási házirend**.

  ![Házirendek][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > A házirendekkel kapcsolatos további információkért lásd: [Azure Active Directory B2C: bővíthető szabályzat-keretrendszernek].

14. A megadott kívánt beállításait, kattintson **mentése**.

  Menti a módosításokat, miután a fejlesztők fog tudni új fiókok létrehozását, és jelentkezzen be a fejlesztői portálhoz Azure Active Directory B2C használatával.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C segítségével egy developer-fiók regisztrálása

1. Az Azure Active Directory B2C segítségével egy developer-fiók regisztrálása, nyisson meg egy új böngészőablakot, majd keresse meg a fejlesztői portálhoz. Kattintson a **regisztráljon** gombra.

   ![Fejlesztői portálján 1][api-management-howto-aad-b2c-dev-portal]

2. Regisztrálni kívánt **Azure Active Directory B2C**.

   ![Fejlesztői portálján 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. A program átirányítja az előfizetési házirend, az előzőekben konfigurált. Válassza ki az e-mail címet vagy egy meglévő közösségi fiók használatával regisztrálni.

   > [!NOTE]
   > Ha az Azure Active Directory B2C jelenleg az egyetlen lehetséges, hogy engedélyezve van a **identitások** lapon publisher portálon meg fogja átirányítani az előfizetési házirend közvetlenül.

   ![Fejlesztői portál][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Ha befejeződött a regisztráció, a program átirányítja vissza a fejlesztői portálján. Most bejelentkeztetjük a fejlesztői portálhoz az API Management szolgáltatáspéldány.

    ![Regisztráció kész][api-management-registration-complete]

## <a name="next-steps"></a>Következő lépések

*  [Azure Active Directory B2C áttekintése]
*  [Azure Active Directory B2C: bővíthető szabályzat-keretrendszernek]
*  [Az Azure Active Directory B2C identitás-szolgáltatóként a Microsoft-fiók használata]
*  [A Google-fiók használata az Azure Active Directory B2C identitás-szolgáltatóként]
*  [Az Azure Active Directory B2C identitás-szolgáltatóként LinkedIn fiók használata]
*  [Az Azure Active Directory B2C identitás-szolgáltatóként Facebook fiók használata]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
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
[Azure Active Directory B2C áttekintése]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: bővíthető szabályzat-keretrendszernek]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Az Azure Active Directory B2C identitás-szolgáltatóként a Microsoft-fiók használata]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[A Google-fiók használata az Azure Active Directory B2C identitás-szolgáltatóként]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Az Azure Active Directory B2C identitás-szolgáltatóként Facebook fiók használata]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Az Azure Active Directory B2C identitás-szolgáltatóként LinkedIn fiók használata]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
