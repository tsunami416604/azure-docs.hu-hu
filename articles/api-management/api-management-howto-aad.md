---
title: "Engedélyezi a fejlesztői fiókok Azure Active Directory - Azure API Management használata |} Microsoft Docs"
description: "Útmutató az Azure Active Directoryval az API Management felhasználók hitelesítéséhez."
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
ms.openlocfilehash: 3faa6c1867808436a66a2b33ea1a9d79ede2c8fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
> [!WARNING]
> Az Azure Active Directory-integráció érhető el a [fejlesztői és a prémium](https://azure.microsoft.com/en-us/pricing/details/api-management/) csak tiers.

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával az Azure API Management
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan engedélyezze a hozzáférést a fejlesztői portálhoz, a felhasználók számára az Azure Active Directoryból. Ez az útmutató is bemutatja, hogyan Azure Active Directory-felhasználók kezelése külső csoportok, amelyek tartalmazzák az Azure Active Directory a felhasználók hozzáadásával.

> Ez az útmutató lépéseinek végrehajtásához először egy Azure Active Directory használandó hozzon létre egy alkalmazást kell rendelkeznie.
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával
A kezdéshez kattintson **Publisher portal** az API Management szolgáltatás az Azure portálon. Ezzel továbblép az API Management közzétevő portáljára.

![Közzétevő portál][api-management-management-console]

> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Kattintson a **biztonsági** a a **API Management** a bal oldalon, majd kattintson a menü **külső identitások**.

![Külső identitások][api-management-security-external-identities]

Kattintson a **az Azure Active Directory**. Jegyezze fel a **átirányítási URL-cím** és átválthat a klasszikus Azure-portálon az Azure Active Directoryban.

![Külső identitások][api-management-security-aad-new]

Kattintson a **Hozzáadás** gombra kattintva hozzon létre egy új Azure Active Directory-alkalmazást, és válassza a **a szerveztem által fejlesztett alkalmazás hozzáadása**.

![Új Azure Active Directory-alkalmazás hozzáadása][api-management-new-aad-application-menu]

Adjon meg egy nevet az alkalmazásnak, válassza a **webes alkalmazáshoz és/vagy webes API**, és kattintson a Tovább gombra.

![Új Azure Active Directory-alkalmazás][api-management-new-aad-application-1]

A **bejelentkezési URL-cím**, adja meg a bejelentkezési URL-CÍMÉT a fejlesztői portálján. Ebben a példában a **bejelentkezési URL-cím** van `https://aad03.portal.current.int-azure-api.net/signin`. 

Az a **azonosító URL-címet**, adja meg az alapértelmezett tartomány vagy egyéni tartományhoz az Azure Active Directoryban, és egyedi hozzáfűzése. Ebben a példában az alapértelmezett tartomány **https://contoso5api.onmicrosoft.com** utótagjával rendelkező használt **/api** megadott.

![Új Azure Active Directory-alkalmazás tulajdonságai][api-management-new-aad-application-2]

Kattintson a pipa gombra mentéséhez és az alkalmazás létrehozása, majd átváltása a **konfigurálása** lapján adja meg az új alkalmazás.

![Új Azure Active Directory-alkalmazás létrehozása][api-management-new-aad-app-created]

Ha több aktív Azure-könyvtárak nem használható ehhez az alkalmazáshoz, kattintson a **Igen** a **alkalmazás több-bérlős**. Az alapértelmezett érték **nem**.

![Alkalmazás több-bérlős][api-management-aad-app-multi-tenant]

Másolás a **átirányítási URL-cím** a a **Azure Active Directory** szakasza a **külső identitások** a közzétevő portálon lapra, és illessze be azt a **válasz URL-cím** szövegmezőben. 

![Válasz-URL][api-management-aad-reply-url]

Görgessen az alsó részén a konfigurálása lapon válassza a **Alkalmazásengedélyek** legördülő, és ellenőrizze, **címtáradatok olvasása**.

![Alkalmazásengedélyek][api-management-aad-app-permissions]

Válassza ki a **engedélyek delegálása** legördülő, és ellenőrizze, **bejelentkezéssel, és olvassa el a felhasználói profilokon**.

![Delegált engedélyek][api-management-aad-delegated-permissions]

> További információ az alkalmazás és a delegált jogosultságokkal sikeresen telepítették: [fér hozzá a Graph API][Accessing the Graph API].
> 
> 

Másolás a **ügyfél-azonosító** a vágólapra.

![Ügyfél-azonosító][api-management-aad-app-client-id]

Lépjen vissza a közzétevő portálon, és illessze be a **ügyfél-azonosító** másolta át az Azure Active Directory-alkalmazás konfigurációja.

![Ügyfél-azonosító][api-management-client-id]

Váltson vissza az az Azure Active Directory konfigurációját, majd kattintson a **válassza ki a duration** a legördülő a **kulcsok** szakaszt, és adjon meg egy időközt. Ebben a példában **1 év** szolgál.

![Kulcs][api-management-aad-key-before-save]

Kattintson a **mentése** a konfiguráció mentéséhez, és a kulcs megjelenítéséhez. Másolja a vágólapra a kulcsot.

> Jegyezze fel ezt a kulcsot. Az Azure Active Directory konfigurációs ablak bezárása után a kulcs nem jeleníthető meg újra.
> 
> 

![Kulcs][api-management-aad-key-after-save]

Váltás a közzétevő portal, majd illessze be a kulcs a **Ügyfélkulcs** szövegmezőben.

![Titkos ügyfélkulcs][api-management-client-secret]

**Bérlők engedélyezett** határozza meg, mely könyvtárak van az API Management service-példány az API-k eléréséhez. Adja meg a tartomány, amelyhez hozzáférést szeretne az Azure Active Directory-példányok. Elkülönítheti a több tartomány ágyazódjanak, szóközzel vagy vesszővel válassza el egymástól.

![Engedélyezett bérlők][api-management-client-allowed-tenants]


Ha a szükséges konfiguráció van megadva, kattintson **mentése**.

![Mentés][api-management-client-allowed-tenants-save]

Ha menti a módosításokat, a felhasználók a megadott Azure Active Directoryban bármikor beléphet a fejlesztői portálhoz lépéseit követve [jelentkezzen be egy Azure Active Directory-fiókot a fejlesztői portálra] [ Log in to the Developer portal using an Azure Active Directory account].

Több tartomány is megadhatók a **engedélyezett bérlők** szakasz. Előtt minden olyan felhasználó, mint az eredeti tartomány, ahol az alkalmazás regisztrálva lett más tartományokból is bejelentkezhetnek, ugyanaz a tartományi globális rendszergazdájának engedélyeznie kell az alkalmazás hozzáférési címtáradatok. Adja meg az engedélyt, a globális rendszergazdának kell lépjen `https://<URL of your developer portal>/aadadminconsent` (például https://contoso.portal.azure-api.net/aadadminconsent), írja be a tartomány nevét annak az Active Directory-bérlő szeretnék a hozzáférést, és kattintson a Küldés gombra. A következő példában a globális rendszergazdájának `miaoaad.onmicrosoft.com` próbál engedélyt az adott fejlesztői portálra. 

![Engedélyek][api-management-aad-consent]

A következő képernyőn a globális rendszergazdai jogosultságot ad az engedély megerősítéséhez kéri. 

![Engedélyek][api-management-permissions-form]

> Ha a nem globális rendszergazda megpróbál bejelentkezni, mielőtt a globális rendszergazda által megadott engedélyekkel, a bejelentkezési kísérlet sikertelen lesz, és egy hiba történt a képernyő jelenik meg.
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Egy külső Azure Active Directory-csoport hozzáadása
Miután engedélyezte a hozzáférést a felhasználók számára egy Azure Active Directory, Azure Active Directory-csoportok is hozzáadhat, az API Management könnyebben kezelhetik a kívánt termékek a fejlesztők a csoport társítását.

> Egy külső Azure Active Directory-csoport konfigurálásához, az Azure Active Directory először konfigurálni kell az identitások lapon az alábbi eljárást az előző szakaszban. 
> 
> 

Külső Active Directory-csoportok hozzáadott a **látható** a terméket, amelynek kíván hozzáférést biztosítson a csoport lapján. Kattintson a **termékek**, majd kattintson a kívánt termék nevét.

![Termék konfigurálása][api-management-configure-product]

Váltás a **látható** fülre, majd **csoportok hozzáadása az Azure Active Directory**.

![Csoportok hozzáadása][api-management-add-groups]

Válassza ki a **Azure Active Directory-bérlő** a legördülő listában, és írja be a kívánt csoport neve a **csoportok** adhatók hozzá a szövegmezőben.

![Csoport kiválasztása][api-management-select-group]

A csoport neve megtalálható a **csoportok** listában az Azure Active Directory, az alábbi példában látható módon.

![Az Azure Active Directory-csoportok listája][api-management-aad-groups-list]

Kattintson a **Hozzáadás** ellenőrzéséhez a csoport nevét, és vegye fel azt a csoportot. Ebben a példában a **Contoso 5 fejlesztők** külső csoport hozzá van adva. 

![Csoport hozzáadva][api-management-aad-group-added]

Kattintson a **mentése** az új csoport mentéshez.

Miután egy Azure Active Directory csoport konfigurációja egy terméket, akkor érhető el ellenőrzi a **látható** az API Management szolgáltatáspéldány-egyéb termék a lapon.

Tekintse át, és azok hozzáadása után állítsa be a külső csoportok tulajdonságait, kattintson a csoport nevét a **csoportok** fülre.

![Csoportok kezelése][api-management-groups]

Itt szerkesztheti a **neve** és a **leírás** a csoport.

![Csoport szerkesztése][api-management-edit-group]

A konfigurált Azure Active Directory felhasználók jelentkezzen be a fejlesztői portálján, tekintse meg, és minden olyan csoportot, amelyeknél látható a következő szakaszban található utasításokat követve előfizetni.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>A fejlesztői portálra egy Azure Active Directory-fiókot bejelentkezés
A fejlesztői portálra a korábbi szakaszokban konfigurált Azure Active Directory-fiókkal bejelentkezni, nyisson meg egy új böngészőt ablak használatával a **bejelentkezési URL-cím** az Active Directory-alkalmazás konfigurációból kattintson**Az azure Active Directory**.

![Fejlesztői portálján][api-management-dev-portal-signin]

Adja meg az egyik felhasználó hitelesítő adatait az Azure Active Directoryban, és kattintson a **bejelentkezés**.

![Bejelentkezés][api-management-aad-signin]

Kérheti a regisztrációs űrlap Ha bármilyen további információkra szükség. Végezze el a regisztrációs képernyő, és kattintson a **regisztráljon**.

![Regisztráció][api-management-complete-registration]

A felhasználó most jelentkezett be a fejlesztői portálra, az API Management szolgáltatáspéldány.

![A regisztráció kész.][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
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
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

