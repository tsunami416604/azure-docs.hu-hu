---
title: Az Azure Active Directory B2C és az Azure API Management használatával védheti a SPA-háttérrendszer használatát az OAuth 2.0-s rendszerrel.
description: Az Api-k védelme az OAuth 2.0-s verzióval az Azure Active Directory B2C, az Azure API Management és az Easy Auth használatával, amelyeket JavaScript SPA-ból hívhat meg.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475476"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Spa-háttérrendszer védelme az OAuth 2.0, az Azure Active Directory B2C és az Azure API Management segítségével

Ebben a forgatókönyvben bemutatja, hogyan konfigurálhatja az Azure API Management-példány egy API védelme.
Az OAuth 2.0 protokollt az Azure AD B2C-vel, valamint az API Management et fogjuk használni az EasyAuth használatával az Azure Functions-háttérrendszer biztonságossá tétele érdekében.

## <a name="aims"></a>Célja
Bemutatjuk, hogyan használható az API Management egy egyszerűsített forgatókönyvben az Azure Functions és az Azure AD B2C használatával. Létre kell hoznia egy JavaScript (JS) alkalmazást, amely egy API-t hív meg, amely az Azure AD B2C-vel bejelentkezik a felhasználókban. Ezután az API Management validate-jwt házirend-funkcióit fogja használni a háttér-API védelméhez.

A mélységi védelemhez az EasyAuth segítségével újra érvényesítjük a jogkivonatot a háttér-API-n belül.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések végrehajtásához a következőkre van szüksége:
* Egy Azure (StorageV2) általános célú V2 tárfiók az előtér-js egyoldalas alkalmazás üzemeltetéséhez
* Egy Azure API Management-példány 
* Üres Azure Function alkalmazás (a V2 .NET Core futtató, Windows-felhasználási terv) a hívott API üzemeltetéséhez
* Egy Azure AD B2C-bérlő, előfizetéshez kapcsolva 

Bár a gyakorlatban az éles számítási feladatokban ugyanabban a régióban lévő erőforrásokat használná, ehhez az útmutató cikkhez a központi telepítés régiója nem fontos.

## <a name="overview"></a>Áttekintés
Itt van egy példa a használatban lévő alkatrészek és a köztük lévő áramlás, ha ez a folyamat befejeződött.
![Használatban lévő és áramlási alkatrészek](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Használatban lévő és áramlási alkatrészek")

Íme egy gyors áttekintés a lépésekről:

1. Hozza létre az Azure AD B2C-hívást (előtér- és API-kezelés) és AZ API-alkalmazásokat hatókörökkel és grant API Access-el
1. A regisztráció vagy a bejelentkezési szabályzatok létrehozása, hogy a felhasználók bejelentkezhessenek az Azure AD B2C-vel 
1. Az API Management konfigurálása az új Azure AD B2C ügyfélazonosítókkal és kulcsokkal az OAuth2 felhasználói engedélyezés engedélyezéséhez a fejlesztői konzolon
1. A függvény API létrehozása
1. Konfigurálja a függvény API-t az EasyAuth engedélyezéséhez az új Azure AD B2C ügyfélazonosítókkal és kulcsokkal, és zárolja az APIM VIP-t 
1. Az API-definíció létrehozása az API Managementben
1. Oauth2 beállítása az API Management API konfigurációjához
1. Állítsa be a **CORS-házirendet,** és adja hozzá az **validate-jwt** házirendet az OAuth-jogkivonat érvényesítéséhez minden bejövő kérelemhez
1. A hívó alkalmazás létrehozása az API felhasználásához
1. A JS SPA minta feltöltése
1. A Minta JS-ügyfélalkalmazás konfigurálása az új Azure AD B2C ügyfélazonosítókkal és kulcsokkal 
1. Az ügyfélalkalmazás tesztelése

## <a name="configure-azure-ad-b2c"></a>Az Azure AD B2C konfigurálása 
Nyissa meg az Azure AD B2C panelt a portálon, és tegye meg a következő lépéseket.
1. Az Alkalmazások lap **kijelölése** 
1. Kattintson a "Hozzáadás" gombra, és hozzon létre három alkalmazást a következő célokra
   * Az előtér-ügyfél.
   * A háttérfüggvény-függvény API.The Backend Function API.
   * [Nem kötelező] Az API Management fejlesztői portál (kivéve, ha az Azure API Management a fogyasztási rétegben, erről a forgatókönyvről később).
1. Állítsa be a WebApp / Web API-t mind a 3 alkalmazáshoz, és állítsa az "Implicit folyamat engedélyezése" lehetőséget csak az előtér-ügyfélre.
1. Most állítsa be az App ID URI-t, válasszon valami egyedit és relevánsat a létrehozott szolgáltatáshoz.
1. Használja helyőrzők a válasz URL-eket most, mint https://localhostpéldául, frissítjük ezeket az URL-eket később.
1. Kattintson a "Létrehozás" gombra, majd ismételje meg a 2-5.
1. Nyissa meg az API Management Developer Portal alkalmazást az alkalmazások listájából, és válassza a *Kulcsok* lapot (az Általános csoportban), majd kattintson a "Kulcs létrehozása" gombra egy hitelesítési kulcs létrehozásához
1. A mentés gombra kattintva rögzítse a kulcsot valahol biztonságos későbbi használatra - vegye figyelembe, hogy ez a hely az egyetlen esélye, hogy megtekinthesse és másolja ezt a kulcsot.
1. Most válassza ki a *közzétett hatókörök* lapot (az API Access alatt)
1. Hozzon létre és nevezzen el egy hatókört a függvény API-hoz, és rögzítse a hatókört és a feltöltött teljes hatókör értékét, majd kattintson a "Mentés" gombra.
   > [!NOTE]
   > Az Azure AD B2C hatókörök hatékonyan engedélyek et az API-n belül, hogy más alkalmazások is kérhetnek hozzáférést az API-hozzáférési panelen keresztül az alkalmazások, hatékonyan csak létrehozott alkalmazás engedélyeket a hívott API-t.
1. Nyissa meg a másik két alkalmazást, majd keresse meg az *API Access* lapon.
1. Adjon nekik hozzáférést a háttér-API-hatókörhöz és a már ott lévő alapértelmezetthez ("A felhasználói profil elérése").
1. Hozzon létre nekik egy kulcsot minden kiválasztásával a *Kulcsok* fülre az "Általános" létrehozásához egy auth kulcsot, és rögzítse ezeket a kulcsokat valahol biztonságos később.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>"Regisztráció vagy bejelentkezés" felhasználói folyamat létrehozása
1. Visszatérés az Azure AD B2C blade gyökérzetéhez (vagy "Áttekintés") 
1. Ezután válassza a "Felhasználói folyamatok (házirendek)" lehetőséget, és kattintson az "Új felhasználói folyamat" gombra.
1. Válassza ki a "Regisztráció és bejelentkezés" felhasználói folyamat típusát
1. Adjon nevet a házirendnek, és rögzítse későbbre.
1. Ezután az "Identitásszolgáltatók" alatt jelölje be a "Felhasználói azonosító regisztráció" (ez az "E-mail regisztráció" parancsot) és kattintson az OK gombra. 
1. A "Felhasználói attribútumok és jogcímek" területen kattintson a "További megjelenítése..." lehetőségre. majd válassza ki a jogcím beállításait, amelyeket a felhasználók nak meg kell adniuk, és a jogkivonatban visszaküldték. Ellenőrizze legalább a "Megjelenítendő név" és az "E-mail cím" gyűjteni és vissza, majd kattintson az "OK", majd kattintson a "Create".
1. Jelölje ki a listában létrehozott házirendet, majd kattintson a "Felhasználói folyamat futtatása" gombra.
1. Ez a művelet megnyitja a futtatott felhasználói folyamat panelt, kiválasztja az előtér-alkalmazást, majd rögzíti a b2clogin.com tartomány címét, amely a "Tartomány kiválasztása" legördülő menü alatt jelenik meg.
1. Kattintson a tetején lévő hivatkozásra a "jól ismert openid konfigurációs végpont" megnyitásához, és rögzítse a authorization_endpoint és token_endpoint értékeket, valamint magát a kapcsolatot, mint a jól ismert openid konfigurációs végpontot.

   > [!NOTE]
   > B2C szabályzatok lehetővé teszik, hogy az Azure AD B2C bejelentkezési végpontok, hogy képes rögzíteni a különböző adatösszetevők és a felhasználók különböző módon való bejelentkezéshez. Ebben az esetben konfiguráltunk egy regisztrációt vagy a végpontra való bejelentkezést, amely egy jól ismert konfigurációs végpontot tett elérhetővé, különösen a létrehozott szabályzatot a p = paraméter azonosította az URL-ben.
   > 
   > Miután ez megtörtént - most már van egy funkcionális Üzleti fogyasztói identitás platform, amely aláírja a felhasználók több alkalmazás. 
   > Ha azt szeretnénk, hogy kattintson a "Run felhasználói folyamat" gombra itt (hogy menjen át a regisztráció vagy jelentkezzen be a folyamat), és kap egy megtapint, hogy mit fog tenni a gyakorlatban, de az átirányítási lépés végén sikertelen lesz, mint az alkalmazás még nem telepített.

## <a name="build-the-function-api"></a>A függvény API-jának létrehozása
1. Váltás vissza a standard Azure AD-bérlőre az Azure Portalon, hogy újra konfigurálhassuk az előfizetésében lévő elemeket 
1. Nyissa meg az Azure Portal Funkcióalkalmazások paneljét, nyissa meg az üres függvényalkalmazást, majd hozzon létre egy új In-Portal "Webhook + API" függvényt a rövid útmutatón keresztül.
1. Illessze be a mintakódot alulról a Run.csx fájlba a megjelenő meglévő kódba.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > A c# script function code you just pasted simply logs a sort a függvénynaplók, és visszaadja a szöveg "Hello World" néhány dinamikus adatok (a dátum és az idő).

3. Válassza az "Integrálás" lehetőséget a bal oldali panelen, majd az "Irányított szerkesztő" lehetőséget az ablaktábla jobb felső sarkában.
4. Illessze be a mintakódot a meglévő json fölé.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Váltson vissza a HttpTrigger1 fülre, kattintson a "Függvény URL-címének bemásolása" gombra, majd másolja a megjelenő URL-t.

   > [!NOTE]
   > Az imént létrehozott kötések egyszerűen mondja Functions válaszolni névtelen http GET kéréseket az URL-t, amit csak másolt. (https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) Most van egy skálázható kiszolgáló nélküli https API, amely képes egy nagyon egyszerű hasznos adat visszaadására.
   > Most már tesztelheti ezt az API-t egy webböngészőből a fenti URL-cím használatával, akkor is csík a ?code=secret része az URL-t, és bizonyítani, hogy az Azure Functions ad vissza 401-es hibát.

## <a name="configure-and-secure-the-function-api"></a>Az API függvény konfigurálása és biztonságossá tétele
1. A függvényalkalmazás két további területét kell konfigurálni (hitelesítési és hálózati korlátozások).
1. Először is állítsuk be a hitelesítést / engedélyezést, ezért &lt;&gt; kattintson a függvényalkalmazás nevére (a Z függvények ikonja mellett) az áttekintő oldal megjelenítéséhez.
1. Ezután válassza ki a "Platform funkciók" fület, és válassza a "Hitelesítés / engedélyezés" lehetőséget.
1. Kapcsolja be az App Service hitelesítési szolgáltatását.
1. A "Hitelesítési szolgáltatók" csoportban válassza az "Azure Active Directory" lehetőséget, és válassza a "Speciális" lehetőséget a Felügyeleti mód kapcsolóból.
1. A háttérrendszer-függvény API-jának alkalmazásazonosítójának beillesztése (az Azure AD B2C-ből az "Ügyfélazonosító" mezőbe)
1. Illessze be a jól ismert open-id konfigurációs végpontot a regisztrációból, vagy jelentkezzen be a házirendbe a Kiállító URL-címmezőjébe (ezt a konfigurációt korábban rögzítettük).
1. Kattintson az OK gombra.
1. Állítsa be a műveletet, ha a kérelem nincs hitelesítve legördülő "Bejelentkezés az Azure Active Directoryval", majd kattintson a Mentés gombra.

   > [!NOTE]
   > Most a függvény API telepítve van, és 401-es válaszokat kell dobnia, ha a megfelelő kulcs nincs megadva, és érvényes kérelem bemutatásakor adatokat kell visszaadnia.
   > További részletes védelmet adott az EasyAuth-ban a "Bejelentkezés Az Azure AD-vel" beállítás konfigurálásával a nem hitelesített kérelmek kezeléséhez. Ne feledje, hogy ez megváltoztatja a jogosulatlan kérelem viselkedését a háttér-függvény alkalmazás és a frontend SPA között, mivel az EasyAuth 302-es átirányítást ad ki az AAD-ra a 401 nem engedélyezett válasz helyett, ezt később kijavítjuk az API Management használatával.
   > Még mindig nincs IP-biztonság alkalmazva, ha van egy érvényes kulcs és OAuth2 token, bárki hívhatja ezt bárhonnan - ideális esetben azt akarjuk kényszeríteni az összes kérelmet, hogy jöjjön keresztül API Management.
   > Ha az API Management fogyasztási réteget használja, nem fogja tudni végrehajtani ezt a zárolást a VIP által, mivel nincs dedikált statikus IP az adott réteghez, akkor az API-hívások zárolásának módszerére kell támaszkodnia a megosztott titkos funkciókulcson keresztül , így a 11-13 lépés nem lehetséges.

1. A "Hitelesítés/ engedélyezés" panel bezárása 
1. Válassza a "Hálózat" lehetőséget, majd a "Hozzáférési korlátozások" lehetőséget.
1. Ezután zárja le az engedélyezett függvényIP-címeket az API Management-példány VIP.Next, lock down the allowed function app IP-k to the API Management instance VIP. Ez a VIP az API-kezelés - áttekintés a portál áttekintése szakaszában jelenik meg.
1. Ha továbbra is kapcsolatba szeretne lépni a függvényportállal, és el szeretné végezni az alábbi választható lépéseket, itt is adja hozzá saját nyilvános IP-címét vagy CIDR-tartományát.
1. Ha van egy engedélyezett bejegyzés a listában, az Azure hozzáad egy implicit megtagadási szabályt az összes többi cím blokkolásához. 

Cidr formátumú címblokkokat kell hozzáadnia az IP-korlátozások panelhez. Ha egyetlen címet kell hozzáadnia, például az API Management VIP-t, akkor az xx.xx.xx.xx formátumban kell hozzáadnia.

   > [!NOTE]
   > Mostantól a Függvény API-t nem lehet hívható bárhonnan, kivéve az API-kezelés, vagy a címét.
   
## <a name="import-the-function-app-definition"></a>A függvényalkalmazás-definíció importálása
1. Nyissa meg az *API Management panelt,* majd nyissa *meg a példányt.*
1. Válassza ki az API-k blade a példány API Management szakaszában.
1. Az "Új API hozzáadása" ablaktáblán válassza a "Függvényalkalmazás" lehetőséget, majd válassza a "Teljes" lehetőséget az előugró ablak tetejéről.
1. Kattintson a Tallózás gombra, válassza ki azt a függvényalkalmazást, amelyen belül az API-t üzemelteti, majd kattintson a Kijelölés gombra.
1. Adjon nevet és leírást az API Management belső használatához, és adja hozzá a "korlátlan" termékhez.
1. Győződjön meg arról, hogy rögzíti az alap URL-címet későbbi használatra, majd kattintson a létrehozás gombra.

## <a name="configure-oauth2-for-api-management"></a>Oauth2 konfigurálása AZ API-kezeléshez

1. Ezután válassza ki az Oauth 2.0 panelt a Biztonság lapon, és kattintson a "Hozzáadás" gombra.
1. Adja meg a *megjelenítendő név* és a *leírás értékét* a hozzáadott Oauth végponthoz (ezek az értékek a következő lépésben Oauth2 végpontként jelennek meg).
1. Bármilyen értéket megadhat az Ügyfél regisztrációs lapjának URL-címében, mivel ez az érték nem lesz használva.
1. Ellenőrizze az *Implicit Hitelesítési* támogatás típusát, és hagyja bejelölve az engedélyezési kód támogatási típusát.
1. Lépjen az *Engedélyezési* és *Token* végpontmezőkre, és adja meg a korábban a jól ismert konfigurációs XML-dokumentumból rögzített értékeket.
1. Görgessen le, és feltöltsen egy "erőforrásnak" nevezett *további törzsparamétert* az Azure AD B2C alkalmazás regisztrációjából származó háttérfüggvény-API-ügyfélazonosítóval
1. Válassza ki az "Ügyfél hitelesítő adatok", állítsa be az ügyfélazonosítót a fejlesztői konzol alkalmazás azonosítóját - hagyja ki ezt a lépést, ha a fogyasztási API Management modell.
1. Állítsa be az ügyféltitkos kulcsot a korábban rögzített kulcsra – hagyja ki ezt a lépést, ha a fogyasztási API Management modellt használja.
1. Végül most rögzítse az API Management hitelesítési kódtámogatásának redirect_uri későbbi használatra.

## <a name="set-up-oauth2-for-your-api"></a>Oauth2 beállítása az API-hoz
1. Az API a portál bal oldalán jelenik meg a "Minden API" szakaszban, és megnyithatja az API-t, kattintson rá.
1. Válassza a "Beállítások" lapot.
1. Frissítse a beállításokat a felhasználói engedélyezési választógomb "Oauth 2.0" gombjának kiválasztásával.
1. Jelölje ki a korábban definiált Oauth-kiszolgálót.
1. Jelölje be a "Hatókör felülbírálása" jelölőnégyzetet, és adja meg a háttér-API-híváshoz korábban rögzített hatókört.

   > [!NOTE]
   > Most már van egy API Management-példány, amely tudja, hogyan kaphat hozzáférési jogkivonatokat az Azure AD B2C kérelmek engedélyezéséhez, és megérti az Oauth2 Azure Active Directory B2C konfigurációját.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>A **CORS** és az **validate-jwt** házirendek beállítása

> A következő szakaszokat a használt APIM-szinttől függetlenül kell követni. 

1. Váltson vissza a tervező lapra, és válassza az "Összes API" lehetőséget, majd kattintson a kódnézet gombra a házirend-szerkesztő megjelenítéséhez.
1. Szerkesztsd a bejövő szakaszt, és illessze be az alábbi xml-t, hogy a következőképpen jelenjen meg.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Edit a openid-config URL-t, hogy megfeleljen a jól ismert Azure AD B2C végpont a regisztrációhoz, vagy jelentkezzen be a szabályzatban.
1. A jogcímértékét úgy szerkesztheti, hogy megfeleljen az érvényes alkalmazásazonosítónak, más néven a háttérrendszer API-alkalmazásának ügyfélazonosítójának, és mentse.

   > [!NOTE]
   > Most API-kezelés képes válaszolni a kereszt-eredetű kérelmek JS SPA-alkalmazások, és elvégzi a szabályozás, sebesség-korlátozás és a JWT hitelesítési token átadása előtt a kérelmet a függvény API-t.

   > [!NOTE]
   > A következő szakasz nem kötelező, és nem vonatkozik a **felhasználási** szintre, amely nem támogatja a fejlesztői portált.
   > Ha nem kívánja használni a fejlesztői portált, vagy nem tudja használni, mivel a Felhasználás iszint, kérjük, hagyja ki ezt a lépést, és ugorjon egyenesen [a "Build the JavaScript SPA az API felhasználásához"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>[Nem kötelező] A fejlesztői portál konfigurálása

1. Nyissa meg az Azure AD B2C panelt, és keresse meg a fejlesztői portál alkalmazásregisztrációját
1. Állítsa be a "Válasz URL" bejegyzést arra, amelyet akkor írt le, amikor korábban konfigurálta az API-kezelés ben az auth kód támogatás redirect_uri.

   Most, hogy az OAuth 2.0 `Echo API`felhasználói engedélyezve van a, a developer console beszerez egy hozzáférési jogkivonatot a felhasználó számára, mielőtt meghívja az API-t.

1. Tallózzon a `Echo API` fejlesztői portál on található bármely műveletközött, és válassza a **Próbálja ki lehetőséget** a fejlesztői konzolhoz való elhozáshoz.
1. Jegyezz fel egy új elemet az **Engedélyezés** szakaszban, amely az imént hozzáadott engedélyezési kiszolgálónak felel meg.
1. Válassza ki az **engedélyezési kódot** az engedélyezési legördülő listából, és a rendszer kéri, hogy jelentkezzen be az Azure AD-bérlőbe. Ha már be van jelentkezve a fiókkal, előfordulhat, hogy a rendszer nem kéri.
1. Sikeres bejelentkezés után egy `Authorization: Bearer` fejléc et ad hozzá a kérelemhez, az Azure AD B2C-ből származó hozzáférési jogkivonattal a Base64-ben. 
1. Válassza **a Küldés lehetőséget,** és az API-t sikeresen meghívhatja.

   > [!NOTE]
   > Most az API-kezelés képes megszerezni a jogkivonatokat a fejlesztői portálon az API teszteléséhez, és képes megérteni a definícióját, és a megfelelő tesztoldalt a fejlesztői portálon.

1. Az API Management portál áttekintő paneljén kattintson a "Fejlesztői portál" elemre az API rendszergazdájaként való bejelentkezéshez.
1. Itt Ön és az API más kiválasztott fogyasztói tesztelhetik és hívhatják őket egy konzolról.
1. Válassza a "Termékek", majd a "Korlátlan" lehetőséget, majd válassza ki a korábban létrehozott API-t, és kattintson a "TRY IT" gombra.
1. Fedje fel az API-előfizetési kulcsot, és másolja biztonságos helyre a kérelem URL-címét, amelykésőbb szüksége lesz rá.
1. Is válassza implicit, az oauth auth legördülő menüből, és lehet, hogy hitelesíteni itt egy felugró ablak.
1. Kattintson a "Küldés" gombra, és ha minden rendben van, a Függvényalkalmazásnak egy hello üzenettel kell válaszolnia az API-kezelésen keresztül egy 200 OK üzenettel és néhány JSON-nal.

   > [!NOTE]
   > Gratulálunk, most már az Azure AD B2C, AZ API Management és az Azure Functions együttműködve közzétenni, biztonságos és api-t fogyaszt. Előfordulhat, hogy észrevette, hogy az API valójában kétszer is biztonságos ezzel a módszerrel, egyszer az API Management Ocp-Subscription-Key fejléc, és egyszer az engedélyező JWT.
   > Helyes lenne, mivel ez a példa egy JavaScript egyoldalas alkalmazás, az API Management Key csak a sebesség-korlátozó és számlázási hívások.
   > A tényleges engedélyezési és hitelesítési kezeli az Azure AD B2C, és beágyazódik a JWT, amely kétszer érvényesíti, egyszer az API Management, majd az Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>A JavaScript SPA létrehozása az API felhasználásához
1. A tárfiókok panel megnyitása az Azure Portalon 
1. Válassza ki a létrehozott fiókot, és válassza ki a "Statikus webhely" panelt a Beállítások szakaszban (ha nem látja a "Statikus webhely" lehetőséget, ellenőrizze, hogy létrehozott-e V2-fiókot).
1. Állítsa a statikus webhosting funkciót "engedélyezve",, és állítsa az indexdokumentum nevét "index.html"-re, majd kattintson a "mentés" gombra.
1. Jegyezze fel az elsődleges végpont tartalmát, mivel ez a hely az a hely, ahol az előtér-hely lesz tárolva. 

   > [!NOTE]
   > Használhatja az Azure Blob Storage + CDN átírását vagy az Azure App Service szolgáltatást - de a Blob Storage statikus webhelyüzemeltetési funkciója alapértelmezett tárolót biztosít számunkra a statikus webes tartalom / html / js / css kiszolgálására az Azure Storage-ból, és egy alapértelmezett oldalt következtet ki számunkra a nulla munkához.

## <a name="upload-the-js-spa-sample"></a>A JS SPA-minta feltöltése
1. Továbbra is a tárfiók panelen válassza ki a "Blobs" panela a Blob Service szakaszban, és kattintson a $web a tároló, amely megjelenik a jobb oldali ablaktáblában.
1. Mentse az alábbi kódot a számítógépen lévő helyi fájlba index.html néven, majd töltse fel az index.html fájlt a $web tárolóba.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Tallózással keresse meg az előző szakaszban korábban tárolt statikus webhelyelsődleges végpontot.

   > [!NOTE]
   > Gratulálunk, most telepített egy JavaScript egyoldalas alkalmazást az Azure Storage-ba Mivel még nem konfiguráltuk a JS-alkalmazást az API kulcsaival, vagy a JS-alkalmazást az Azure AD B2C részleteivel konfiguráltuk – az oldal még nem fog működni, ha megnyitja.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>A JS SPA konfigurálása az Azure AD B2C-hez
1. Most már tudjuk, hol van minden: beállíthatjuk a SPA a megfelelő API Management API-címet és a megfelelő Azure AD B2C alkalmazás / ügyfélazonosítók
1. Lépjen vissza az Azure Portal storage panelre, és kattintson az index.html elemre, majd válassza a "Blob szerkesztése" lehetőséget. 
1. Frissítse az auth részleteket, hogy megfeleljen a B2C korábban regisztrált előtér-alkalmazásnak, megjegyezve, hogy a "b2cScopes" értékek az API-háttérrendszerhez tartoznak.
1. A webApi-kulcs és az API URL-címe megtalálható az API-kezelés tesztablakában az API-művelethez.
1. Hozzon létre egy APIM-előfizetési kulcsot úgy, hogy visszamegy az API Management panelre, kiválasztja az "Előfizetések" lehetőséget, majd az "Előfizetés hozzáadása" gombra kattint, majd menti a rekordot. A létrehozott sor melletti Ellipszis (...) elemre kattintva megjelenítheti a kulcsokat, így másolhatja az elsődleges kulcsot.
1. Meg kell kinéznie valamiilyesmiaz alábbi kódot: -  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Kattintson a Save (Mentés) gombra.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Az Átirányítási URI-k beállítása az Azure AD B2C előtér-alkalmazáshoz
1. Nyissa meg az Azure AD B2C panelt, és keresse meg a JavaScript előtér-alkalmazás alkalmazásregisztrációját
1. Állítsa be az átirányítási URL-címet arra, amelyet akkor írt le, amikor korábban beállította a statikus webhely elsődleges végpontját.

   > [!NOTE] 
   > Ez a konfiguráció azt eredményezi, hogy az előtér-alkalmazás egy ügyfél egy hozzáférési jogkivonatot kap az Azure AD B2C megfelelő jogcímekkel.
   > Az SPA képes lesz hozzáadni ezt a tulajdonosi jogkivonatot a https fejlécben a háttér-API-t.
   > Az API Management előzetesen ellenőrzi a token, a sebesség-limit hívásokat a végponthoz az előfizetői kulcs által, mielőtt átadná a kérelmet a fogadó Azure-függvény API-nak.
   > A SPA teszi a választ a böngészőben.

   > *Gratulálunk, az Azure AD B2C, az Azure API Management, az Azure Functions, az Azure App Service authorization konfigurálta, hogy tökéletes harmóniában működjenek!*

   > [!NOTE]
   > Most már van egy egyszerű alkalmazás unk egy egyszerű biztonságos API-val, teszteljük.

## <a name="test-the-client-application"></a>Az ügyfélalkalmazás tesztelése
1. A korábban létrehozott tárfiókból feljegyzett mintaalkalmazás URL-címének megnyitása
1. Kattintson a "Bejelentkezés" gombra a jobb felső sarokban, ez a kattintás megjelenik az Azure AD B2C regisztráció vagy bejelentkezési profil.
1. Post Jelentkezzen be a "Bejelentkezett mint" részben a képernyő lesz feltöltve a JWT.
1. Most kattintson a "Call Web Api", és a lap frissítenie kell az értékeket küldött vissza a biztonságos API-t.

## <a name="and-were-done"></a>És végeztünk.
A fenti lépések módosíthatók és szerkeszthetők, hogy az Azure AD B2C számos különböző felhasználási szolgáltatását lehetővé tegyék az API Management használatával.

## <a name="next-steps"></a>További lépések
* További információ az [Azure Active Directoryról és az OAuth2.0-ról.](../active-directory/develop/authentication-scenarios.md)
* Tekintse n le az API Managementről szóló további [videókat.](https://azure.microsoft.com/documentation/videos/index/?services=api-management)
* A háttérszolgáltatás védelmének egyéb módjairól a [Kölcsönös tanúsítvány hitelesítése](api-management-howto-mutual-certificates.md)című témakörben van.
* [Hozzon létre egy API Management szolgáltatáspéldányt.](get-started-create-service-instance.md)
* [Az első API kezelése.](import-and-publish.md)
