---
title: Az alkalmazások típusai a Microsoft Identity platformhoz | Azure
description: A Microsoft Identity platform (v 2.0) végpont által támogatott alkalmazások és forgatókönyvek típusai.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21f1da8738946078cb625c76e11ce1bfd62d97ac
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927162"
---
# <a name="application-types-for-microsoft-identity-platform"></a>A Microsoft Identity platform alkalmazás-típusai

A Microsoft Identity platform (v 2.0) végpontja támogatja a különböző modern alkalmazás-architektúrák hitelesítését, amelyek mindegyike az iparági szabványnak megfelelő protokollok, az [2,0-es vagy az OpenID Connect-OAuth](active-directory-v2-protocols.md)alapul. Ez a cikk a Microsoft Identity platform használatával felépíthető alkalmazások típusait ismerteti, az Ön által választott nyelvtől és platformtól függetlenül. Az információk célja, hogy segítsen megérteni a magas szintű forgatókönyveket, mielőtt [elkezdi a használatot a kóddal](v2-overview.md#getting-started).

> [!NOTE]
> A Microsoft Identity platform végpontja nem támogatja az összes Azure Active Directory (Azure AD) forgatókönyvet és funkciót. Annak megállapításához, hogy a Microsoft Identity platform-végpontot kell-e használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

## <a name="the-basics"></a>Az alapok

Regisztrálnia kell minden olyan alkalmazást, amely a Microsoft Identity platform-végpontot használja az új [Alkalmazásregisztrációk portálon](https://go.microsoft.com/fwlink/?linkid=2083908). Az alkalmazás regisztrációs folyamata összegyűjti és hozzárendeli ezeket az értékeket az alkalmazáshoz:

* Egy **alkalmazás-(ügyfél-) azonosító** , amely egyedileg azonosítja az alkalmazást
* Egy **átirányítási URI** , amely a válaszok visszairányítására használható az alkalmazásba
* Néhány más forgatókönyv-specifikus érték, például a támogatott fióktípus

Részletekért olvassa el az [alkalmazások regisztrálását](quickstart-register-app.md)ismertető témakört.

Az alkalmazás regisztrálása után az alkalmazás kommunikál a Microsoft Identity platformmal, ha kéréseket küld a végpontnak. Nyílt forráskódú keretrendszereket és könyvtárakat biztosítunk, amelyek kezelik a kérelmek részleteit. Lehetősége van arra is, hogy saját maga is megvalósítsa a hitelesítési logikát, ha kéréseket hoz létre ezekhez a végpontokhoz:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Egyoldalas alkalmazások (JavaScript)

Számos modern alkalmazás rendelkezik egy egyoldalas alkalmazás előtérrel, amely elsősorban JavaScript nyelven íródott. Ez gyakran egy olyan keretrendszer használatával van írva, mint a szögletes, a reakciós vagy a Vue. A Microsoft Identity platform végpontja a [OAuth 2,0 implicit folyamat](v2-oauth2-implicit-grant-flow.md)használatával támogatja ezeket az alkalmazásokat.

Ebben a folyamatban az alkalmazás jogkivonatokat kap közvetlenül a Microsoft Identity platform engedélyezés végponttól, kiszolgáló és kiszolgáló közötti csere nélkül. Az összes hitelesítési logika és munkamenet-kezelés teljes egészében a JavaScript-ügyfélen történik, és nem kell külön oldalt átirányítani.

![Az implicit hitelesítési folyamat megjelenítése](./media/v2-app-types/convergence-scenarios-implicit.svg)

Ha ezt a forgatókönyvet működés közben szeretné látni, próbálja ki a [Microsoft Identity platform első lépések](v2-overview.md#getting-started) szakaszának egyoldalas alkalmazás kódjának mintáit.

## <a name="web-apps"></a>Webalkalmazások

Webalkalmazásokhoz (.NET, PHP, Java, Ruby, Python, node) a felhasználó egy böngészőben keresztül fér hozzá, az [OpenID Connect](active-directory-v2-protocols.md) felhasználói bejelentkezéshez használható. Az OpenID Connect szolgáltatásban a webalkalmazás megkapja az azonosító tokent. Az azonosító jogkivonat egy biztonsági jogkivonat, amely ellenőrzi a felhasználó identitását, és a jogcímek formájában szolgáltat információkat a felhasználóról:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

A Microsoft Identity platform végpontjában használt különböző típusú jogkivonatok további részletei a [hozzáférési jogkivonat](access-tokens.md) referenciájában és [id_token dokumentációjában](id-tokens.md) olvashatók.

A webkiszolgáló alkalmazásokban a bejelentkezési hitelesítési folyamat a következő magas szintű lépéseket hajtja végre:

![A webalkalmazás-hitelesítési folyamat megjelenítése](./media/v2-app-types/convergence-scenarios-webapp.svg)

A felhasználó identitását úgy ellenőrizheti, hogy az azonosító tokent a Microsoft Identity platform végpontján fogadott nyilvános aláíró kulccsal ellenőrzi. Be van állítva egy munkamenet-cookie, amely a felhasználó azonosítására szolgál a következő lapokra vonatkozó kérelmeknél.

Ha ezt a forgatókönyvet működés közben szeretné látni, próbálja ki a webalkalmazás bejelentkezési kódjának mintáit a [Microsoft Identity platform első lépések](v2-overview.md#getting-started) szakaszában.

Az egyszerű bejelentkezés mellett előfordulhat, hogy egy webkiszolgáló alkalmazásnak hozzá kell férnie egy másik webszolgáltatáshoz, például egy REST API. Ebben az esetben a webkiszolgáló alkalmazás egy kombinált OpenID Connect és OAuth 2,0 flow-t alkalmaz a [OAuth 2,0 engedélyezési kód folyamatával](active-directory-v2-protocols.md). További információ erről a forgatókönyvről: [Ismerkedés a Web Apps szolgáltatással és a webes API-](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)kkal.

## <a name="web-apis"></a>Webes API-k

A Microsoft Identity platform végpontján keresztül biztonságossá teheti a webszolgáltatásokat, például az alkalmazás REST-alapú webes API-ját. A webes API-kat számos platformon és nyelven lehet megvalósítani. A Azure Functions HTTP-eseményindítók használatával is megvalósítható. Az azonosító tokenek és a munkamenet-cookie-k helyett a webes API egy OAuth 2,0 hozzáférési tokent használ az adatai védelméhez és a bejövő kérések hitelesítéséhez. A webes API hívója hozzáfűz egy hozzáférési jogkivonatot egy HTTP-kérelem engedélyezési fejlécében, például a következőhöz:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A webes API a hozzáférési jogkivonattal ellenőrzi az API-hívó identitását, és Kinyeri a hívó információit a hozzáférési jogkivonatban kódolt jogcímekről. A Microsoft Identity platform végpontjában használt különböző típusú jogkivonatok további részletei a [hozzáférési jogkivonat](access-tokens.md) referenciájában és [id_token dokumentációjában](id-tokens.md) olvashatók.

A webes API-k lehetővé teszik a felhasználók számára, hogy a jogosultságok, más néven [hatókörök](v2-permissions-and-consent.md)megadásával vagy letiltásával eldönthetik, hogy bizonyos funkciókat vagy adatokból választhatnak. Ahhoz, hogy egy hívó alkalmazás egy hatókörhöz engedélyt szerezzen, a felhasználónak a folyamat során hozzá kell járulnia a hatókörhöz. A Microsoft Identity platform végpontja kéri a felhasználótól az engedélyt, majd rögzíti az engedélyeket a webes API által fogadott összes hozzáférési jogkivonatban. A webes API ellenőrzi az egyes hívásokhoz kapott hozzáférési jogkivonatokat, és végrehajtja az engedélyezési ellenőrzéseket.

A webes API-k bármilyen típusú alkalmazásból fogadhatnak hozzáférési jogkivonatokat, beleértve a webkiszolgáló alkalmazásokat, az asztali és a mobil alkalmazásokat, az egyoldalas alkalmazásokat, a kiszolgálóoldali démonokat és akár más webes API-kat is. A webes API-k magas szintű folyamata így néz ki:

![A webes API-hitelesítési folyamat megjelenítése](./media/v2-app-types/convergence-scenarios-webapi.svg)

Ha meg szeretné tudni, hogyan védheti a webes API-t a OAuth2 hozzáférési tokenek használatával, tekintse meg a webes API-kód mintáit a [Microsoft Identity platform első lépések](v2-overview.md#getting-started) szakaszában.

Sok esetben a webes API-knak a Microsoft Identity platform által védett más, az alsóbb rétegbeli webes API-khoz is el kell végezniük a kimenő kérelmeket. Ehhez a webes API **-** k kihasználhatják a folyamaton kívüli folyamatot, amely lehetővé teszi a webes API számára a bejövő hozzáférési tokenek cseréjét egy másik hozzáférési jogkivonat számára, amelyet a kimenő kérésekben használni fog. További információ: [Microsoft Identity platform és OAuth 2,0 on-Half-of flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobil- és natív alkalmazások

Az eszközre telepített alkalmazások, például a mobil-és asztali alkalmazások, gyakran olyan háttér-szolgáltatásokhoz vagy webes API-khoz szükségesek, amelyek az adatok tárolását és a függvények a felhasználók nevében való elvégzését végzik. Ezek az alkalmazások a [OAuth 2,0 engedélyezési kód folyamatával](v2-oauth2-auth-code-flow.md)adhatnak hozzá bejelentkezést és engedélyezést a háttér-szolgáltatásokhoz.

Ebben a folyamatban az alkalmazás egy engedélyezési kódot kap a Microsoft Identity platform végponttól, amikor a felhasználó bejelentkezik. Az engedélyezési kód az alkalmazásnak a bejelentkezett felhasználó nevében történő meghívására vonatkozó engedélyét jelöli. Az alkalmazás a háttérben egy OAuth 2,0 hozzáférési jogkivonat és egy frissítési jogkivonat számára is kicserélheti az engedélyezési kódot. Az alkalmazás a hozzáférési token használatával hitelesíti a webes API-kat a HTTP-kérelmekben, és a frissítési token használatával új hozzáférési jogkivonatokat kérhet le, ha a régebbi hozzáférési tokenek lejárnak.

![Megjeleníti a natív alkalmazás hitelesítési folyamatát.](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Démonok és kiszolgálóoldali alkalmazások

A hosszan futó folyamatokkal rendelkező vagy a felhasználóval való interakció nélkül működő alkalmazások esetében is szükség van a biztonságos erőforrásokhoz, például a webes API-khoz való hozzáférésre. Ezek az alkalmazások a felhasználó delegált identitása helyett az alkalmazás identitását használva hitelesíthetők és lekérhetik a jogkivonatokat a OAuth 2,0 ügyfél-hitelesítő adatokkal. Az alkalmazás identitását az ügyfél titkos kódjával vagy tanúsítványával igazolhatja. További információ: [hitelesítés a Microsoft Identity platformon a Daemon-alkalmazásokban tanúsítványokkal](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/).

Ebben a folyamatban az alkalmazás közvetlenül a `/token`-végponttal kommunikál a hozzáférés eléréséhez:

![A démon-alkalmazás hitelesítési folyamatát mutatja](./media/v2-app-types/convergence-scenarios-daemon.svg)

Daemon-alkalmazás létrehozásához tekintse meg az [ügyfél hitelesítő adatait tartalmazó dokumentációt](v2-oauth2-client-creds-grant-flow.md), vagy próbálkozzon egy [.net-minta alkalmazással](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
