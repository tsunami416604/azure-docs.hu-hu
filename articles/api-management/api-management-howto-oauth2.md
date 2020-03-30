---
title: Fejlesztői fiókok engedélyezése az OAuth 2.0 használatával az API Management ben
titleSuffix: Azure API Management
description: Ismerje meg, hogyan engedélyezheti a felhasználók at OAuth 2.0 az API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 90c890925378c30ce5688d2713990b4b2cdd20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430680"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Fejlesztői fiókok engedélyezése az OAuth 2.0 használatával az Azure API Management ben

Számos API támogatja az [OAuth 2.0-s](https://oauth.net/2/) api-kat az API biztonságossá tétele érdekében, és biztosítja, hogy csak az érvényes felhasználók rendelkezzenek hozzáféréssel, és csak olyan erőforrásokhoz férhetnek hozzá, amelyekre jogosultak. Annak érdekében, hogy az Azure API Management interaktív fejlesztői konzolját ilyen API-kkal használhassa, a szolgáltatás lehetővé teszi a szolgáltatáspéldány konfigurálását az OAuth 2.0-s api-val való együttműködésre.

> [!IMPORTANT]
> Az OAuth 2.0-s engedélyezés még nem érhető el az új fejlesztői portál interaktív konzolján.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Előfeltételek

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management szolgáltatáspéldányt az OAuth 2.0-s engedélyezési szolgáltatás használatára a fejlesztői fiókokhoz, de nem mutatja meg, hogyan konfigurálhat egy OAuth 2.0-szolgáltatót. Az egyes OAuth 2.0-s szolgáltatók konfigurációja eltérő, bár a lépések hasonlóak, és az API Management szolgáltatáspéldány OAuth 2.0 konfigurálásához szükséges adatok megegyeznek. Ez a témakör példákat mutat be az Azure Active Directory OAuth 2.0-s szolgáltatóként való használatával.

> [!NOTE]
> Az OAuth 2.0 Azure Active Directory használatával történő konfigurálásáról a [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] mintában talál további információt.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>OAuth 2.0 engedélyezési kiszolgáló konfigurálása az API Management programban

> [!NOTE]
> Ha még nem hozott létre API Management szolgáltatáspéldányt, olvassa el az API Management szolgáltatáspéldány létrehozása című [témakört.][Create an API Management service instance]

1. Kattintson az OAuth 2.0 fülre a bal oldali menüben, és kattintson a **+Add gombra.**

    ![OAuth 2.0 menü](./media/api-management-howto-oauth2/oauth-01.png)

2. Írjon be egy nevet és egy megnevezést a **Név** és **a Leírás** mezőbe.

    > [!NOTE]
    > Ezek a mezők az OAuth 2.0 engedélyezési kiszolgáló azonosítására szolgálnak az aktuális API Management szolgáltatáspéldányon belül, és értékeik nem az OAuth 2.0-s kiszolgálóról származnak.

3. Adja meg az **ügyfél regisztrációs lapjának URL-címét**. Ezen a lapon a felhasználók létrehozhatják és kezelhetik a fiókjukat, és a használt OAuth 2.0 szolgáltatótól függően változnak. Az **Ügyfél regisztrációs lapjának URL-címe** arra a lapra mutat, amelyet a felhasználók saját fiókjuk létrehozásához és konfigurálásához használhatnak a fiókok felhasználói kezelését támogató OAuth 2.0-s szolgáltatók számára. Egyes szervezetek akkor sem konfigurálják és nem használják ezt a funkciót, ha az OAuth 2.0 szolgáltató támogatja azt. Ha az OAuth 2.0-s szolgáltatónem rendelkezik a fiókok felhasználói kezelésével, itt adjon meg egy `https://placeholder.contoso.com`helyőrző URL-címet, például a vállalat URL-címét vagy egy URL-t, például .

    ![OAuth 2.0 új szerver](./media/api-management-howto-oauth2/oauth-02.png)

4. Az űrlap következő szakasza tartalmazza az **engedélyezési támogatás típusait**, az **engedélyezési végpont URL-címét**és az **Engedélyezési kérelem metódusának** beállításait.

    Adja meg az **engedélyezési támogatás típusait** a kívánt típusok ellenőrzésével. **Az engedélyezési kód** alapértelmezés szerint meg van adva.

    Adja meg az **engedélyezési végpont URL-címét**. Az Azure Active Directory esetében ez az URL-cím hasonló lesz a következő URL-címhez, ahol `<tenant_id>` az Azure AD-bérlő azonosítója lesz.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    Az **engedélyezési kérelem metódusa** határozza meg, hogy az engedélyezési kérelem hogyan kerül-e az OAuth 2.0 kiszolgálóra. Alapértelmezés szerint **a GET** beállítás van kiválasztva.

5. Ezután meg kell adni **a Token végpont URL-címét**, **az ügyfélhitelesítési módszereket**, **az Access token küldési metódusát** és **az Alapértelmezett hatókört.**

    ![OAuth 2.0 új szerver](./media/api-management-howto-oauth2/oauth-03.png)

    Egy Azure Active Directory OAuth 2.0-s kiszolgáló esetén a **tokenvégpont URL-címe** a következő formátummal fog rendelkezni, ahol `<TenantID>` a formátuma. `yourapp.onmicrosoft.com`

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    **Az ügyfélhitelesítési módszerek** alapértelmezett beállítása **az Alapszintű**, **az Access token küldési módja** pedig az Engedélyezési **fejléc.** Ezek az értékek az űrlap ezen szakaszában vannak konfigurálva az **Alapértelmezett hatókörrel**együtt.

6. Az **Ügyfél hitelesítő adatai** szakasz tartalmazza az **Ügyfél-azonosító** és **az Ügyfél titkos kulcsot,** amelyet az OAuth 2.0-kiszolgáló létrehozása és konfigurációs folyamata során kapunk. Az **ügyfélazonosító** és az **ügyféltitkos lista** megadása után létrejön az **engedélyezési kód** **redirect_uri.** Ezzel az URI-val konfigurálhatja a válasz URL-címét az OAuth 2.0-s kiszolgáló konfigurációjában.

    ![OAuth 2.0 új szerver](./media/api-management-howto-oauth2/oauth-04.png)

    Ha **az engedélyezési támogatási típusok** **erőforrás-tulajdonosjelszó-ra**vannak állítva, az **Erőforrás-tulajdonos jelszóhitelesítő adatok szakasza** adja meg ezeket a hitelesítő adatokat; ellenkező esetben üresen hagyhatja.

    Miután az űrlap elkészült, kattintson a **Létrehozás** gombra az API Management OAuth 2.0 engedélyezési kiszolgáló konfigurációjának mentéséhez. A kiszolgáló konfigurációjának mentése után beállíthatja, hogy az API-k ezt a konfigurációt használják, ahogy az a következő szakaszban látható.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>API konfigurálása az OAuth 2.0 felhasználói hitelesítésének használatára

1. Kattintson az **API-k** a bal oldali **API Management** menüben.

    ![OAuth 2.0 API-k](./media/api-management-howto-oauth2/oauth-05.png)

2. Kattintson a kívánt API nevére, majd a **Beállítások gombra.** Görgessen a **Biztonság** szakaszhoz, majd jelölje be az **OAuth 2.0**jelölőnégyzetet.

    ![OAuth 2.0 beállítások](./media/api-management-howto-oauth2/oauth-06.png)

3. Válassza ki a kívánt **engedélyezési kiszolgálót** a legördülő listából, majd kattintson a **Mentés gombra.**

    ![OAuth 2.0 beállítások](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Örökölt fejlesztői portál - tesztelje az OAuth 2.0 felhasználói engedélyezést

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Miután konfigurálta az OAuth 2.0 engedélyezési kiszolgálót, és konfigurálta az API-t a kiszolgáló használatára, tesztelheti azt a fejlesztői portálon, és meghívhat egy API-t. Kattintson **a Fejlesztői portál (örökölt)** a felső menüben az Azure API Management példány **áttekintése** oldalon.

Kattintson az **API-k** elemre a felső menüben, és válassza az **Echo API**lehetőséget.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Ha csak egy API van konfigurálva, vagy csak egy API látható a fiókja számára, és rákattint az API-k elemre, az közvetlenül az API-hoz tartozó művelethez fogja vinni.

Válassza ki az **Erőforrás beadása** műveletet, kattintson a **Konzol megnyitása**gombra, majd válassza az Engedélyezési **kódot** a legördülő menüből.

![Konzol megnyitása][api-management-open-console]

Ha **az Engedélyezési kód** van kiválasztva, egy előugró ablak jelenik meg az OAuth 2.0 szolgáltató bejelentkezési formájával. Ebben a példában a bejelentkezési űrlapot az Azure Active Directory biztosítja.

> [!NOTE]
> Ha le tiltotta az előugró ablakokat, a böngésző kérni fogja, hogy engedélyezze őket. Miután engedélyezte őket, ismét válassza **ki az Engedélyezési kódot,** és megjelenik a bejelentkezési űrlap.

![Bejelentkezés][api-management-oauth2-signin]

Miután bejelentkezett, a **kérelem fejlécek** `Authorization : Bearer` feltölti egy fejléc, amely engedélyezi a kérelmet.

![Fejléctoken kérése][api-management-request-header-token]

Ezen a ponton konfigurálhatja a kívánt értékeket a fennmaradó paraméterekhez, és elküldheti a kérelmet.

## <a name="next-steps"></a>További lépések

Az OAuth 2.0 és az API Management használatáról az alábbi videóban és a kapcsolódó cikkben olvashat [bővebben.](api-management-howto-protect-backend-with-aad.md)

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
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

