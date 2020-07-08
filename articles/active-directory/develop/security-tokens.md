---
title: Biztonsági jogkivonatok | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a biztonsági jogkivonatok alapjairól a Microsoft Identity platformon (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 88dc4bb86459cd0390c4c01deb871aa93e39c6d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266746"
---
# <a name="security-tokens"></a>Biztonsági jogkivonatok

A központosított identitás-szolgáltató különösen olyan alkalmazások esetében hasznos, amelyeken a világon található felhasználók nem feltétlenül jelentkeznek be a vállalati hálózatról. A Microsoft Identity platform hitelesíti a felhasználókat, és biztonsági jogkivonatokat biztosít, például [hozzáférési tokent](developer-glossary.md#access-token), [frissítési jogkivonatot](developer-glossary.md#refresh-token)és [azonosító](developer-glossary.md#id-token)jogkivonatot, amely lehetővé teszi, hogy az [ügyfélalkalmazás](developer-glossary.md#client-application) hozzáférjen a védett erőforrásokhoz az [erőforrás-kiszolgálón](developer-glossary.md#resource-server).

A **hozzáférési jogkivonat** olyan biztonsági jogkivonat, amelyet egy [engedélyezési kiszolgáló](developer-glossary.md#authorization-server) állít ki egy [OAuth 2,0](active-directory-v2-protocols.md) folyamat részeként. A felhasználóval és az alkalmazással kapcsolatos információkat tartalmaz, amelyekhez a jogkivonat készült. a webes API-k és más védett erőforrások elérésére használható. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan bocsát ki hozzáférési jogkivonatokat, tekintse meg a [hozzáférési jogkivonatokat](access-tokens.md).

A hozzáférési jogkivonatok csak rövid ideig érvényesek, ezért az engedélyezési kiszolgálók időnként kiállítanak egy **frissítési jogkivonatot** a hozzáférési jogkivonat kikiadásakor. Az ügyfélalkalmazás ezután szükség esetén átadhatja ezt a frissítési tokent egy új hozzáférési jogkivonathoz. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan használja a frissítési jogkivonatokat az engedélyek visszavonására, lásd: [jogkivonat-visszavonás](access-tokens.md#token-revocation)

Az **azonosító jogkivonatokat** az ügyfélalkalmazás az [OpenID Connect](v2-protocols-oidc.md) folyamat részeként elküldi az alkalmazásnak. A felhasználók az oldalon vagy a hozzáférési jogkivonat helyett is elküldhetők, és az ügyfél a felhasználó hitelesítésére használja. Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan okoz problémát azonosító jogkivonatokat, tekintse meg az [azonosító jogkivonatokat](id-tokens.md).

> [!NOTE]
> Ez a cikk a OAuth2 és az OpenID Connect protokollok által használt biztonsági jogkivonatokat ismerteti. Számos vállalati alkalmazás SAML-t használ a felhasználók hitelesítéséhez. Az SAML-kijelentésekkel kapcsolatos információkért tekintse meg az [Azure ad SAML-jogkivonat referenciáját](reference-saml-tokens.md) .

## <a name="validating-security-tokens"></a>Biztonsági jogkivonatok ellenőrzése

Ez az alkalmazás, amelyhez a jogkivonat létrejött, a felhasználó által bejelentkezett webalkalmazást vagy a meghívott webes API-t a jogkivonat érvényesítéséhez. A tokent a **biztonsági jogkivonat-kiszolgáló (STS)** írja alá titkos kulccsal. Az STS közzéteszi a megfelelő nyilvános kulcsot. A jogkivonat érvényesítéséhez az alkalmazás ellenőrzi az aláírást az STS nyilvános kulcsának használatával annak ellenőrzéséhez, hogy az aláírás a titkos kulccsal lett-e létrehozva.

A tokenek csak korlátozott időtartamra érvényesek. Az STS általában egy pár tokent biztosít:

* Hozzáférési token az alkalmazáshoz vagy a védett erőforráshoz való hozzáféréshez, valamint
* A hozzáférési jogkivonat frissítéséhez használt frissítési jogkivonat, ha a hozzáférési jogkivonat lejár.

Hozzáférési jogkivonatok átadása egy webes API-nak a fejlécben szereplő tulajdonosi jogkivonatként `Authorization` . Egy alkalmazás frissítési tokent biztosíthat az STS számára, és ha az alkalmazáshoz való hozzáférés nem lett visszavonva, egy új hozzáférési tokent és egy új frissítési jogkivonatot fog kapni. Így történik a vállalatot elhagyó személy forgatókönyvének kezelése. Ha az STS megkapja a frissítési jogkivonatot, nem ad ki másik érvényes hozzáférési jogkivonatot, ha a felhasználó már nem rendelkezik jogosultsággal.

## <a name="json-web-tokens-jwts-and-claims"></a>JSON webes tokenek (JWTs) és jogcímek

A Microsoft Identity platform a **jogcímeket**tartalmazó **JSON webes tokenként (JWTs)** implementálja a biztonsági jogkivonatokat. Mivel a JWTs biztonsági jogkivonatként használják, ezt a hitelesítési űrlapot néha JWT- **hitelesítésnek**is nevezik.

A [jogcím](developer-glossary.md#claim) egy entitásra, például egy ügyfélalkalmazás vagy [erőforrás-tulajdonosra](developer-glossary.md#resource-owner)vonatkozó kijelentéseket biztosít egy másik entitáshoz, például egy erőforrás-kiszolgálóhoz. A jogcímek JWT jogcímként vagy JSON Web Token jogcímként is szerepelhetnek.

A jogcímek olyan név/érték párok, amelyek a jogkivonat tárgyával kapcsolatos adatokat továbbítanak. Előfordulhat például, hogy a jogcím az engedélyezési kiszolgáló által hitelesített rendszerbiztonsági tag tényeit is tartalmazza. Az adott jogkivonatban található jogcímek számos dologtól függenek, beleértve a token típusát, a tulajdonos hitelesítéséhez használt hitelesítő adatokat, az alkalmazás konfigurációját stb.

Az alkalmazások különféle feladatokhoz használhatják a jogcímeket, például:

* A jogkivonat ellenőrzése
* A token tulajdonos [bérlőjának](developer-glossary.md#tenant) azonosítása
* Felhasználói adatok megjelenítése
* A tulajdonos hitelesítésének meghatározása

A jogcím kulcs-érték párokból áll, amelyek olyan információkat biztosítanak, mint például a:

* A jogkivonatot létrehozó biztonsági jogkivonat-kiszolgáló
* A jogkivonat létrehozásának dátuma
* Tárgy (például a felhasználó – a démonok kivételével)
* A célközönség, amely az az alkalmazás, amelyhez a jogkivonat létrejött
* Az alkalmazás (a-ügyfél), amely a tokent kéri. A webalkalmazások esetében ez lehet ugyanaz, mint a célközönség

Ha többet szeretne megtudni arról, hogy a Microsoft Identity platform hogyan valósítja meg a jogkivonatokat és a jogcímeket, tekintse meg a [hozzáférési jogkivonatok](access-tokens.md) és [azonosító jogkivonatok](id-tokens.md)

## <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki

Az ügyfél felépítésének módjától függően a Microsoft Identity platform által támogatott hitelesítési folyamatok közül egy (vagy több) is használható. Ezek a folyamatok különféle tokeneket (azonosító jogkivonatokat, frissítési tokeneket, hozzáférési tokeneket) és engedélyezési kódokat hozhatnak létre, és különböző jogkivonatokat igényelnek a működésük érdekében. Ez a diagram áttekintést nyújt:

|Folyamat | Igényel | AZONOSÍTÓ token | hozzáférési jogkivonat | jogkivonat frissítése | engedélyezési kód |
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC folyamat](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Jogkivonat-beváltások frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | jogkivonat frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazás)| | |

Az implicit módban kiállított tokenek hossza korlátozott, mert az URL-cím (ahol a vagy a) a böngészőbe kerül vissza `response_mode` `query` `fragment` .  Egyes böngészőkben korlátozva van a böngészőablakban elhelyezhető URL-cím mérete, és a művelet nem hajtható végre, ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek `groups` vagy `wids` jogcímek.

## <a name="next-steps"></a>További lépések

A hitelesítési és engedélyezési alapismeretekkel kapcsolatos egyéb témakörökhöz:

* Tekintse meg a [hitelesítés](authentication-vs-authorization.md) és engedélyezés című témakört, amely a Microsoft Identity platform hitelesítésének és engedélyezésének alapvető fogalmait ismerteti.
* Tekintse meg az alkalmazás [modelljét](application-model.md) , amelyből megismerheti az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal.
* Az [alkalmazás bejelentkezési folyamata](app-sign-in-flow.md) című témakörben megismerheti a webes, asztali és mobil alkalmazások bejelentkezési folyamatát a Microsoft Identity platformon.
