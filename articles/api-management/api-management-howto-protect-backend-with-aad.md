---
title: "Egy webes API háttéralkalmazás, az Azure Active Directory és az API Management védelme |} Microsoft Docs"
description: "Ismerje meg, hogyan védi meg a webes API háttéralkalmazás az Azure Active Directory és az API Management."
services: api-management
documentationcenter: 
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
ms.openlocfilehash: 603a22059dcf07c68f4c6576ea1df97d810eacf3
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Hogyan védi meg a webes API háttéralkalmazás az Azure Active Directory és az API Management
A következő videó bemutatja, hogyan egy webes API háttéralkalmazás létrehozásához, és a védelmét, és Azure Active Directory és az API Management OAuth 2.0 protokoll használatával.  Ez a cikk áttekintése és további információt a videó lépéseit tartalmazza. A 24 perces videó bemutatja, hogyan számára:

* Hozza létre egy webes API háttéralkalmazás, és biztosíthatja az AAD - kezdő pozíció: 1:30-ben
* Importálja az API-t az API Management - 7:10 kezdődő
* A fejlesztői portálra, ahol az API - kezdő pozíció: 9:09 konfigurálása
* Az API - 18:08 kezdve az asztali alkalmazások konfigurálása
* Előre engedélyezze a kérelmek – 20:47 kezdődő JWT érvényesítési házirend konfigurálása

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Az Azure AD-címtár létrehozása
A webes API biztonságossá kell Azure Active Directory használatával biztonsági egy AAD-bérlőt. Ez a videó egy bérlő nevű **APIMDemo** szolgál. Hozzon létre egy AAD-bérlőt, hogy jelentkezzen be a [klasszikus Azure portál](https://manage.windowsazure.com) kattintson **új**->**alkalmazásszolgáltatások**->**Active Directory**  -> **Directory**->**egyéni létrehozás**. 

![Azure Active Directory][api-management-create-aad-menu]

Ebben a példában egy nevű könyvtár **APIMDemo** létrejön egy alapértelmezett tartomány nevű **DemoAPIM.onmicrosoft.com**. Ez a könyvtár használja a rendszer a videó keresztül.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Azure Active Directory által biztosított webes API szolgáltatás létrehozása
Ebben a lépésben egy webes API háttéralkalmazás létrehozása a Visual Studio 2013. Ez a lépés a videó kezdődik, 1:30. Webes API háttéralkalmazás-projekt létrehozása a Visual Studio kattintson **fájl**->**új**->**projekt**, és válassza a **ASP.NET webalkalmazás Alkalmazás** a a **webes** sablonok listájának. Ez a videó a projekt neve **APIMAADDemo**. A projekt létrehozásához kattintson az **OK** gombra. 

![Visual Studio][api-management-new-web-app]

Kattintson a **Web API** a a **jelöljön ki egy sablon listát** egy webes API-projekt létrehozása. Konfigurálhatja az Azure Directory hitelesítési kattintson **hitelesítés módosítása**.

![Új projekt][api-management-new-project]

Kattintson a **munkahelyi és iskolai fiókok**, és adja meg a **tartomány** , az AAD-bérlőt. Ebben a példában a tartomány pedig **DemoAPIM.onmicrosoft.com**. A Directory-tartomány lehet lekérni a **tartományok** a címtár lapján.

![Tartományok][api-management-aad-domains]

Adja meg a kívánt beállításokat a **hitelesítés módosítása** párbeszédpanel megnyitásához, és kattintson **OK**.

![Hitelesítés módosítása][api-management-change-authentication]

Amikor rákattint **OK** Visual Studio megkísérli az alkalmazás regisztrálása az Azure AD-címtár és a Visual Studio bejelentkezéshez kérheti. Jelentkezzen be egy rendszergazdai fiókkal a címtáron.

![Jelentkezzen be a Visual Studio][api-management-sign-in-vidual-studio]

Konfigurálhatja az ebben a projektben egy Azure webes API-t, jelölje be a **a felhőben lévő gazdagéphez** majd **OK**.

![Új projekt][api-management-new-project-cloud]

Meg lehet kérni jelentkezzen be Azure, és a Web App beállításához használhatja.

![Konfigurálás][api-management-configure-web-app]

Ebben a példában új **App Service-csomag** nevű **APIMAADDemo** van megadva.

Kattintson a **OK** a webalkalmazás konfigurálása és a projekt létrehozásához.

## <a name="add-the-code-to-the-web-api-project"></a>Adja hozzá a kódot a Web API-projekt
A következő lépése a videó hozzáadja a kódot a Web API-projektet. Ez a lépés 4:35 kezdődik.

Ebben a példában a webes API és a vezérlő alapvető Számológép szolgáltatás megvalósítja. A szolgáltatás a modell hozzáadásához kattintson a jobb gombbal **modellek** a **Solution Explorer** válassza **Hozzáadás**, **osztály**. Az osztály neve `CalcInput` kattintson **Hozzáadás**.

Adja hozzá a következő `using` nyilatkozat tetején a `CalcInput.cs` fájlt.

```csharp
using Newtonsoft.Json;
```

Cserélje le a következő kódot a létrehozott osztály.

```csharp
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Kattintson a jobb gombbal **tartományvezérlők** a **Megoldáskezelőben** válassza **Hozzáadás**->**vezérlő**. Válasszon **Web API 2 vezérlő - üres** kattintson **Hozzáadás**. Típus **CalcController** a vezérlő nevet, és kattintson a **Hozzáadás**.

![Vezérlő hozzáadása][api-management-add-controller]

Adja hozzá a következő `using` nyilatkozat tetején a `CalcController.cs` fájlt.

```csharp
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Cserélje le a következő kódot a létrehozott vezérlőosztály. Ez a kód valósítja meg a `Add`, `Subtract`, `Multiply`, és `Divide` alapvető Számológép API műveletek.

```csharp
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Nyomja le az **F6** építsenek, és ellenőrizze a megoldást.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban
Ebben a lépésben a Visual Studio projekt közzé van téve az Azure-bA. Ez a lépés a videó 5:45 kezdődik.

A projekt közzététele az Azure-ba, kattintson a jobb gombbal a **APIMAADDemo** a Visual Studio projekt, és válassza a **közzététel**. Az alapértelmezett beállítások megtartásához a **webhely közzététele** párbeszédpanel megnyitásához, és kattintson **közzététel**.

![Webes közzététel][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Engedélyezze, hogy az Azure AD szolgáltatás háttéralkalmazás
Egy új alkalmazást a háttérszolgáltatáshoz jön létre az Azure AD-címtár a webes API-projekt konfigurálása és a közzétételi folyamat részeként. Ebben a lépésben a videó, 6:13, kezdve a webes API háttéralkalmazás megadott engedélyekkel.

![Alkalmazás][api-management-aad-backend-app]

Kattintson a nevére, az alkalmazás a szükséges engedélyek konfigurálásához. Keresse meg a **konfigurálása** lapra, és görgessen le a **egyéb alkalmazások engedélyei** szakasz. Kattintson a **Alkalmazásengedélyek** melletti legördülő **Windows** **Azure Active Directory**, jelölje be a **címtáradatok olvasása** , és kattintson a **mentése**.

![Engedélyek hozzáadása][api-management-aad-add-permissions]

> [!NOTE]
> Ha **Windows** **Azure Active Directory** van az engedélyek más alkalmazásoknak nincs felsorolva, kattintson a **alkalmazás hozzáadása** , és adja hozzá a listából.
> 
> 

Jegyezze fel a **App Id URI** használható egy későbbi lépésben, ha egy Azure AD-alkalmazást úgy van konfigurálva az API Management fejlesztői portálján.

![App Id URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>A webes API-k importálnia kell az API Management
API-kat úgy vannak konfigurálva, a portálról API publisher, amely az Azure portálon keresztül érhető el. Az eléréséhez kattintson **Publisher portal** az eszköztáron az API Management szolgáltatás. Ha még nem hozott létre az API Management szolgáltatáspéldány, lásd: [hozzon létre egy API-kezelés szolgáltatás példányt] [ Create an API Management service instance] a a [kezelése az első API] [ Manage your first API] oktatóanyag.

![Közzétevő portál][api-management-management-console]

A műveleteket lehet [manuálisan hozzáadni az API-k](api-management-howto-add-operations.md), vagy az importálható lesz. Ez a videó műveletek importált 6:40 kezdődő Swagger formátumú.

Hozzon létre egy fájlt `calcapi.json` következő tartalommal, és mentse azt a számítógépet. Győződjön meg arról, hogy a `host` pontok attribútum a webes API háttéralkalmazás segítségével. Ebben a példában `"host": "apimaaddemo.azurewebsites.net"` szolgál.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

A számológép API importálásához kattintson a bal oldali **API Management** menü **API-k** elemére, majd kattintson az **API importálása** lehetőségre.

![API importálása gomb][api-management-import-api]

Hajtsa végre a következő lépésekkel állíthatja be a Számológép API.

1. Kattintson a **fájlból**, keresse meg a `calculator.json` fájlt mentette, és kattintson a **Swagger** választógombot.
2. Típus **Számológép** azokat a **webes API URL-címe utótag** szövegmező.
3. Kattintson a **Termékek (választható)** mezőre, és válassza a **Kezdő** lehetőséget.
4. Kattintson a **Mentés** gombra az API importálásához.

![Új API hozzáadása][api-management-import-new-api]

Az API importálása után megjelenik az API összefoglaló lapja a közzétevő portálon.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Sikertelenül hívja az API-t a fejlesztői portálján
Ezen a ponton az API-t az API Management importálva van, de nem még hívható sikeresen a developer portálról, mert a háttérszolgáltatáshoz védi az Azure AD-alapú hitelesítés. Ezt a video-7:40 az alábbi lépéseket követve kezdődő mutatják.

Kattintson a **fejlesztői portálján** a közzétevő portál jobb felső szélétől.

![Fejlesztői portál][api-management-developer-portal-menu]

Kattintson a **API-k** , és kattintson a **Számológép** API.

![Fejlesztői portál][api-management-dev-portal-apis]

Kattintson a **kipróbálás**.

![Kipróbálom][api-management-dev-portal-try-it]

Kattintson a **küldése** meg és jegyezze fel a választ állapotának **401 nem engedélyezett**.

![Küldés][api-management-dev-portal-send-401]

A kérelem nem engedélyezett, mert a háttér-API Azure Active Directory által védett. Az API-t a fejlesztői sikeresen hívása előtt portal engedélyezéséhez OAuth 2.0 használatával fejlesztők be kell állítani. Ez a folyamat az alábbi szakaszokban ismertetett.

## <a name="register-the-developer-portal-as-an-aad-application"></a>A fejlesztői portálján regisztrálható egy AAD-alkalmazást
Az első lépés a fejlesztői portálján engedélyezéséhez OAuth 2.0 használatával fejlesztők konfigurálása, hogy regisztrálja a fejlesztői portálján egy AAD-alkalmazást. Ezt mutatják 8:27 videóban kezdve.

Ez a videó, ebben a példában az első lépés a keresse meg az Azure AD-bérlő **APIMDemo** , és keresse meg a **alkalmazások** fülre.

![Új alkalmazás][api-management-aad-new-application-devportal]

Kattintson a **Hozzáadás** gombra kattintva hozzon létre egy új Azure Active Directory-alkalmazást, és válassza a **a szerveztem által fejlesztett alkalmazás hozzáadása**.

![Új alkalmazás][api-management-new-aad-application-menu]

Válasszon **webes alkalmazáshoz és/vagy webes API**, adjon meg egy nevet, és kattintson a Tovább nyílra. Ebben a példában **APIMDeveloperPortal** szolgál.

![Új alkalmazás][api-management-aad-new-application-devportal-1]

A **bejelentkezési URL-cím** adja meg az URL-címet a API Management szolgáltatás és a hozzáfűző `/signin`. Ebben a példában `https://contoso5.portal.azure-api.net/signin` szolgál.

A **azonosító URL-címet** adja meg az URL-címet a API Management szolgáltatás és a hozzáfűző néhány egyedi karaktert. Ezek lehetnek a kívánt karaktereket, és ebben a példában `https://contoso5.portal.azure-api.net/dp` szolgál. Ha a kívánt **alkalmazás tulajdonságainak** vannak konfigurálva, kattintson a pipa jelre az alkalmazás létrehozása.

![Új alkalmazás][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Az API Management OAuth 2.0 hitelesítési kiszolgáló konfigurálása
A következő lépés az OAuth 2.0 hitelesítési kiszolgáló konfigurálása az API Management. Ebben a lépésben bemutatott 9:43 kezdődik a videóban.

Kattintson a **biztonsági** kattintson a bal oldalon található API-kezelés menü, **OAuth 2.0**, és kattintson a **adja hozzá az engedélyezési** kiszolgáló.

![Engedélyezési kiszolgáló hozzáadása][api-management-add-authorization-server]

Adjon meg egy nevet és egy leírást a **neve** és **leírás** mezőket. Ezeket a mezőket az OAuth 2.0-engedélyezési kiszolgálót az API Management szolgáltatáspéldány azonosítására szolgálnak. Ebben a példában **engedélyezési server bemutató** szolgál. Később az OAuth 2.0-kiszolgáló API-hitelesítéshez használandó megadásakor kiválaszthatja ezt a nevet.

Az a **ügyfél regisztrációs URL-címe** adja meg például a helyőrző értékét `http://localhost`.  A **ügyfél regisztrációs URL-címe** mutat, a felhasználók segítségével hozza létre és konfigurálja a saját felhasználói a felhasználói fiókok kezelését támogató OAuth 2.0-s szolgáltatók oldal. Ebben a példában a felhasználók létrehozása és nem a saját felhasználói konfigurálása, így helyőrzőjeként szolgál.

![Engedélyezési kiszolgáló hozzáadása][api-management-add-authorization-server-1]

Ezt követően adja meg **engedélyezési végpont URL-címet** és **végponti URL-cím Token**.

![engedélyezési kiszolgáló][api-management-add-authorization-server-1a]

Ezek az értékek lekérhetők a **App végpontok** az AAD-alkalmazást a fejlesztői portálon létrehozott oldalán. Eléréséhez a végpontok navigáljon a **konfigurálása** lapján az AAD-alkalmazást, és kattintson a **végpontok megtekintése**.

![Alkalmazás][api-management-aad-devportal-application]

![Végpontok megtekintése][api-management-aad-view-endpoints]

Másolás a **OAuth 2.0 hitelesítési végpont** és illessze be azt a **engedélyezési végpont URL-címet** szövegmező.

![Engedélyezési kiszolgáló hozzáadása][api-management-add-authorization-server-2]

Másolás a **OAuth 2.0 token-végpont** és illessze be azt a **végponti URL-cím Token** szövegmező.

![Engedélyezési kiszolgáló hozzáadása][api-management-add-authorization-server-2a]

Mellett a jogkivonat végpontjához beillesztésével, adja hozzá a további válaszüzenettörzs-paramétert nevű **erőforrás** és az érték használható a **App Id URI** az aad-ben alkalmazásból a háttér-szolgáltatás, hogy mikor jött létre a A Visual Studio-projekt közzététele.

![App Id URI][api-management-aad-sso-uri]

Ezt követően adja meg az ügyfél hitelesítő adatait. Ezek a hozzáférési, ez esetben a fejlesztői portálján kívánt erőforrás hitelesítő adatai.

![Ügyfél-hitelesítő adatok][api-management-client-credentials]

A beolvasandó a **ügyfél-azonosító**, keresse meg a **konfigurálása** lapján a fejlesztői portálján, és másolja az AAD-alkalmazást a **ügyfél-azonosító**.

A beolvasandó a **Ügyfélkulcs** kattintson a **időtartam válassza** a legördülő a **kulcsok** szakaszt, és adjon meg egy időközt. Ebben a példában 1 év szolgál.

![Ügyfél-azonosító][api-management-aad-client-id]

Kattintson a **mentése** a konfiguráció mentéséhez, és a kulcs megjelenítéséhez. 

> [!IMPORTANT]
> Jegyezze fel ezt a kulcsot. Az Azure Active Directory konfigurációs ablak bezárása után a kulcs nem jeleníthető meg újra.
> 
> 

Másolja a vágólapra a kulcsot, lépjen vissza a közzétevő portal, illessze be a kulcs a **Ügyfélkulcs** szövegmező, és kattintson a **mentése**.

![Engedélyezési kiszolgáló hozzáadása][api-management-add-authorization-server-3]

Azonnal az ügyfél hitelesítő adatait a következő kód egy hitelesítésengedélyezési. Az Azure AD fejlesztői portál alkalmazásba az engedélyezési kód és a kapcsoló készítsen biztonsági másolatot konfigurálása lap, és illessze be a hitelesítésengedélyezési azokat a **válasz URL-CÍMEN** mezőben, majd kattintson a **mentése** újra.

![Válasz-URL][api-management-aad-reply-url]

A következő lépés a fejlesztői portálhoz AAD-alkalmazást az engedélyek beállításához. Kattintson a **Alkalmazásengedélyek** , és jelölje be a **címtáradatok olvasása**. Kattintson a **mentése** a módosítás mentéséhez, majd **alkalmazás hozzáadása**.

![Engedélyek hozzáadása][api-management-add-devportal-permissions]

Kattintson a keresés ikonra típus **APIM** be a kezdő a jelölését, jelölje ki a **APIMAADDemo**, és kattintson a pipa jelre mentéséhez.

![Engedélyek hozzáadása][api-management-aad-add-app-permissions]

Kattintson a **delegált engedélyek** a **APIMAADDemo** , és jelölje be a **hozzáférés APIMAADDemo**, és kattintson a **mentése**. Ez lehetővé teszi a fejlesztők a háttérszolgáltatáshoz eléréséhez portál alkalmazás.

![Engedélyek hozzáadása][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>A Számológép API OAuth 2.0 felhasználó engedélyezése
Most, hogy az OAuth 2.0-kiszolgáló van konfigurálva, megadhatja az API biztonsági beállításait. Ebben a lépésben bemutatott 14:30 kezdődik a videóban.

Kattintson a **API-k** a bal oldali menüben, majd kattintson a **Számológép** is megtekinthetik és konfigurálhatják a beállításai.

![A Számológép API][api-management-calc-api]

Keresse meg a **biztonsági** lapon jelölje a **OAuth 2.0** jelölőnégyzetet, válassza ki a kívánt engedélyezési kiszolgálót a a **engedélyezési server** legördülő, kattintson **Mentés**.

![A Számológép API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Sikeresen meg tudja hívni a Számológép API a developer portálról
Most, hogy az OAuth 2.0 hitelesítési az API konfigurálva van, a fejlesztői központból sikeresen hívható a műveleteket. Ebben a lépésben bemutatott a 15:00-tól kezdve a videó.

Lépjen vissza a **két egész számok hozzáadása** műveletet a fejlesztői portálján, majd kattintson a Számológép szolgáltatás **kipróbálás**. Jegyezze fel az új elem a **engedélyezési** szakasz megfelelő a hitelesítési kiszolgáló, az előzőekben adott hozzá.

![A Számológép API][api-management-calc-authorization-server]

Válassza ki **engedélyezési kód** engedélynek legördülő listában, és adja meg a használandó fiók hitelesítő adatait. Ha már be van jelentkezve a fiók nem kérheti.

![A Számológép API][api-management-devportal-authorization-code]

Kattintson a **küldése** meg és jegyezze fel a **válaszállapot** a **200 OK** és a válasz tartalmat a művelet eredményét.

![A Számológép API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Az API hívása asztali alkalmazások konfigurálása
A következő eljárással a videó 16:30 kezdődik, és konfigurálja a egyszerű asztali alkalmazást az API számára. Az első lépés az asztali alkalmazás regisztrálása az Azure ad-ben, és adjon neki hozzáférés a címtárhoz, és által a háttérszolgáltatáshoz. 18:25 nincs a művelet a Számológép API hívása az asztali alkalmazások bemutatója.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Előre a kérések hitelesítése JWT érvényesítési házirend konfigurálása
Az utolsó eljárás videóban 20:48 kezdődik, és bemutatja, hogyan használható a [érvényesítése JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) előre engedélyezésére kérelmek érvényesítésével megjeleníthető az egyes bejövő kérelmek a hozzáférési jogkivonatok házirend. Ha a kérelem nem érvényesíti a JWT érvényesítése házirend, a kérelem API Management le van tiltva, és nem kerül át, mentén háttérkiszolgálóra.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Egy másik bemutató konfigurálása, és ez a házirend használatával, lásd: [felhő fedik le a epizód 177: több API-felügyeleti funkciókat](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 13:50. Gyors továbbítsa 15:00, a házirendek a Helyicsoportházirend-szerkesztő konfigurált megjelenítéséhez, majd a művelet hívása a developer portálról, és a szükséges engedélyezési jogkivonat anélkül bemutatója 18:50.

## <a name="next-steps"></a>További lépések
* Tekintse meg több [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) API-kezeléssel kapcsolatos.
* Egyéb módjai a háttérszolgáltatás biztonságos, lásd: [kölcsönös tanúsítványhitelesítés](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
