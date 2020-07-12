---
title: Azure Active Directory B2C és az Azure API Management használatával biztosíthatja a SPA-háttér OAuth 2,0-es védelemmel való ellátását.
description: Az API-k a OAuth 2,0-mel való ellátása a JavaScript SPA-ból hívható Azure Active Directory B2C, Azure API Management és Easy Auth használatával.
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
ms.openlocfilehash: 60177dd00dc6326aae4cfdc0b658c85f2635f8c0
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86253694"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Az OAuth 2,0, a Azure Active Directory B2C és az Azure API Management által védett SPA-háttérrendszer

Ez a forgatókönyv bemutatja, hogyan konfigurálhatja az Azure API Management-példányát az API-k elleni védelemhez.
A OAuth 2,0 protokollt a Azure AD B2Ctel együtt fogjuk használni, a API Management mellett pedig az EasyAuth-t használó Azure Functions-háttér védelmét.

## <a name="aims"></a>Célja
Látni fogjuk, hogyan használhatók a API Management egy egyszerűsített forgatókönyvben Azure Functions és Azure AD B2C. Létre fog hozni egy API-t meghívó JavaScript-(JS-) alkalmazást, amely a felhasználók Azure AD B2C használatával jelentkezik be. Ezt követően API Management érvényesítő JWT házirend-funkcióit fogja használni a háttérrendszer API-ja elleni védelemhez.

A védelem részletesen a EasyAuth használatával érvényesíti a tokent a háttérbeli API-ban.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:
* Egy Azure (StorageV2) általános célú v2 Storage-fiók a frontend JS Single Page alkalmazás üzemeltetéséhez
* Azure API Management-példány 
* Egy üres Azure Function-alkalmazás (a v2 .NET Core Runtime futtatása Windows-alapú használati csomagon) a nevezett API üzemeltetéséhez
* Egy előfizetéshez csatolt Azure AD B2C bérlő 

Annak ellenére, hogy a gyakorlatban a termelési számítási feladatokban ugyanabban a régióban használja az erőforrásokat, ebben az útmutatóban az üzembe helyezés régiója nem fontos.

## <a name="overview"></a>Áttekintés
Itt látható a használatban lévő összetevők, valamint a folyamat befejezése után zajló folyamatok.
![Használatban és folyamatban található összetevők](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Használatban és folyamatban található összetevők")

Íme egy gyors áttekintés a következő lépésekről:

1. A Azure AD B2C hívó (frontend, API Management) és API-alkalmazások létrehozása hatókörökkel és API-hozzáférés biztosítása
1. Hozza létre a regisztrációs vagy bejelentkezési szabályzatokat, hogy a felhasználók bejelentkezhetnek Azure AD B2C 
1. API Management konfigurálása az új Azure AD B2C ügyfél-azonosítókkal és kulcsokkal a OAuth2 felhasználói hitelesítésének engedélyezéséhez a Fejlesztői konzolon
1. A Function API létrehozása
1. Konfigurálja a Function API-t úgy, hogy engedélyezze a EasyAuth az új Azure AD B2C ügyfél-AZONOSÍTÓval és kulccsal, és zárolja a APIM VIP-t 
1. Az API-definíció létrehozása a API Managementban
1. Oauth2 beállítása a API Management API-konfigurációhoz
1. Állítsa be a **CORS** házirendet, és adja hozzá a **validate-JWT** szabályzatot a OAuth-jogkivonat ellenőrzéséhez minden bejövő kérelem esetében
1. A hívó alkalmazás felépítése az API használatára
1. A JS SPA-minta feltöltése
1. A minta JS-ügyfélalkalmazás konfigurálása az új Azure AD B2C ügyfél-azonosító és-kulcsok használatával 
1. Az ügyfélalkalmazás tesztelése

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C konfigurálása 
Nyissa meg a Azure AD B2C panelt a portálon, és hajtsa végre a következő lépéseket.
1. Az **alkalmazások** lap kiválasztása 
1. Kattintson a Hozzáadás gombra, és hozzon létre három alkalmazást az alábbi célokból
   * A frontend-ügyfél.
   * A háttérbeli függvény API-ját.
   * Választható A API Management fejlesztői portálon (kivéve, ha az Azure API Managementt a felhasználási szinten futtatja, további információ a forgatókönyvről később).
1. Állítsa be a WebApp/web API-t mind a 3 alkalmazáshoz, és állítsa az "implicit flow engedélyezése" beállítást Igen értékre csak a előtér-ügyfél számára.
1. Most állítsa be az alkalmazás-azonosító URI-JÁT, válassza ki a létrehozni kívánt szolgáltatás egyedi és releváns elemét.
1. A válasz URL-címeihez helyőrzőket használhat, például az https://localhost URL-eket később frissíti.
1. Kattintson a Létrehozás elemre, majd ismételje meg a 2-5. lépést a fenti három alkalmazásnál, rögzítse a AppID URI-JÁT, a nevet és az alkalmazás AZONOSÍTÓját, hogy az a későbbiekben mindhárom alkalmazást használja.
1. Nyissa meg a API Management fejlesztői portál alkalmazást az alkalmazások listájából, és válassza a *kulcsok* fület (általános), majd kattintson a kulcs generálása elemre egy hitelesítési kulcs létrehozásához.
1. A Save (Mentés) gombra való kattintás után rögzítse a kulcsot valahol a későbbi használat érdekében – vegye figyelembe, hogy ez a hely az egyetlen lehetőség a kulcs megtekintésére és másolására.
1. Most válassza ki a *közzétett hatókörök* lapot (az API-hozzáférés területen)
1. Hozzon létre és nevezzen el egy hatókört a Function API számára, és jegyezze fel a hatókört és a feltöltött teljes hatókört, majd kattintson a Save (Mentés) gombra.
   > [!NOTE]
   > Azure AD B2C hatókörök hatékony engedélyek az API-n belül, hogy más alkalmazások is igényelhetnek hozzáférést az API-hozzáférés panelről az alkalmazásokból, gyakorlatilag csak a nevezett API-hoz létrehozott alkalmazás-engedélyeket.
1. Nyissa meg a másik két alkalmazást, és tekintse meg az *API-hozzáférés* lapot.
1. Hozzáférés biztosítása a háttérrendszer API-hatóköréhez, és az alapértelmezett érték, amely már létezik ("hozzáférés a felhasználó profiljához").
1. Létrehozhatja a kulcsokat úgy, hogy kijelöli a *kulcsok* fület az "általános" alatt egy hitelesítési kulcs létrehozásához, és rögzíti ezeket a kulcsokat a későbbiek során.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>"Regisztráció vagy bejelentkezés" felhasználói folyamat létrehozása
1. Visszatérés a Azure AD B2C panel gyökerébe (vagy az áttekintő oldalra) 
1. Ezután válassza a "Felhasználókövetés (szabályzatok)" lehetőséget, majd kattintson az "új felhasználói folyamat" elemre.
1. Válassza a "regisztráció és bejelentkezés" felhasználói folyamat típusát.
1. Adja meg a szabályzat nevét, és jegyezze fel később.
1. Ezután a "személyazonosság-szolgáltatók" területen jelölje be a "felhasználói azonosító regisztrálása" lehetőséget (ez az "e-mail-regisztráció" lehet), majd kattintson az OK gombra. 
1. Kattintson a "felhasználói attribútumok és jogcímek" lehetőségre, majd a "továbbiak..." elemre. Ezután válassza ki azokat a jogcím-beállításokat, amelyeket a felhasználók megadhatnak, és amelyeket visszaadottak a jogkivonatban. A gyűjtéshez és a visszatéréshez jelölje be a "megjelenítendő név" és az "e-mail-cím" elemet, majd kattintson az OK gombra, majd a Létrehozás gombra.
1. Válassza ki a listában létrehozott szabályzatot, majd kattintson a "felhasználói folyamat futtatása" gombra.
1. Ez a művelet megnyitja a felhasználói folyamat futtatása panelt, jelölje ki a előtér-alkalmazást, majd jegyezze fel a "Select domain" legördülő listájában látható b2clogin.com-tartomány címeit.
1. A felül található hivatkozásra kattintva nyissa meg a "jól ismert OpenID konfigurációs végpontot", és jegyezze fel a authorization_endpoint és a token_endpoint értékeket, valamint a hivatkozás értékét, mint a jól ismert OpenID konfigurációs végpontot.

   > [!NOTE]
   > A B2C-szabályzatok lehetővé teszik a Azure AD B2C bejelentkezési végpontok elérhetővé tételét, így különböző módon rögzíthetik a különböző adatösszetevőket és jelentkezhetnek be a felhasználókba. Ebben az esetben olyan regisztrációs vagy bejelentkezési végpontot konfiguráltunk, amely egy jól ismert konfigurációs végpontot tesz elérhetővé, és az URL-cím a p = paraméter alapján azonosította a létrehozott házirendet.
   > 
   > Ha ez megtörtént, most már rendelkezik egy működőképes üzleti felhasználói identitás platformmal, amely több alkalmazásba is aláírja a felhasználókat. 
   > Ha azt szeretné, hogy a "felhasználói folyamat futtatása" gombra kattinthat (a regisztráció vagy a bejelentkezés folyamatán keresztül), és megtudhatja, hogy mit fog tenni a gyakorlatban, de az átirányítási lépés a végén sikertelen lesz, mivel az alkalmazás még nincs telepítve.

## <a name="build-the-function-api"></a>A Function API létrehozása
1. Váltson vissza a standard Azure AD-bérlőre a Azure Portal, hogy az előfizetésben lévő elemeket újra be lehessen állítani 
1. Lépjen a Azure Portal Function apps paneljére, nyissa meg az üres Function alkalmazást, majd hozzon létre egy új, a portálon elérhető webhook + API funkciót a gyors útmutatóban.
1. Illessze be az alábbi kódrészletet a Run. CSX fölé a megjelenő meglévő kód fölé.

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
   > Az imént beillesztett c# parancsfájl-függvény kódja egyszerűen naplóz egy sort a függvények naplóiba, és a ""Helló világ!"alkalmazás" szöveget adja vissza néhány dinamikus adattal (a dátummal és idővel).

3. Válassza az "integrálás" lehetőséget a bal oldali panelen, majd válassza a "Speciális szerkesztő" lehetőséget a panel jobb felső sarkában.
4. Illessze be az alábbi mintakód-kódot a meglévő JSON fölé.

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

5. Váltson vissza a HttpTrigger1 lapra, kattintson a "függvény URL-címének beolvasása" elemre, majd másolja ki a megjelenő URL-címet.

   > [!NOTE]
   > Az imént létrehozott kötések egyszerűen csak azt mondják, hogy a függvények válaszolnak a névtelen http GET kérelmekre az imént másolt URL-címre. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Most már van egy méretezhető, kiszolgáló nélküli HTTPS API-je, amely képes egy nagyon egyszerű hasznos adat visszaküldésére.
   > Most már tesztelheti ezt az API-t egy webböngészőből a fenti URL-cím használatával, és a? Code = Secret részét is megadhatja az URL-címben, és igazolhatja, hogy a Azure Functions 401 hibát ad vissza.

## <a name="configure-and-secure-the-function-api"></a>A Function API konfigurálása és biztonságossá tétele
1. A Function alkalmazás két további területét kell konfigurálni (hitelesítési és hálózati korlátozásokat).
1. Először konfigurálja a hitelesítést/engedélyezést, ezért kattintson a Function alkalmazás nevére (a &lt; Z &gt; functions ikon mellett) az Áttekintés oldal megjelenítéséhez.
1. Ezután válassza a "platform szolgáltatások" fület, és válassza a "hitelesítés/engedélyezés" lehetőséget.
1. Kapcsolja be a App Service hitelesítési funkciót.
1. A "hitelesítő szolgáltatók" területen válassza a "Azure Active Directory" lehetőséget, és válassza a "speciális" lehetőséget a felügyeleti mód kapcsolóból.
1. Illessze be a háttérbeli függvény API alkalmazás-AZONOSÍTÓját (a Azure AD B2Cból az "ügyfél-azonosító" mezőbe)
1. Illessze be a jól ismert Open-ID konfigurációs végpontot a regisztrációs vagy bejelentkezési szabályzatba a kiállító URL-címe mezőbe (ezt a konfigurációt korábban rögzítették).
1. Kattintson az OK gombra.
1. Állítsa be az elvégzendő műveletet, ha a kérés nincs hitelesítve legördülő menüben a "Bejelentkezés Azure Active Directoryval" lehetőségre, majd kattintson a Mentés gombra.

   > [!NOTE]
   > Most a Function API üzembe lett helyezve, és ha a megfelelő kulcs nincs megadva, akkor a 401-es válaszokat kell kidobnia.
   > A nem hitelesített kérelmek kezeléséhez a "Bejelentkezés az Azure AD szolgáltatással" beállítással további védelmi részletes biztonsági EasyAuth bővült. Vegye figyelembe, hogy ez megváltoztatja a háttérbeli függvényalkalmazás és a frontend SPA közötti jogosulatlan kérelmek viselkedését, mivel a EasyAuth a 401-re nem jogosult válasz helyett 302-es átirányítást ad ki, ezért API Management később kell kijavítani.
   > Még mindig nem alkalmazunk IP-biztonságot, ha rendelkezik érvényes kulcs-és OAuth2 jogkivonattal, bárki meghívhatja ezt bárhonnan – ideális esetben az API Management-on keresztüli összes kérést kényszeríteni szeretné.
   > Ha a API Management használati szintet használja, nem fogja tudni végrehajtani ezt a zárolást VIP-ként, mivel az adott szinten nincs dedikált statikus IP-cím, az API-hívások zárolásának módszerét kell használnia a közös titkos kulcson keresztül, így a 11-13-es lépések nem lesznek lehetségesek.

1. A "hitelesítés/engedélyezés" panel lezárása 
1. Válassza a hálózatkezelés lehetőséget, majd válassza a "hozzáférési korlátozások" lehetőséget.
1. Ezután zárja be az engedélyezett Function app IP-címeket a API Management-példány VIP-címére. Ez a VIP a portál API Management – Áttekintés szakaszában látható.
1. Ha továbbra is használni szeretné a functions-portált, és az alábbi opcionális lépéseket is el kívánja végezni, adja hozzá a saját nyilvános IP-címét vagy CIDR-tartományát.
1. Ha egy engedélyezési bejegyzés szerepel a listán, az Azure egy implicit megtagadási szabályt ad hozzá az összes többi cím blokkolásához. 

A CIDR formázott blokkokat kell hozzáadnia az IP-korlátozások panelhez. Ha egyetlen címet kell hozzáadnia, például a API Management VIP-t, hozzá kell adnia a következő formátumban: xx. xx. xx. xx.

   > [!NOTE]
   > A Function API mostantól nem hívható meg, mint az API Management vagy a címe.
   
## <a name="import-the-function-app-definition"></a>A Function app-definíció importálása
1. Nyissa meg a *API Management*panelt, majd nyissa meg a *példányt*.
1. Válassza ki az API-kat a példány API Management szakaszából.
1. Az "új API hozzáadása" panelen válassza a "függvényalkalmazás" lehetőséget, majd az előugró ablak tetején válassza a "teljes" lehetőséget.
1. Kattintson a Tallózás gombra, válassza ki az API-t üzemeltető Function alkalmazást, és kattintson a Kiválasztás gombra.
1. Adja meg az API számára a API Management belső használatra vonatkozó nevét és leírását, és adja hozzá a "korlátlan" termékhez.
1. Ügyeljen rá, hogy az alap URL-címet későbbi használatra jegyezze fel, majd kattintson a Létrehozás gombra.

## <a name="configure-oauth2-for-api-management"></a>API Management Oauth2 konfigurálása

1. Ezután válassza ki a OAuth 2,0 panelt a biztonság lapon, és kattintson a "Hozzáadás" gombra.
1. Adja meg a hozzáadott OAuth-végpont *megjelenítendő nevét* és *leírását* (ezek az értékek a következő lépésben jelennek meg Oauth2-végpontként).
1. Megadhat bármilyen értéket az ügyfél-regisztrációs oldal URL-címében, mivel ez az érték nem lesz használatban.
1. Jelölje be az *implicit hitelesítési* engedélyezési típust, és hagyja bejelölve az engedélyezési kód engedélyezésének típusát.
1. Váltson az *engedélyezési* és *jogkivonat* -végpont mezőire, és adja meg a korábban a jól ismert konfigurációs XML-dokumentumból rögzített értékeket.
1. Görgessen lefelé, és töltse fel az "erőforrás" nevű *további Body paramétert* a háttérbeli függvény API-ÜGYFELÉnek azonosítójával a Azure ad B2C alkalmazás regisztrációja alatt
1. Válassza az "ügyfél hitelesítő adatai" lehetőséget, állítsa be az ügyfél-azonosítót a fejlesztői konzol alkalmazásának AZONOSÍTÓJÁRA – ezt a lépést kihagyhatja, ha a felhasználás API Management modellt használja.
1. Állítsa az ügyfél titkát a korábban rögzített kulcsra – ezt a lépést kihagyhatja, ha a felhasználás API Management modellt használja.
1. Végül pedig az API Managementtól a későbbi használat érdekében rögzítse az Auth Code-támogatás redirect_uri.

## <a name="set-up-oauth2-for-your-api"></a>Oauth2 beállítása az API-hoz
1. Az API a portál bal oldalán jelenik meg a "minden API" szakaszban, majd az API megnyitásához kattintson rá.
1. Válassza a beállítások lapot.
1. A beállítások frissítéséhez válassza a "OAuth 2,0" lehetőséget a felhasználói hitelesítés választógombot választva.
1. Válassza ki a korábban megadott OAuth-kiszolgálót.
1. Jelölje be a "hatókör felülbírálása" jelölőnégyzetet, és adja meg a háttérbeli API-híváshoz korábban rögzített hatókört.

   > [!NOTE]
   > Most már van egy API Management-példánya, amely tudja, hogyan kérhet le hozzáférési jogkivonatokat a Azure AD B2Ctól a kérelmek engedélyezéséhez és a Oauth2 Azure Active Directory B2C konfigurációjának megismeréséhez.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>A **CORS** és a **validate-JWT** szabályzatok beállítása

> A következő részeket kell követni, függetlenül attól, hogy milyen APIM-réteg van használatban. 

1. Váltson vissza a tervezés lapra, és válassza az "összes API" lehetőséget, majd kattintson a kód nézet gombra a szabályzat-szerkesztő megjelenítéséhez.
1. Szerkessze a bejövő szakaszt, és illessze be az alábbi XML-fájlt, hogy az a következőképpen hangzik.

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
1. Szerkessze az OpenID-config URL-címet, hogy az megfeleljen a regisztrálási vagy bejelentkezési szabályzatok jól ismert Azure AD B2C végpontjának.
1. Szerkessze a jogcím értékét úgy, hogy az megfeleljen az érvényes alkalmazás-AZONOSÍTÓnak, más néven a háttér API-alkalmazás ügyfél-AZONOSÍTÓjának, és mentse.

   > [!NOTE]
   > Az API Management mostantól képes válaszolni a JS SPA-alkalmazásokba érkező, több eredetű kérelmekre, és a kérésnek a Function API-ra való továbbítása előtt szabályozni fogja a sávszélesség-korlátozást és a JWT hitelesítési jogkivonat előzetes érvényesítését.

   > [!NOTE]
   > A következő szakasz nem kötelező, és nem vonatkozik a fejlesztői portált nem támogató **felhasználási** szintre.
   > Ha nem kívánja használni a fejlesztői portált, vagy nem használhatja azt a felhasználási szint használata óta, ugorja át ezt a lépést, és ugorjon a ["JavaScript-Spa felépítése az API-hoz](#build-the-javascript-spa-to-consume-the-api)" lehetőségre.

## <a name="optional-configure-the-developer-portal"></a>Választható A fejlesztői portál konfigurálása

1. Nyissa meg a Azure AD B2C panelt, és navigáljon az alkalmazás regisztrálásához a fejlesztői portálon
1. Állítsa be a "válasz URL-címe" bejegyzést arra a megjegyzett értékre, amelyet a API Management korábbi hitelesítési kód engedélyezésének redirect_uri konfigurált.

   Most, hogy a OAuth 2,0 felhasználói hitelesítés engedélyezve van a `Echo API` -ben, a fejlesztői konzol a felhasználó számára hozzáférési jogkivonatot szerez be az API meghívása előtt.

1. Tallózással keresse meg a `Echo API` fejlesztői portálon található bármelyik műveletet, és válassza a **kipróbálás** lehetőséget a fejlesztői konzol létrehozásához.
1. Jegyezze fel, hogy az **engedélyezési** szakasz egy új eleme, amely az imént hozzáadott engedélyezési kiszolgálónak felel meg.
1. Válassza az engedélyezési **kód** lehetőséget az engedélyezés legördülő listából, és a rendszer felszólítja, hogy jelentkezzen be az Azure ad-bérlőbe. Ha már bejelentkezett a fiókkal, előfordulhat, hogy a rendszer nem kérdezi le.
1. A sikeres bejelentkezés után a rendszer egy `Authorization: Bearer` fejlécet ad hozzá a kérelemhez, amely a Azure ad B2C Base64-kódolású hozzáférési jogkivonattal rendelkezik. 
1. Kattintson a **Küldés** lehetőségre, és az API-t sikeresen meghívhatja.

   > [!NOTE]
   > Az API Management mostantól képes jogkivonatokat beszerezni a fejlesztői portálon az API teszteléséhez, és képes megérteni a definícióját, és megjeleníti a megfelelő teszt oldalt a fejlesztői portálon.

1. A API Management portál áttekintés paneljén kattintson a fejlesztői portál elemre az API rendszergazdájaként való bejelentkezéshez.
1. Itt Ön és az API más kiválasztott felhasználói is kipróbálhatják és meghívhatják őket a konzolról.
1. Válassza a termékek lehetőséget, majd válassza a "korlátlan" lehetőséget, majd válassza ki a korábban létrehozott API-t, és kattintson a "kipróbálás" gombra.
1. Az API-előfizetési kulcs elrejtésének megszüntetése, és a kérelem URL-címének másolása, amelyre később szükség lesz.
1. Válassza a implicit lehetőséget is a OAuth-hitelesítés legördülő menüből, és előfordulhat, hogy az itt megadott előugró ablakban kell hitelesítenie magát.
1. Kattintson a Send (küldés) gombra, és ha minden rendben van, a függvényalkalmazás a Hello-üzenettel kell válaszolnia az API Management használatával egy 200 OK üzenettel és néhány JSON-val.

   > [!NOTE]
   > Gratulálunk, mostantól Azure AD B2C, API Management és Azure Functions együtt dolgozhat az API-k közzétételével, biztonságossá tételével és felhasználásával. Előfordulhat, hogy észrevette, hogy az API-t a metódus használatával kétszer is biztonságossá teszi, egyszer a API Management OCP-Subscription-Key Fejléctel, és egyszer a következő engedélyekkel: tulajdonos JWT.
   > Helyes lenne, mivel ez a példa egy JavaScript-alapú egyoldalas alkalmazás, a API Management kulcsot csak a ráta-korlátozási és számlázási hívások esetében használjuk.
   > A tényleges engedélyezést és hitelesítést a Azure AD B2C kezeli, és a rendszer a JWT ágyazja be, amely API Management, egyszer pedig a Azure Functions alapján érvényesítve lesz.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>A JavaScript SPA felépítése az API használatára
1. Nyissa meg a Storage-fiókok panelt a Azure Portal 
1. Válassza ki a létrehozott fiókot, és válassza a "statikus webhely" panelt a beállítások szakaszban (ha nem jelenik meg a "statikus webhely" beállítás, ellenőrizze, hogy létrehozott-e v2-fiókot).
1. Állítsa a statikus web hosting szolgáltatást "engedélyezve" értékre, és állítsa az index dokumentum nevét "index.html" értékre, majd kattintson a Save (Mentés) gombra.
1. Jegyezze fel az elsődleges végpont tartalmát, mivel ez a hely, ahol a előtér-helyet üzemeltetni fogja. 

   > [!NOTE]
   > Használhat Azure Blob Storage + CDN-újraírást, vagy Azure App Service, de a Blob Storage statikus webhely-üzemeltetési funkciója egy alapértelmezett tárolót biztosít számunkra, amely az Azure Storage-ban statikus webtartalomot/HTML/js/CSS-t szolgál ki, és a nulla működéshez az alapértelmezett oldalt következteti ki számunkra.

## <a name="upload-the-js-spa-sample"></a>A JS SPA-minta feltöltése
1. Továbbra is a Storage-fiók panelen jelölje ki a "Blobok" panelt a blob szolgáltatás szakaszban, majd kattintson a jobb oldali ablaktáblán megjelenő $web tárolóra.
1. Mentse az alábbi kódot egy, a gépen helyileg található fájlba index.html néven, majd töltse fel a index.html fájlt a $web tárolóba.

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

1. Keresse meg az előző szakaszban korábban tárolt statikus webhely elsődleges végpontját.

   > [!NOTE]
   > Gratulálunk, egy JavaScript-alapú egyoldalas alkalmazást helyezett üzembe az Azure Storage-ban, mivel még nem konfigurálta a JS-alkalmazást az API kulcsaival, vagy a JS-alkalmazást még nem konfigurálta a Azure AD B2C részleteivel – a lap még nem fog működni, ha megnyitja.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>A JS SPA konfigurálása Azure AD B2Choz
1. Most már tudjuk, hol vannak: az SPA-t konfigurálhatja a megfelelő API Management API-címnek és a megfelelő Azure AD B2C alkalmazás-/ügyfél-azonosítóknak megfelelően.
1. Lépjen vissza a Azure Portal Storage panelre, és kattintson a index.html elemre, majd válassza a "blob szerkesztése" lehetőséget. 
1. Frissítse az Auth részleteit úgy, hogy az megfeleljen a B2C-ben korábban regisztrált előtér-alkalmazásnak, és azt, hogy az "b2cScopes" értékek az API-háttérhez tartoznak.
1. A webApi kulcs és az API URL-címe az API-művelet API Management teszt paneljén található.
1. Hozzon létre egy APIM-előfizetési kulcsot fejléc szerint a API Management vissza a API Management panelre, majd válassza az "előfizetések" lehetőséget, és kattintson az "előfizetés hozzáadása" gombra, majd mentse a rekordot. A létrehozott sor melletti három pontra (...) kattintva megjelenítheti a kulcsokat, így az elsődleges kulcs másolását is lehetővé teszi.
1. A következő kódhoz hasonlóan kell kinéznie:-  

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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Az átirányítási URI-k beállítása a Azure AD B2C előtér-alkalmazáshoz
1. Nyissa meg a Azure AD B2C panelt, és navigáljon az alkalmazás regisztrációja a JavaScript frontend-alkalmazáshoz
1. Állítsa be az átirányítási URL-címet arra a megjegyzett értékre, amikor korábban már beállította a statikus webhely elsődleges végpontját

   > [!NOTE] 
   > Ez a konfiguráció azt eredményezi, hogy az előtér-alkalmazás egy olyan hozzáférési jogkivonatot kap, amely a Azure AD B2Ctól megfelelő jogcímeket fogad.
   > A SPA a háttér-API hívásakor a https-fejlécben szereplő tulajdonosi jogkivonatként fogja tudni felvenni ezt a tulajdonságot.
   > API Management előre érvényesíti a tokent, és az előfizetői kulcs korlátozza a végpont felé irányuló hívásokat, mielőtt átadná a kérést az Azure Function API-nak.
   > A SPA a választ a böngészőben fogja megjeleníteni.

   > *Gratulálunk, Ön konfigurálta Azure AD B2C, Azure API Management, Azure Functions Azure App Service engedélyezését, hogy tökéletes harmóniában működjön!*

   > [!NOTE]
   > Most már van egy egyszerű alkalmazásunk egy egyszerű, biztonságos API-val, teszteljük.

## <a name="test-the-client-application"></a>Az ügyfélalkalmazás tesztelése
1. Nyissa meg a korábban létrehozott Storage-fiókban megjegyzett minta alkalmazás URL-címét
1. Kattintson a "Bejelentkezés" gombra a jobb felső sarokban, ez a gombra kattintva megnyithatja a Azure AD B2C regisztrációs vagy bejelentkezési profilját.
1. A JWT a képernyő "bejelentkezett" szakaszába való beléptetése után jelenik meg.
1. Most kattintson a "webes API hívása" lehetőségre, és a lapon a biztonságos API-ból visszaadott értékeket kell frissítenie.

## <a name="and-were-done"></a>És készen vagyunk
A fenti lépések módosíthatók és szerkeszthetők úgy, hogy a Azure AD B2C számos különböző felhasználási igényét lehetővé tegyék a API Management.

## <a name="next-steps"></a>Következő lépések
* További információ a [Azure Active Directory és a OAuth 2.0-s](../active-directory/develop/authentication-vs-authorization.md)verzióról.
* További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.
* A háttér-szolgáltatás biztonságossá tételének egyéb módjaival kapcsolatban lásd: [kölcsönös Tanúsítványos hitelesítés](api-management-howto-mutual-certificates.md).
* [Hozzon létre egy API Management Service-példányt](get-started-create-service-instance.md).
* [Az első API kezelése](import-and-publish.md).
