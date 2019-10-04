---
title: A Twilio használata a Voice és az SMS (.NET) szolgáltatáshoz | Microsoft Docs
description: Megtudhatja, hogyan készíthet telefonhívást, és hogyan küldhet SMS-üzenetet a Twilio API szolgáltatással az Azure-ban. A .NET-ben írt mintakód-minták.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876806"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>A Twilio használata a hang-és SMS-funkciókhoz az Azure-ban
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure Twilio API szolgáltatásával. A tárgyalt forgatókönyvek közé tartozik a telefonhívás kezdeményezése és egy rövid üzenetküldési szolgáltatás (SMS) üzenet küldése. A Twilio és a hang-és SMS-alkalmazások alkalmazásokban való használatáról további információt a [következő lépések](#NextSteps) című szakaszban talál.

## <a id="WhatIs"></a>Mi az a Twilio?
A Twilio az üzleti kommunikáció jövőjét fejleszti, és lehetővé teszi, hogy a fejlesztők hang-, VoIP-és üzenetkezelési adatokat ágyaznak be alkalmazásokba. Minden szükséges infrastruktúra virtualizálása egy felhőalapú, globális környezetben, amely a Twilio Communications API platformon keresztül teszi elérhetővé. Az alkalmazások egyszerűen létrehozhatók és méretezhetők. Élvezze az utólagos elszámolású díjszabást, és használja ki a felhő megbízhatóságát.

A **Twilio Voice** lehetővé teszi az alkalmazások számára telefonhívások készítését és fogadását. A **TWILIO SMS** lehetővé teszi az alkalmazások számára az SMS-üzenetek küldését és fogadását. A **Twilio-ügyfél** lehetővé teszi, hogy bármilyen telefonról, táblaszámítógépről vagy böngészőből kezdeményezheti a VoIP-hívásokat, és támogatja a WebRTC.

## <a id="Pricing"></a>A Twilio díjszabása és különleges ajánlataink
A Twilio-fiók frissítésekor az Azure-ügyfelek [különleges ajánlatot](https://www.twilio.com/azure)kapnak: ingyenes, $10-Twilio kreditet. Ez a Twilio-kredit bármely Twilio-felhasználásra alkalmazható ($10 kredit, amely akár több mint 1 000 SMS-üzenetet küld, akár 1000 bejövő hang-percet is igénybe vehet, a telefonszám és az üzenet vagy a hívás célhelyének helyétől függően). Váltsa be ezt a Twilio-kreditet, és ismerkedjen meg a [twilio.com/Azure](https://twilio.com/azure)-vel.

A Twilio az utólagos elszámolású szolgáltatás. Nincsenek beállított díjak, és bármikor lezárhatja a fiókját. További részleteket a [Twilio díjszabását](https://www.twilio.com/voice/pricing)ismertető webhelyen talál.

## <a id="Concepts"></a>Fogalmak
A Twilio API egy REST-alapú API, amely hang-és SMS-funkciókat biztosít az alkalmazásokhoz. Az ügyféloldali kódtárak több nyelven is elérhetők; a listákat lásd: [TWILIO API][twilio_libraries]-kódtárak.

A Twilio API legfontosabb szempontjai a Twilio-műveletek és a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-műveletek
Az API a Twilio-műveletek használatát teszi lehetővé; például a **&lt;Say&gt;** művelet arra utasítja a Twilio, hogy hallhatóan kézbesítse az üzenetet egy híváson.

A következő lista az Twilio-műveletek listáját tartalmazza.  Ismerje meg a többi műveletet és képességeket a [Twilio Markup Language dokumentációján](https://www.twilio.com/docs/api/twiml)keresztül.

* `<Dial>`: Csatlakoztatja a hívót egy másik telefonhoz.
* `<Gather>`: A telefon billentyűzetén megadott numerikus számjegyeket gyűjti.
* `<Hangup>`: Egy hívást ér véget.
* `<Play>`: Hangfájl lejátszása.
* `<Pause>`: Csendes várakozás a megadott számú másodpercre.
* `<Record>`: A hívó hangját rögzíti, és egy, a rögzítést tartalmazó fájl URL-címét adja vissza.
* `<Redirect>`: Egy másik URL-címen keresztül továbbítja a hívást vagy SMS-t a TwiML.
* `<Reject>`: Elutasítja a Twilio-szám bejövő hívását a számlázás nélkül
* `<Say>`: Átalakítja a szöveget a híváson végrehajtott beszédre.
* `<Sms>`: SMS-üzenet küldése.

### <a name="twiml"></a>TwiML
A TwiML XML-alapú utasításokat tartalmaz a Twilio műveletek alapján, amelyek tájékoztatják a Twilio, hogy hogyan dolgozzák fel a hívást vagy az SMS-t.

Példaként a következő TwiML konvertálja a szöveget **"Helló világ!" alkalmazás** beszédre.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az egyik API-paraméter az a URL-cím, amely a TwiML választ adja vissza. A Twilio által biztosított URL-címek segítségével megadhatja az alkalmazások által használt TwiML-válaszokat. Saját URL-címeket is tárolhat a TwiML-válaszok létrehozásához, és egy másik lehetőség a **TwiMLResponse** objektum használata.

A Twilio-műveletekkel, azok attribútumaival és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [TWILIO API][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll egy Twilio-fiók beszerzésére, regisztráljon a [Twilio kipróbálása][try_twilio]gombra. Elindíthat egy ingyenes fiókot, és később is frissítheti a fiókját.

Amikor regisztrál egy Twilio-fiókra, megkapja a fiók AZONOSÍTÓját és a hitelesítési jogkivonatot. Mindkettőre szükség lesz a Twilio API-hívások létrehozásához. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében őrizze meg biztonságos hitelesítési tokenjét. A fiók-azonosító és a hitelesítési jogkivonat a [Twilio fiók lapján][twilio_account], a **fiók SID** és **hitelesítési jogkivonat**mezőjében látható.

## <a id="create_app"></a>Azure-alkalmazás létrehozása
Egy Twilio-kompatibilis alkalmazást futtató Azure-alkalmazás nem különbözik más Azure-alkalmazástól. Adja hozzá a Twilio .NET-függvénytárat, és konfigurálja a szerepkört a Twilio .NET-kódtárak használatára.
A kezdeti Azure-projektek létrehozásával kapcsolatos információkért lásd: [Azure-projekt létrehozása a Visual Studióval][vs_project].

## <a id="configure_app"></a>Az alkalmazás konfigurálása Twilio-kódtárak használatára
A Twilio olyan .NET segéd-kódtárakat biztosít, amelyek a Twilio különböző szempontjait biztosítják, így egyszerű és egyszerű módszereket biztosítanak a Twilio-REST API és a Twilio-ügyféllel való interakcióhoz TwiML-válaszok létrehozásához.

A Twilio öt kódtárat biztosít a .NET-fejlesztőknek:

| Erőforrástár | Leírás |
| --- | --- |
| Twilio. API | A Twilio REST API egy felhasználóbarát .NET-könyvtárban lévő központi Twilio könyvtár. Ez a könyvtár a .NET, a Silverlight és a Windows Phone-telefon 7 rendszerhez érhető el. |
| Twilio.TwiML | .NET-barát módszert biztosít a TwiML-jelölések létrehozásához. |
| Twilio. MVC | A ASP.NET MVC-t használó fejlesztők számára ez a könyvtár egy TwilioController, egy TwiML-ActionResult és egy kérelem-ellenőrzési attribútumot tartalmaz. |
| Twilio. WebMatrix | A Microsoft ingyenes WebMatrix-fejlesztői eszközét használó fejlesztők számára ez a könyvtár a különböző Twilio műveletekhez használható Razor szintaxisú segítőket tartalmaz. |
| Twilio. Client. képesség | A Twilio-ügyfél JavaScript SDK-val használható képesség token generátort tartalmazza. |

> [!Important]
> Minden függvénytárhoz .NET 3,5, Silverlight 4 vagy Windows Phone-telefon 7 vagy újabb rendszer szükséges.

Az útmutatóban megadott minták a Twilio. API függvénytárat használják.

A kódtárak [telepíthetők a NuGet csomagkezelő bővítménnyel](https://www.twilio.com/docs/csharp/install) , amely a Visual Studio 2010-ig elérhető a 2015-ig.  A forráskód a githubon fut [][twilio_github_repo], amely tartalmaz egy, a kódtárak használatára vonatkozó teljes dokumentációt tartalmazó wikit.

Alapértelmezés szerint a Microsoft Visual Studio 2010 telepíti a NuGet 1,2-es verzióját. A Twilio-kódtárak telepítéséhez a NuGet vagy újabb 1,6 verzióra van szükség. A NuGet telepítésével vagy frissítésével kapcsolatos információkért [https://nuget.org/][nuget]lásd:.

> [!NOTE]
> A NuGet legújabb verziójának telepítéséhez először el kell távolítania a betöltött verziót a Visual Studio Extension Manager használatával. Ehhez a Visual studiót rendszergazdaként kell futtatnia. Ellenkező esetben az Eltávolítás gomb le van tiltva.
>
>

### <a id="use_nuget"></a>A Twilio-kódtárak hozzáadása a Visual Studio-projekthez:
1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobbgombbal a referenciák elemre.
3. Kattintson a **NuGet-csomagok kezelése...** lehetőségre.
4. Kattintson az **online**lehetőségre.
5. A Keresés az interneten mezőbe írja be a következőt: *twilio*.
6. Kattintson a **telepítés** gombra a Twilio-csomagban.

## <a id="howto_make_call"></a>kézikönyv: Kimenő hívás létrehozása
Az alábbiakban bemutatjuk, hogyan lehet kimenő hívást kezdeményezni a **CallResource** osztály használatával. Ez a kód egy Twilio által biztosított helyet is használ a Twilio Markup Language (TwiML) válaszának visszaadásához. Helyettesítse be az értékeket a és **a** telefonszámok között, és ellenőrizze, hogy a kód futtatása előtt a Twilio **-** fiókhoz tartozó telefonszámot kell-e ellenőrizni.

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

További információ a **CallResource. Create** metódusban átadott paraméterekről: [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Ahogy említettük, ez a kód egy Twilio által biztosított helyet használ a TwiML válasz visszaadásához. Ehelyett használhatja a saját webhelyét a TwiML-válasz megadásához. További információkért lásd: [hogyan: Adja meg a TwiML-válaszokat a](#howto_provide_twiml_responses)saját webhelyéről.

## <a id="howto_send_sms"></a>kézikönyv: SMS-üzenet küldése
A következő képernyőfelvétel bemutatja, hogyan küldhet SMS-üzeneteket a **MessageResource** osztály használatával. Az SMS **-** üzenetek küldéséhez a Twilio által biztosított próbaverziót adja meg. A kódot a kód futtatása előtt ellenőrizni kell a Twilio **-** fiókhoz.

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

## <a id="howto_provide_twiml_responses"></a>kézikönyv: TwiML-válaszok megadása a saját webhelyéről
Amikor az alkalmazás kezdeményezi a Twilio API-t, például a **CallResource. Create** metódussal, a Twilio a kérelmet egy olyan URL-címre küldi, amely egy TwiML-válasz visszaadására vár. Példa a következőre [: A kimenő hívások](#howto_make_call) a Twilio által megadott URL-cím [https://twimlets.com/message][twimlet_message_url] használatával adják vissza a választ.

> [!NOTE]
> Míg a TwiML a webszolgáltatások számára készült, megtekintheti a TwiML a böngészőben. Ha például [https://twimlets.com/message][twimlet_message_url] egy üres &lt; [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) &gt; `<Response>` elemet szeretne megtekinteni, kattintson ide egy olyan elem megjelenítéséhez, amely egy Say elemet tartalmaz. `<Response>`
>

Ahelyett, hogy a Twilio által megadott URL-címet kellene megadnia, létrehozhat egy saját URL-helyet, amely a HTTP-válaszokat adja vissza. A helyet bármilyen nyelven létrehozhatja, amely a HTTP-válaszokat adja vissza. Ez a témakör feltételezi, hogy az URL-címet egy ASP.NET általános kezelője fogja üzemeltetni.

A következő ASP.NET-kezelő olyan TwiML választ, amely a híváson **"Helló világ!" alkalmazás** .

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
    
Ahogy a fenti példában is látható, a TwiML válasz egyszerűen egy XML-dokumentum. A Twilio. TwiML függvénytár olyan osztályokat tartalmaz, amelyek a TwiML-t fogják előállítani. Az alábbi példa a fentiekben látható egyenértékű választ állítja elő, de a **VoiceResponse** osztályt használja.

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

További információ a TwiML: [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Miután beállította a TwiML-válaszok megadásának módját, átadhatja ezt az URL-címet a **CallResource. Create** metódusnak. Ha például egy MyTwiML nevű webalkalmazást helyez üzembe egy Azure Cloud Service-ben, és a ASP.NET-kezelő neve MyTwiML. ashx, az URL-cím átadható a **CallResource. Create** néven az alábbi kódrészletben látható módon:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

További információ az Azure-beli Twilio az ASP.NET használatával történő használatáról: [telefonhívás kezdeményezése a Twilio használatával webes szerepkörben az Azure-ban][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
