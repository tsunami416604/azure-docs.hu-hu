---
title: Hitelesítés és engedélyezés
description: Ismerje meg az Azure App Service beépített hitelesítési és engedélyezési támogatását, valamint azt, hogy miként segíthet az alkalmazás jogosulatlan hozzáféréselleni védelmében.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 825d113bbe081ba6fb85da19ff6449824db92d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475391"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben

> [!NOTE]
> Jelenleg az AAD V2 (beleértve az MSAL-t is) nem támogatott az Azure App Services és az Azure Functions. Kérjük, látogasson vissza a frissítéseket.
>

Az Azure App Service beépített hitelesítési és engedélyezési támogatást nyújt, így bejelentkezhet a felhasználókba, és hozzáférhet az adatokhoz, ha minimális kódot ír a webalkalmazásában, a RESTful API-ban és a mobil háttérrendszerben, valamint az [Azure Functionsben.](../azure-functions/functions-overview.md) Ez a cikk azt ismerteti, hogy az App Service hogyan egyszerűsíti az alkalmazás hitelesítését és engedélyezését.

A biztonságos hitelesítés és engedélyezés a biztonság alapos megértését igényli, beleértve az összevonást, a titkosítást, a [JSON-webtokenek (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) kezelését, [a támogatási típusokat](https://oauth.net/2/grant-types/)és így tovább. Az App Service biztosítja ezeket a segédprogramokat, így több időt és energiát fordíthat arra, hogy üzleti értéket biztosítson az ügyfélnek.

> [!IMPORTANT]
> Nem kell használnia az App Service-t az AuthN/AuthO szolgáltatáshoz. Használhatja a mellékelt biztonsági funkciókat a választott webes keretrendszerben, vagy írhat saját segédprogramokat. Ne feledje azonban, hogy a Chrome 80 a Cookie-k (2020 március a 2020. március környékén) [megvalósítása során megszakítja a samesite végrehajtását,](https://www.chromestatus.com/feature/5088147346030592) és az egyéni távoli hitelesítés vagy más, a webhelyek közötti cookie-közzétételre támaszkodó forgatókönyvek megszakadhatnak az ügyfél Chrome-böngészők frissítésekor. A megoldás összetett, mert támogatnia kell a különböző SameSite viselkedést a különböző böngészőkhöz. 
>
> Az App Service által üzemeltetett ASP.NET Core 2.1 és újabb verziók már javításra kerülnek ehhez a törésváltozáshoz, és megfelelően kezelik a Chrome 80 és a régebbi böngészőket. Emellett a ASP.NET Framework 4.7.2-es összes javítása 2020 januárjában az App Service-példányokon is telepítve van. További információért, például arról, hogy az alkalmazás megkapta-e a javítást, olvassa el az [Azure App Service SameSite cookie-frissítését.](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)
>

A natív mobilalkalmazásokkal kapcsolatos információkért lásd: [Felhasználói hitelesítés és engedélyezés a mobilalkalmazásokhoz az Azure App Service szolgáltatással.](../app-service-mobile/app-service-mobile-auth.md)

## <a name="how-it-works"></a>Működés

A hitelesítési és engedélyezési modul ugyanabban a sandboxban fut, mint az alkalmazáskód. Ha engedélyezve van, minden bejövő HTTP-kérelem áthalad rajta, mielőtt az alkalmazáskód kezeli.

![](media/app-service-authentication-overview/architecture.png)

Ez a modul több dolgot is kezel az alkalmazáshoz:

- A felhasználók hitelesítése a megadott szolgáltatóval
- Jogkivonatok ellenőrzése, tárolói és frissítése
- A hitelesített munkamenet kezelése
- Identitásadatok befecskendezése a kérelemfejlécekbe

A modul az alkalmazáskódtól elkülönítve fut, és az alkalmazásbeállítások használatával van konfigurálva. Nincs szükség SDK-kra, meghatározott nyelvekre vagy az alkalmazáskód módosítására. 

### <a name="user-claims"></a>Felhasználói jogcímek

Az Összes nyelvi keretrendszerek, App Service elérhetővé teszi a felhasználó jogcímeket a kód a kérelem fejlécek befecskendezése. A ASP.NET 4.6-os alkalmazások esetében az App Service feltölti a [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) alkalmazást a hitelesített felhasználó jogcímekkel, így követheti a szabványos .NET-kódmintát, beleértve az `[Authorize]` attribútumot is. Hasonlóképpen, a PHP alkalmazások, App Service `_SERVER['REMOTE_USER']` feltölti a változó. Java-alkalmazások esetén a jogcímek [a Tomcat servletből érhetők el.](containers/configure-language-java.md#authenticate-users-easy-auth)

Az [Azure](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` Functions esetében a .NET-kód nem hidratált, de továbbra is megtalálhatja a felhasználói jogcímeket a kérelemfejlécekben.

További információt a Felhasználói jogcímek elérése című [témakörben talál.](app-service-authentication-how-to.md#access-user-claims)

### <a name="token-store"></a>Jogkivonat-tároló

Az App Service egy beépített jogkivonat-tárolót biztosít, amely a webalkalmazások, API-k vagy natív mobilalkalmazások felhasználóihoz társított jogkivonatok tárháza. Ha engedélyezi a hitelesítést bármely szolgáltatóval, ez a jogkivonat-tároló azonnal elérhető vé válik az alkalmazás számára. Ha az alkalmazáskódnak hozzá kell férnie ezekhez a szolgáltatókhoz a felhasználó nevében, például: 

- bejegyzés a hitelesített felhasználó Facebook-idővonalán
- a felhasználó vállalati adatainak olvasása a Microsoft Graph API használatával

Általában meg kell írnia a kódot gyűjteni, tárolni és frissíteni ezeket a jogkivonatokat az alkalmazásban. A jogkivonat-tároló, csak [letölteni a jogkivonatokat,](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) amikor szüksége van rájuk, és [mondja app service frissíteni őket,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) ha érvénytelenné válnak. 

Az id-jogkivonatok, a hozzáférési jogkivonatok és a frissítési jogkivonatok a hitelesített munkamenet gyorsítótárazott, és azok csak a társított felhasználó által érhető el.  

Ha nem kell dolgozni a jogkivonatokat az alkalmazásban, letilthatja a jogkivonat-tároló.

### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha [engedélyezi az alkalmazásnaplózást,](troubleshoot-diagnostic-logs.md)a hitelesítési és engedélyezési nyomkövetések közvetlenül a naplófájlokban jelennek meg. Ha olyan hitelesítési hibát lát, amelyet nem várt, kényelmesen megtalálhatja az összes részletet a meglévő alkalmazásnaplók megtekintésével. Ha engedélyezi [a sikertelen kérelmek nyomkövetését,](troubleshoot-diagnostic-logs.md)pontosan láthatja, hogy a hitelesítési és engedélyezési modul milyen szerepet játszhatott egy sikertelen kérelemben. A nyomkövetési naplókban keressen hivatkozásokat egy `EasyAuthModule_32/64`module nevű modulra. 

## <a name="identity-providers"></a>Identitásszolgáltatók

Az App Service [összevont identitást](https://en.wikipedia.org/wiki/Federated_identity)használ, amelyben egy külső identitásszolgáltató kezeli a felhasználói identitásokat és a hitelesítési folyamatot. Alapértelmezés szerint öt identitásszolgáltató érhető el: 

| Szolgáltató | Bejelentkezési végpont |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-fiók](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Ha engedélyezi a hitelesítést és az engedélyezést az egyik ilyen szolgáltatóval, a bejelentkezési végpont jaad a felhasználói hitelesítéshez és a szolgáltatótól származó hitelesítési jogkivonatok érvényesítéséhez. A felhasználók számára könnyedén biztosíthatja a bejelentkezési lehetőségek et. Egy másik identitásszolgáltatót vagy [saját egyéni identitáskezelési megoldást][custom-auth]is integrálhat.

## <a name="authentication-flow"></a>Hitelesítési folyamat

A hitelesítési folyamat minden szolgáltató esetében azonos, de attól függően változik, hogy be szeretne-e jelentkezni a szolgáltató SDK-jával:

- Szolgáltató nélkül SDK: Az alkalmazás delegálja az összevont bejelentkezés az App Service-be. Ez általában a böngészőalkalmazások esetében van így, amelyek a szolgáltató bejelentkezési oldalát a felhasználó nak mutathatják be. A kiszolgálókód kezeli a bejelentkezési folyamatot, ezért _kiszolgáló által irányított folyamatnak_ vagy _kiszolgálófolyamatnak is nevezik._ Ez az eset a böngészőalkalmazásokra vonatkozik. Ez vonatkozik a natív alkalmazások, amelyek bejelentkeznek a felhasználók a Mobilalkalmazások ügyfél SDK, mert az SDK megnyit egy webes nézetet, hogy jelentkezzen be a felhasználók az App Service-hitelesítéssel. 
- A szolgáltató SDK: Az alkalmazás bejelentkezik a felhasználók a szolgáltató manuálisan, majd elküldi a hitelesítési jogkivonatot az App Service érvényesítésre. Ez általában a böngésző nélküli alkalmazások esetében van így, amelyek nem mutathatják be a szolgáltató bejelentkezési lapját a felhasználónak. Az alkalmazáskód kezeli a bejelentkezési folyamatot, ezért _ügyféláltal irányított folyamatnak_ vagy _ügyfélfolyamatnak is nevezik._ Ez az eset a REST API-kra, [az Azure Functionsre](../azure-functions/functions-overview.md)és a JavaScript böngészőügyfelekre, valamint azokra a böngészőalkalmazásokra vonatkozik, amelyek nagyobb rugalmasságot igényelnek a bejelentkezési folyamatsorán. Ez vonatkozik a natív mobilalkalmazások, amelyek a felhasználók a szolgáltató SDK használatával.

> [!NOTE]
> Az App Service-ben egy megbízható böngészőalkalmazásból érkező hívások egy másik REST API-t hívnak meg az App Service-ben vagy az [Azure Functionsben](../azure-functions/functions-overview.md) a kiszolgáló által irányított folyamat használatával. További információt a [Hitelesítés és engedélyezés testreszabása az App Service szolgáltatásban című témakörben talál.](app-service-authentication-how-to.md)
>

Az alábbi táblázat a hitelesítési folyamat lépéseit mutatja be.

| Lépés | Szolgáltató nélkül SDK | Szolgáltatóval SDK |
| - | - | - |
| 1. Bejelentkezés a felhasználó | Átirányítja az `/.auth/login/<provider>`ügyfelet a rendszerbe. | Az ügyfélkód közvetlenül a szolgáltató SDK-jával jelentkezik be a felhasználóhoz, és kap egy hitelesítési jogkivonatot. További információt a szolgáltató dokumentációjában talál. |
| 2. Hitelesítés utáni | A szolgáltató átirányítja az ügyfelet a rendszerbe. `/.auth/login/<provider>/callback` | Az ügyfélkód [jogkivonatot ad](app-service-authentication-how-to.md#validate-tokens-from-providers) a szolgáltatótól az `/.auth/login/<provider>` érvényesítéshez. |
| 3. Hitelesített munkamenet létrehozása | Az App Service hitelesített cookie-t ad hozzá a válaszhoz. | Az App Service saját hitelesítési jogkivonatot ad vissza az ügyfélkódnak. |
| 4. Hitelesített tartalom kiszolgálása | Az Ügyfél a hitelesítési cookie-t is tartalmazza a későbbi kérelmekben (a böngésző automatikusan kezeli). | Az ügyfélkód hitelesítési `X-ZUMO-AUTH` jogkivonatot jelenít meg a fejlécben (amelyet automatikusan kezelnek a Mobile Apps ügyfél SDK-k). |

Az ügyfélböngészők esetében az App Service automatikusan `/.auth/login/<provider>`az összes nem hitelesített felhasználót a rendszerbe irányítja. A felhasználók nak egy vagy `/.auth/login/<provider>` több hivatkozást is megmutathat, hogy a szolgáltatójuk segítségével jelentkezzenek be az alkalmazásba.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Engedélyezési viselkedés

Az [Azure Portalon](https://portal.azure.com)konfigurálhatja az App Service-engedélyezés számos viselkedést, ha a bejövő kérelem nincs hitelesítve.

![](media/app-service-authentication-overview/authorization-flow.png)

A következő címsorok ismertetik a beállításokat.

### <a name="allow-anonymous-requests-no-action"></a>Névtelen kérelmek engedélyezése (nincs művelet)

Ez a beállítás elhalasztja a nem hitelesített forgalom engedélyezését az alkalmazáskódra. Hitelesített kérelmek esetén az App Service a HTTP-fejlécekben is továbbítja a hitelesítési adatokat. 

Ez a beállítás nagyobb rugalmasságot biztosít a névtelen kérések kezelésében. Például lehetővé teszi, hogy [több bejelentkezési szolgáltatót jelenítsen](app-service-authentication-how-to.md#use-multiple-sign-in-providers) meg a felhasználóknak. Azonban meg kell írni a kódot. 

### <a name="allow-only-authenticated-requests"></a>Csak hitelesített kérelmek engedélyezése

A beállítás a **Bejelentkezés a \<szolgáltatóval>. ** Az App Service átirányítja `/.auth/login/<provider>` az összes névtelen kérést a kiválasztott szolgáltatóhoz. Ha a névtelen kérelem natív mobilalkalmazásból érkezik, a visszaadott válasz egy `HTTP 401 Unauthorized`.

Ezzel a beállítással nem kell hitelesítési kódot írnia az alkalmazásban. A finomabb engedélyezés, például a szerepkör-specifikus engedélyezés a felhasználó jogcímének vizsgálatával kezelhető [(lásd: Felhasználói jogcímek elérése).](app-service-authentication-how-to.md#access-user-claims)

> [!CAUTION]
> A hozzáférés ily módon történő korlátozása az alkalmazás minden hívására vonatkozik, ami nem feltétlenül kívánatos a nyilvánosan elérhető kezdőlapot igénylő alkalmazások esetében, mint sok egyoldalas alkalmazásban.

## <a name="more-resources"></a>További erőforrások

[Oktatóanyag: A felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service (Windows) szolgáltatásban](app-service-web-tutorial-auth-aad.md)  
[Oktatóanyag: A felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service for Linux-ban](containers/tutorial-auth-aad.md)  
[Hitelesítés és engedélyezés testreszabása az App Service-ben](app-service-authentication-how-to.md)

Szolgáltatóspecifikus útmutatók:

* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára][AAD]
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára][Facebook]
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára][Google]
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára][MSA]
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára][Twitter]
* [Útmutató: Egyéni hitelesítés használata az alkalmazáshoz][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
