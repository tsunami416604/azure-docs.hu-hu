---
title: Twilio használata Hanghívási és SMS (.NET) |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. .NET-keretrendszerben írt kódmintákat.
services: ''
documentationcenter: .net
author: devinrader
manager: twilio
editor: ''
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 087dcfd10b191dcd80ec4a70be8eb2e373e1d56b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427540"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Hogyan lehet a Twilio használata hanghívási és SMS-funkciókhoz az Azure-ból
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a Twilio API-t szolgáltatással az Azure-ban. Az ismertetett forgatókönyvek között megtalálható, így a telefonhívás, és a egy rövid üzenetet szolgáltatást (SMS) üzenetet küld. A Twilio és az alkalmazások használata hanghívási és SMS további információkért lásd: a [további lépések](#NextSteps) szakaszban.

## <a id="WhatIs"></a>Mit jelent a Twilio?
A Twilio jövőjének vállalati kommunikáció, így a fejlesztők hang, a VoIP és a üzenetkezelési alkalmazásokba történő beágyazása van. A Twilio API kommunikációs platformon keresztül adatokhoz hozzáférést biztosító felhőalapú, a globális környezetben szükséges összes infrastruktúra virtualizálása azokat. Alkalmazásokat hozhat létre egyszerű és méretezhető. Használja ki a rugalmasan használatalapú díjszabás, és kihasználhatják a felhő megbízhatóságát.

**A twilio használata Hanghívási** lehetővé teszi, hogy telefonhívásokat fogadja, és az alkalmazások. **A Twilio SMS** lehetővé teszi az alkalmazások számára az SMS-üzenetek küldése és fogadása. **A Twilio-ügyfél** lehetővé teszi bármely telefonon, táblagépen vagy böngészőben VoIP hívásait, és támogatja a WebRTC.

## <a id="Pricing"></a>A Twilio-díjszabás és ajánlatok küldésére
Az Azure-ügyfelek kap egy [a különleges ajánlat](https://www.twilio.com/azure): díjtalan 10 USD kredit Twilio, a Twilio-fiók frissítésekor. A Twilio-Kredit bármely Twilio-használati (10 USD kredit egyenértékű, akár 1000 SMS-üzenetek küldése vagy fogadása legfeljebb 1000 bejövő hangalapú perc, a telefon száma és az üzenet vagy hívás cél helyétől függően) is alkalmazható. A Twilio-kredit beváltásához, és látogasson el [ahoy.twilio.com/azure](https://ahoy.twilio.com/azure).

A Twilio szolgáltatás használatalapú fizetést biztosító. Nincsenek díjak sincsenek beállítás, és a fiókot bármikor bezárhatja. További információt talál [Twilio díjszabás](https://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API-t hanghívási és SMS-funkciókat biztosító alkalmazások. Ügyfélkódtárai több nyelven érhetők el egy listát lásd: [Twilio API függvénytárai][twilio_libraries].

Fontos szempontjai a Twilio API-t a, Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>A Twilio-műveletek
Az API-t felhasznál Twilio műveleteket; Ha például a **&lt;Say&gt;** művelet arra utasítja a Twilio hallhatóan kézbesíteni az üzenetet hívás.

Az alábbiakban látható egy Twilio-műveletek listájához.  Ismerje meg az egyéb műveletek és funkciókat [Twilio Markup Language dokumentáció](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: A hívó kapcsolódik egy másik telefonszámot.
* `<Gather>`: A telefon billentyűzeten beírt számjegyeket gyűjti.
* `<Hangup>`: A hívás véget ér.
* `<Play>`: A hangfájl játszik le.
* `<Pause>`: Csendes a megadott számú másodperc vár.
* `<Record>`: A hívó hangalapú rögzíti, és a felvétel tartalmazó fájl URL-címet adja vissza.
* `<Redirect>`: Hívás vagy SMS-t a TwiML egy másik URL-címen az adatátvitel irányítását.
* `<Reject>`: A Twilio-számra bejövő hívás elutasítja, számlázási nélkül
* `<Say>`: A szöveg-beszéd átalakítás, amely egy hívást történő alakítja át.
* `<Sms>`: Egy SMS-üzenetet küld.

### <a name="twiml"></a>TwiML
TwiML XML-alapú utasítások alapján a Twilio-műveleteket tartalmazó tájékoztatja a hívás feldolgozása Twilio vagy SMS.

Tegyük fel, a következő TwiML konvertálná szöveg **Hello World** át.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyik az URL-cím, amely a TwiML választ ad vissza. Fejlesztési célokra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti, a saját URL-címek a TwiML válaszok előállításához, és a egy másik lehetőség a **TwiMLResponse** objektum.

A Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API-t][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll a Twilio-fiók létrehozása, regisztráció: [próbálja meg Twilio][try_twilio]. Kezdje egy ingyenes fiókot, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiók, kap egy fiók Azonosítóját és a egy hitelesítési tokent. Mindkét lesz szükség a Twilio API-hívásokat. Fiókja a jogosulatlan hozzáférés megakadályozása érdekében, hogy a hitelesítési jogkivonat biztonságának megőrzése. A fiók Azonosítóját és a hitelesítési jogkivonat teljesítményobjektumok, a [Twilio-fiók oldalán][twilio_account], a címkével ellátott mezőket **fiók SID** és **hitelesítési JOGKIVONATÁT**, illetve.

## <a id="create_app"></a>Azure-alkalmazások létrehozása
Azure-alkalmazások a Twilio-kompatibilis alkalmazások üzemeltető nem különbözik bármely más Azure-alkalmazás. A Twilio .NET-kódtár hozzáadása, és konfigurálja a szerepkört a Twilio .NET-kódtárakra használatára.
A kezdeti Azure-projekt létrehozása információkért lásd: [Azure-projekt létrehozása Visual studióval][vs_project].

## <a id="configure_app"></a>A Twilio-kódtárak használata az alkalmazás konfigurálása
A Twilio .NET segítő-kódtárak együttese biztosít az egyszerű és könnyen módon kezelheti a Twilio REST API-t és a Twilio-ügyfél létrehozásához TwiML válaszok a Twilio különféle részeinek burkolása biztosít.

A Twilio öt kódtárak .NET-fejlesztők számára biztosít:

| Erőforrástár | Leírás |
| --- | --- |
| Twilio.API | A Twilio Alapkönyvtár, amely egy egyszerű .NET-kódtárában Twilio REST API-t. A könyvtár a .NET, a Silverlight és a Windows Phone 7 érhető el. |
| Twilio.TwiML | Lehetővé teszi .NET rövid TwiML jelölés létrehozásához. |
| Twilio.MVC | A fejlesztők számára az ASP.NET MVC használatával ebben a könyvtárban egy TwilioController TwiML ActionResult és érvényesítési kérelemattribútum tartalmazza. |
| Twilio.WebMatrix | A fejlesztők a Microsoft ingyenes WebMatrix fejlesztési eszköz használatával a könyvtárban a különböző Twilio Razor-szintaxis segítők. |
| Twilio.Client.Capability | A Twilio ügyfelet JavaScript SDK-val képesség tokengenerátor tartalmazza. |

> [!Important]
> Minden kódtárak .NET 3.5-ös verzióját, a Silverlight 4 vagy a Windows Phone 7 vagy újabb verzió szükséges.

A jelen útmutatóban ismertetett minták a Twilio.API könyvtár használata.

A szalagtárak lehet [a NuGet package manager bővítményének segítségével telepítve](https://www.twilio.com/docs/csharp/install) legfeljebb 2015 Visual Studio 2010-hez elérhető.  A forráskód lévő üzemeltetett [GitHub][twilio_github_repo], amely tartalmazza, amely tartalmazza a könyvtárak a teljes dokumentációját Wiki.

Alapértelmezés szerint a Microsoft Visual Studio 2010 NuGet 1.2-es verzióját telepíti. A Twilio-kódtárak telepítéséhez az 1.6-os NuGet vagy újabb verzió. Információ a telepítésekor vagy frissítésekor NuGet: [ https://nuget.org/ ] [ nuget].

> [!NOTE]
> A legújabb verzió telepítéséhez először el kell távolítania a betöltött verziója a Visual Studio-kiterjesztés kezelőjét használja. Ehhez futtatnia kell az Visual Studiót rendszergazdaként. Ellenkező esetben az Eltávolítás gomb le van tiltva.
>
>

### <a id="use_nuget"></a>A Visual Studio-projektben a Twilio-kódtárak hozzáadása:
1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobb gombbal **hivatkozások**.
3. Kattintson a **NuGet-csomagok kezelése...**
4. Kattintson a **Online**.
5. Az online be a keresőmezőbe írja be a *twilio*.
6. Kattintson a **telepítése** a Twilio-csomag.

## <a id="howto_make_call"></a>Hogyan: végezhet
A következőket mutatja be, hogy egy kimenő hívás használatával a **CallResource** osztály. Ez a kód is egy Twilio által biztosított helyet használ a Twilio Markup Language (TwiML) választ adja vissza. Helyettesítse be a saját értékeit a **való** és **a** telefonszámai, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám a Twilio-fiók a kód futtatása előtt.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

További információ az átadott paraméterek a **CallResource.Create** metódus, lásd: [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Ahogy már említettük, ez a kód egy Twilio által biztosított hely használatával TwiML a választ adja. Saját webhely segítségével adja meg a TwiML választ helyette. További információkért lásd: [hogyan: saját webhelyének biztosítanak TwiML parancsválaszait](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
A következő képernyőképen látható használ egy SMS üzenet küldése a **MessageResource** osztály. A **a** száma az SMS-üzenetek küldéséhez a próbafiókokon Twilio által biztosított. A **való** szám ellenőrizni kell a Twilio-fiók a kód futtatása előtt.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a id="howto_provide_twiml_responses"></a>Hogyan: Adja meg a saját webhelyén TwiML válaszok
Ha az alkalmazás kezdeményezi keresztül a hívást a Twilio API - példában a **CallResource.Create** metódus - Twilio a kérést küld egy kell visszaadnia TwiML válasz URL-CÍMÉT. A példában a [hogyan: végezhet](#howto_make_call) a Twilio által megadott URL-cím [ https://twimlets.com/message ] [ twimlet_message_url] , a választ adja vissza.

> [!NOTE]
> TwiML webszolgáltatások használatra szolgál, míg a TwiML tekintheti meg a böngészőben. Kattintson például [ https://twimlets.com/message ] [ twimlet_message_url] megtekintéséhez egy üres `<Response>` elem; másik példaként kattintson [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ](https://twimlets.com/message?Message%5B0%5D=Hello%20World) megtekintéséhez egy `<Response>` elem, amely tartalmazza a &lt;Say&gt; elemet.
>

Így nem kell hagyatkoznia a Twilio-megadott URL-címet, a saját URL-címet a hely által visszaadott HTTP-válaszok is létrehozhat. A hely által visszaadott HTTP-válaszok bármilyen nyelven hozhat létre. Ez a témakör azt feltételezi, hogy fogja üzemeltetni az URL-címet egy általános ASP.NET-kezelő.

A következő ASP.NET-kezelő TwiML választ, amely szerint létrehozza **Hello World** hívásakor.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Ahogy a fenti példában látható, a TwiML válasz egyszerűen egy XML-dokumentumot. A Twilio.TwiML könyvtárban osztályokat, amelyek az Ön számára TwiML hoz létre. Az alábbi példa hoz létre a megfelelő választ, ahogy fent látható, de használ a **VoiceResponse** osztály.

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

TwiML kapcsolatos további információkért lásd: [ https://www.twilio.com/docs/api/twiml ](https://www.twilio.com/docs/api/twiml).

Olyan megoldást nyújtanak TwiML válaszok beállítása után az URL-CÍMRE adhat át a **CallResource.Create** metódust. Például ha rendelkezik egy Azure-felhőszolgáltatásban üzembe helyezett MyTwiML nevű webalkalmazást, és az ASP.NET-kezelő nevét mytwiml.ashx, az URL-cím adható át **CallResource.Create** az alábbi kódmintában látható módon:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

A Twilio használatával az Azure-ban, az ASP.NET-tel kapcsolatos további információkért lásd: [biztosítása a Twilio használata az Azure-beli webes szerepkör telefonhívás][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
