---
title: OAuth 2.0-val az Azure API Management fejlesztői fiókok engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a felhasználók az API Management OAuth 2.0 használatával.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: b195271edeea6cd5ea527454ad1615ac85a32138
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746727"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Hogyan lehet OAuth 2.0-val az Azure API Management fejlesztői fiókok engedélyezése

Több API-t támogatja [OAuth 2.0](https://oauth.net/2/) biztosításához az API-t, és győződjön meg arról, hogy csak akkor érvényes, ha a felhasználók hozzáférhetnek, és csak, amelyre jogosult használt erőforrások eléréséhez. Ilyen API-k Azure API Management interaktív fejlesztői konzol használatához a szolgáltatás lehetővé teszi a service-példányt szeretne dolgozni az OAuth 2.0-s engedélyezett API konfigurálása.

## <a name="prerequisites"> </a>Előfeltételek

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldányt, fejlesztői fiókok esetében az OAuth 2.0 engedélyezési használatára, de még nem jeleníti meg az OAuth 2.0-s szolgáltató konfigurálása. Minden egyes OAuth 2.0-s szolgáltató konfigurációja eltér, bár a lépések hasonlóak, és a szükséges információt az API Management szolgáltatáspéldányhoz az OAuth 2.0 konfigurálása a használt azonosak. Ez a témakör bemutatja az Azure Active Directory használatával, az OAuth 2.0-s szolgáltató példák.

> [!NOTE]
> OAuth 2.0, az Azure Active Directory konfigurálásával kapcsolatos további információkért lásd: a [WebApp-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] minta.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Az OAuth 2.0 engedélyezési kiszolgáló konfigurálása az API Management

> [!NOTE]
> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg [az API Management szolgáltatáspéldány létrehozása][Create an API Management service instance].

1. Kattintson a bal oldali menüben, az OAuth 2.0-s lapon, majd kattintson a **+ Hozzáadás**.

    ![OAuth 2.0-s menü](./media/api-management-howto-oauth2/oauth-01.png)

2. Adjon meg egy nevet és egy leírást a **neve** és **leírás** mezőket.

    > [!NOTE]
    > Ezek a mezők belül az aktuális API Management szolgáltatáspéldányt, az OAuth 2.0 engedélyezési kiszolgáló azonosítására szolgál, és azok értékeit az OAuth 2.0-kiszolgálóról származott.

3. Adja meg a **ügyfél-regisztrációs lap URL-címe**. Ez a lap, ahol a felhasználók létrehozása és a fiókjaik kezelését és az OAuth 2.0-s szolgáltató függően változik. A **ügyfél-regisztrációs lap URL-címe** mutat, a lap, amelyen a felhasználók létrehozása és konfigurálása saját fiókok, amelyek támogatják a felhasználói fiókok kezelését OAuth 2.0-s szolgáltatók számára. Egyes szervezetek ne konfigurálja, és használja ezt a funkciót, még akkor is, ha támogatja az OAuth 2.0-s szolgáltató. Ha az OAuth 2.0-s szolgáltató nem rendelkezik konfigurált fiókok felhasználói kezelését, írja be az egy helyőrző URL-címe itt például az URL-címét a vállalati vagy egy URL-címe például `https://placeholder.contoso.com`.

    ![OAuth 2.0 új kiszolgálóra](./media/api-management-howto-oauth2/oauth-02.png)

4. Az űrlap a következő szakasz tartalmazza a **engedélyezéstípusok**, **engedélyezési végpont URL-címe**, és **engedélyezési kérelmi metódus** beállításait.

    Adja meg a **engedélyezéstípusok** a kívánt típusú ellenőrzésével. **Engedélyezési kód** alapértelmezés szerint van megadva.

    Adja meg a **engedélyezési végpont URL-címe**. Az Azure Active Directory, az URL-cím a következő URL-címet, hasonló lesz ahol `<client_id>` az ügyfél-azonosítót, amely azonosítja az alkalmazást az OAuth 2.0-kiszolgáló helyére.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    A **engedélyezési kérelmi metódus** Itt adhatja meg, hogyan az engedélyezési kérést küld az OAuth 2.0-kiszolgáló. Alapértelmezés szerint **első** van kiválasztva.

5. Ezt követően **jogkivonat-végpont URL-címe**, **ügyfél-hitelesítési módszer**, **hozzáférési jogkivonatok elküldésének módja** és **alapértelmezett hatókör** kell lennie a megadott.

    ![OAuth 2.0 új kiszolgálóra](./media/api-management-howto-oauth2/oauth-03.png)

    Egy Azure Active Directory OAuth 2.0-kiszolgáló a **jogkivonat-végpont URL-címe** lesz a következő formátumban, ahol `<TenantID>` formátuma alább látható a `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Az alapértelmezett beállítás a **ügyfél-hitelesítési módszer** van **alapszintű**, és **hozzáférési jogkivonatok elküldésének módja** van **engedélyeztetési fejléc**. Ezeket az értékeket a történik az űrlap ebben a szakaszban a **alapértelmezett hatókör**.

6. A **ügyfél-hitelesítő adatok** szakasz tartalmazza a **ügyfél-azonosító** és **titkos Ügyfélkód**, amely kapnak, az OAuth 2.0-kiszolgáló létrehozása és konfigurálása során . Egyszer a **ügyfél-azonosító** és **titkos Ügyfélkód** meg van adva, a **redirect_uri** számára a **engedélyezési kód** jön létre. Ez az URI segítségével konfigurálhatja a válasz-URL az OAuth 2.0-kiszolgálói konfigurációban.

    ![OAuth 2.0 új kiszolgálóra](./media/api-management-howto-oauth2/oauth-04.png)

    Ha **engedélyezéstípusok** értékre van állítva **erőforrás-tulajdonos jelszava**, a **erőforrás tulajdonosának jelszavas hitelesítő adatai** szakaszban használt hitelesítő adatokat megadnia; egyéb akkor is üresen hagyhatja.

    Az űrlap befejeződése után kattintson a **létrehozás** az API Management az OAuth 2.0 engedélyezési kiszolgáló konfigurációjának mentéséhez. A kiszolgáló konfigurációját a mentés után konfigurálhat API-k segítségével használja ezt a konfigurációt, a következő szakaszban látható módon.

## <a name="step2"> </a>API-t használja az OAuth 2.0-s felhasználói hitelesítés konfigurálása

1. Kattintson a **API-k** származó a **az API Management** a bal oldali menüben.

    ![Az OAuth 2.0 API-k](./media/api-management-howto-oauth2/oauth-05.png)

2. Kattintson a kívánt API-t, majd kattintson a nevére **beállítások**. Görgessen a **biztonsági** szakaszt, és ezután jelölje be a **OAuth 2.0**.

    ![OAuth 2.0-beállítások](./media/api-management-howto-oauth2/oauth-06.png)

3. Válassza ki a kívánt **az engedélyezési kiszolgáló** a legördülő listából válassza ki, majd kattintson a **mentése**.

    ![OAuth 2.0-beállítások](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Az OAuth 2.0 felhasználói hitelesítést tesztelése a fejlesztői portálon

Miután beállította az OAuth 2.0 engedélyezési kiszolgáló és az API-t, hogy a kiszolgáló használatához konfigurált, tesztelheti a fejlesztői portál és az API-k hívása által.  Kattintson a **fejlesztői portál** a felső menüben, az Azure API Management-példány **áttekintése** lapot.

![Fejlesztői portál][api-management-developer-portal-menu]

Kattintson a **API-k** a felső menüben, és válasszon **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Ha csak egy API van konfigurálva, vagy csak egy API látható a fiókja számára, és rákattint az API-k elemre, az közvetlenül az API-hoz tartozó művelethez fogja vinni.

Válassza ki a **GET Resource** műveletet, kattintson a **konzol megnyitása**, majd válassza ki **engedélyezési kód** a legördülő listából.

![Konzol megnyitása][api-management-open-console]

Amikor **engedélyezési kód** van jelölve, egy előugró ablak jelenik meg a bejelentkezési képernyőn az OAuth 2.0-s szolgáltató. Ebben a példában a bejelentkezési képernyő Azure Active Directory által biztosított.

> [!NOTE]
> Ha az előugró ablakok le van tiltva van kérni fogja engedélyezni kell a böngészőben. Miután engedélyezte őket, válassza ki a **engedélyezési kód** újra, és a bejelentkezési képernyőn jelennek meg.

![Bejelentkezés][api-management-oauth2-signin]

Miután bejelentkezett, a **Kérésfejlécek** feltöltött egy `Authorization : Bearer` fejlécet, amely engedélyezi a kérelmet.

![Kérés fejlécében lévő biztonsági jogkivonat][api-management-request-header-token]

Ezen a ponton állítsa be a kívánt értékeket a többi paraméterek, és küldje el a kérelmet.

## <a name="next-steps"></a>További lépések

OAuth 2.0 és az API Management használatával kapcsolatos további információkért lásd az alábbi videó és a hozzájuk tartozó [cikk](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

