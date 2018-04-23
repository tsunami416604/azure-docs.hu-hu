---
title: Engedélyezze az OAuth 2.0 verziót használja az Azure API Management fejlesztői fiókok |} Microsoft Docs
description: Útmutató a felhasználóknak az API Management OAuth 2.0 használatával engedélyezik.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 47011bf2cfde268bb6248e54e98930f3a0b522e4
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Hogyan szeretné engedélyekkel felruházni fejlesztői fiókok OAuth 2.0 használatával az Azure API Management
Számos API támogatja [OAuth 2.0](http://oauth.net/2/) biztosításához az API-t, és győződjön meg arról, hogy csak akkor érvényes, ha a felhasználók rendelkezhetnek hozzáféréssel, és csak, amely jogosult most erőforrások eléréséhez. Ahhoz, hogy Azure API Management interaktív Developer Console ilyen API-khoz, a szolgáltatás lehetővé teszi a szolgáltatáspéldány dolgozni az OAuth 2.0-s engedélyezett API konfigurálása.

## <a name="prerequisites"> </a>Előfeltételek
Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldány OAuth 2.0 hitelesítési fejlesztői fiókok használatára, de még nem jeleníti meg az OAuth 2.0-s szolgáltató konfigurálásával. Minden OAuth 2.0-s szolgáltató konfigurációja eltér, bár a lépések hasonlóak, és a szükséges információkat az API Management szolgáltatáspéldány OAuth 2.0 konfigurálásához használt azonos. Ez a témakör bemutatja az Azure Active Directoryt használja az OAuth 2.0-s szolgáltató példák.

> [!NOTE]
> Az Azure Active Directoryval OAuth 2.0 konfigurálásáról további információkért lásd: a [WebApp-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] minta.
> 
> 

## <a name="step1"> </a>Az OAuth 2.0 hitelesítési kiszolgáló konfigurálása az API Management
Első lépésként kattintson a **Közzétevő portál** elemre az API Management szolgáltatás Azure Portalján.

![Közzétevő portál][api-management-management-console]

> [!NOTE]
> Ha még nem hozott létre az API Management szolgáltatáspéldány, lásd: [hozzon létre egy API-kezelés szolgáltatás példányt][Create an API Management service instance].
> 
> 

Kattintson a **biztonsági** a a **API Management** menüjében kattintson a bal oldali, **OAuth 2.0**, és kattintson a **engedélyezési kiszolgáló hozzáadása**.

![OAuth 2.0][api-management-oauth2]

Miután rákattintott **engedélyezési kiszolgáló hozzáadása**, az új engedélyezési server képernyő jelenik meg.

![Új kiszolgáló][api-management-oauth2-server-1]

Adjon meg egy nevet és egy leírást a **neve** és **leírás** mezőket. 

> [!NOTE]
> Ezeket a mezőket az API Management aktuális szolgáltatáspéldányt a OAuth 2.0 hitelesítési kiszolgálót azonosítására szolgálnak, és értékeik nem az OAuth 2.0 kiszolgálóról származnak.
> 
> 

Adja meg a **ügyfél regisztrációs URL-címe**. Ez a lap, ahol a felhasználók létrehozása és azok a fiókok kezelése és a használt OAuth 2.0-s szolgáltató függ. A **ügyfél regisztrációs URL-címe** mutat, a felhasználók segítségével hozza létre és konfigurálja a saját felhasználói a felhasználói fiókok kezelését támogató OAuth 2.0-s szolgáltatók oldal. Egyes szervezetek konfigurálásához, vagy nem használja ezt a funkciót, még akkor is, ha az OAuth 2.0-s szolgáltató támogatja. Ha az OAuth 2.0-s szolgáltató nem rendelkezik konfigurált fiókok felhasználói kezelését, adja meg az egy helyőrző URL-CÍMÉT a vállalata, és egy URL-címe például például `https://placeholder.contoso.com`.

A következő szakaszban a következő formátumban tartalmazza az **engedélyezési kódot adjon típusok**, **engedélyezési végpont URL-címet**, és **engedélyezési metódus** beállításait.

![Új kiszolgáló][api-management-oauth2-server-2]

Adja meg a **engedélyezési kódot adjon típusok** a kívánt típusok ellenőrzésével. **Engedélyezési kód** alapértelmezés szerint van megadva.

Adja meg a **engedélyezési végpont URL-címet**. Az Azure Active Directory, az URL-cím a következő URL-címet, hasonló lesz ahol `<client_id>` az ügyfél-azonosító, amely azonosítja az alkalmazást az OAuth 2.0 kiszolgáló helyén.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

A **engedélyezési metódus** határozza meg, hogyan rendszer a hitelesítési kérelmet küld az OAuth 2.0-kiszolgálóhoz. Alapértelmezés szerint **beolvasása** van kiválasztva.

A következő szakaszban akkor, ha a **végponti URL-cím Token**, **ügyfél-hitelesítési módszerek**, **küldése metódus hozzáférési jogkivonat**, és **hatókör alapértelmezett** vannak megadva.

![Új kiszolgáló][api-management-oauth2-server-3]

Azure Active Directory OAuth 2.0-kiszolgáló esetén a **Token végponti URL-cím** lesz a következő formátumban, ahol `<APPID>` a formátuma a `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

Az alapértelmezett beállítás a **ügyfél-hitelesítési módszerek** van **alapvető**, és **küldése metódus hozzáférési jogkivonat** van **Authorization fejlécet**. Ezeket az értékeket ebben a szakaszban a következő formátumban, valamint konfigurálhatók a **hatókör alapértelmezett**.

A **ügyfél hitelesítő adatait** a szakasz a **ügyfél-azonosító** és **ügyfélkulcs**, amely akkor kapja meg az OAuth 2.0-kiszolgáló létrehozása és a konfigurációs folyamat során. Egyszer a **ügyfél-azonosító** és **ügyfélkulcs** vannak megadva, a **redirect_uri** a a **engedélyezési kód** jön létre. Ezt az URI a válasz URL-CÍMEK konfigurálása az OAuth 2.0-kiszolgálói konfigurációban használatos.

![Új kiszolgáló][api-management-oauth2-server-4]

Ha **engedélyezési kódot adjon típusok** értéke **erőforrás tulajdonosi jelszó**, a **erőforrás tulajdonosa jelszavas hitelesítő adatokat** szakasz ezen hitelesítő adatok megadására szolgál; ellenkező esetben üresen hagyhatja, hogy.

![Új kiszolgáló][api-management-oauth2-server-5]

Ha az űrlap befejeződött, kattintson a **mentése** a API Management OAuth 2.0 hitelesítési kiszolgáló konfigurációjának mentéséhez. Után a kiszolgáló konfigurációjának mentése API-k használja ezt a konfigurációt a következő szakaszban ismertetett módon állíthatja be.

## <a name="step2"> </a>Egy API-t OAuth 2.0 felhasználói engedélyezési használandó konfigurálása
Kattintson a **API-k** a a **API Management** menüjében a bal oldali, kattintson a kívánt API neve kattintson **biztonsági**, és ezután jelölje be a **OAuth 2.0**.

![Felhasználó engedélyezése][api-management-user-authorization]

Válassza ki a kívánt **engedélyezési server** a legördülő listából, és kattintson a **mentése**.

![Felhasználó engedélyezése][api-management-user-authorization-save]

## <a name="step3"> </a>A fejlesztői portálra az OAuth 2.0 felhasználói hitelesítés tesztelése
Miután beállította az OAuth 2.0 hitelesítési kiszolgáló és az API-t, hogy a kiszolgáló használatára konfigurált, tesztelheti a fejlesztői portálhoz fog, és az API felület meghívásakor.  Kattintson a **Fejlesztői portál** lehetőségre a jobb felső menüben.

![Fejlesztői portál][api-management-developer-portal-menu]

Kattintson a **API-k** a felső menüben, és válasszon **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Ha csak egy API van konfigurálva, vagy csak egy API látható a fiókja számára, és rákattint az API-k elemre, az közvetlenül az API-hoz tartozó művelethez fogja vinni.
> 
> 

Válassza ki a **erőforrás beolvasása** műveletet, kattintson a **nyissa meg a konzolt**, majd válassza ki **engedélyezési kód** a legördülő.

![Konzol megnyitása][api-management-open-console]

Ha **engedélyezési kód** van jelölve, egy előugró ablak jelenik meg, amely a bejelentkezési képernyőn az OAuth 2.0-s szolgáltató. Ebben a példában a bejelentkezési képernyőn Azure Active Directory által biztosított.

> [!NOTE]
> Ha az előugró ablakok le van tiltva és lehetővé teszi a böngésző kéri. Miután engedélyezte őket, válassza ki a **engedélyezési kód** újra és a bejelentkezési képernyőn megjelenik.
> 
> 

![Bejelentkezés][api-management-oauth2-signin]

Miután bejelentkezett, a **kérelem fejlécei** kerülnek egy `Authorization : Bearer` fejlécet tartalmazta, amely engedélyezi a kérelmet.

![Kérelem fejléc jogkivonat][api-management-request-header-token]

Ezen a ponton konfigurálja a kívánt értékeket, a többi paraméter, és küldje el a kérelmet. 

## <a name="next-steps"></a>További lépések
OAuth 2.0-s és API-kezelés használatával kapcsolatos további információkért tekintse meg a következő videó és kísérő [cikk](api-management-howto-protect-backend-with-aad.md).


[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

