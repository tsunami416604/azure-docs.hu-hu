---
title: A Twilio használata hang- és SMS-hez (.NET) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. A .NET nyelven írt kódminták.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69876806"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>A Twilio használata az Azure hang- és SMS-funkcióihoz
Ez az útmutató bemutatja, hogyan hajthat végre közös programozási feladatokat az Azure-beli Twilio API-szolgáltatással. A tárgyalt forgatókönyvek közé tartozik a telefonhívás és a rövid üzenetszolgáltatás (SMS) küldése. A Twilio használatával, valamint a hang- és SMS-használattal kapcsolatos további információkért tekintse meg a [Következő lépések szakaszt.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Mi az a Twilio?
A Twilio az üzleti kommunikáció jövőjét működteti, lehetővé téve a fejlesztők számára, hogy hang- és VoIP-üzeneteket ágyaznak be az alkalmazásokba. Virtualizálják az összes szükséges infrastruktúrát egy felhőalapú, globális környezetben, és a Twilio communications API platformon keresztül teszik ki. Az alkalmazások egyszerűen elkészíthetők és méretezhetők. A felosztó-kirovó díjszabásnak és a felhőmegbízhatóságnak a kihasználása mellett is élvezheti a rugalmasságot.

**A Twilio Voice** lehetővé teszi, hogy az alkalmazások telefonhívásokat kezdeményezzenek és fogadjanak. **A Twilio SMS** lehetővé teszi, hogy az alkalmazások SMS-üzeneteket küldjenek és fogadjanak. **Twilio ügyfél** lehetővé teszi, hogy voIP hívásokat bármilyen telefonról, táblagépről vagy böngészőből, és támogatja a WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio árképzés és különleges ajánlatok
Az Azure-ügyfelek [különleges ajánlatot](https://www.twilio.com/azure)kapnak: a Twilio-fiók frissítésekén $10 ingyenes 10 usd Twilio-kreditet. Ez a Twilio-kredit bármely Twilio-használatra alkalmazható ($10 kredit egyenértékű 1000 SMS-üzenet küldésével vagy akár 1000 bejövő hangperc fogadásával, a telefonszám és az üzenet vagy a hívás céljának helyétől függően). Váltsa be ezt a Twilio-jóváírást, és kezdje el a [twilio.com/azure](https://twilio.com/azure).

A Twilio egy felosztó-kiosztó szolgáltatás. Nincsenek beállítási díjak, és bármikor megszüntetheti fiókját. További részleteket a [Twilio Pricing](https://www.twilio.com/voice/pricing)oldalon talál.

## <a name="concepts"></a><a id="Concepts"></a>Fogalmak
A Twilio API egy RESTful API, amely hang- és SMS-funkciókat biztosít az alkalmazások számára. Az ügyféltárak több nyelven is elérhetők; a listát a [Twilio API Libraries című témakörben található.][twilio_libraries]

A Twilio API fő szempontjai a Twilio-műveletek és a Twilio markup nyelv (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-igék
Az API Twilio-igéket használ; A ** &lt;Say&gt; ** ige például arra utasítja a Twilio-t, hogy hallhatóan kézbesítsen egy üzenetet egy hívásra.

Az alábbi lista a Twilio-igéket tartalmazza.  Ismerje meg a többi igét és képességet a [Twilio Markup Language dokumentációban](https://www.twilio.com/docs/api/twiml)keresztül.

* `<Dial>`: A hívót egy másik telefonhoz csatlakoztatja.
* `<Gather>`: A telefon billentyűzetén megadott numerikus számjegyeket gyűjti.
* `<Hangup>`: Befejezi a hívást.
* `<Play>`: Hangfájl lejátszása.
* `<Pause>`: A megadott számú másodpercig csendben várakozik.
* `<Record>`: Rögzíti a hívó hangját, és visszaadja a felvételt tartalmazó fájl URL-címét.
* `<Redirect>`: Egy hívás vagy SMS vezérlésének átvitele a TwiML-re egy másik URL-címen.
* `<Reject>`: Elutasítja a Twilio-számra irányuló bejövő hívást anélkül, hogy
* `<Say>`: A hívás közben igéző szöveget beszédmé alakítja.
* `<Sms>`: SMS-üzenetet küld.

### <a name="twiml"></a>TwiML
A TwiML a Twilio-műveleteken alapuló XML-alapú utasítások készlete, amelyek tájékoztatják a Twilio-t a hívás vagy SMS feldolgozásáról.

Például a következő TwiML a **Hello World** szöveget beszédmé alakítja.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Amikor az alkalmazás meghívja a Twilio API-t, az API-paraméterek egyike az URL-címet, amely a TwiML-választ adja vissza. Fejlesztési célokra használhatja a Twilio által biztosított URL-címeket az alkalmazások által használt TwiML-válaszok biztosításához. A TwiML-válaszok létrehozásához saját URL-eket is üzemeltethet, egy másik lehetőség pedig a **TwiMLResponse** objektum használata.

A Twilio-műveletekről, azok attribútumairól és a TwiML-ről a [TwiML][twiml]című témakörben talál további információt. A Twilio API-ról további információt a [Twilio API című témakörben][twilio_api]talál.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio-fiók létrehozása
Ha készen áll a Twilio-fiók leválasztására, regisztráljon a [Try Twilio][try_twilio]című naphoz. Kezdheti egy ingyenes fiókkal, és később frissítheti fiókját.

Amikor regisztrál egy Twilio-fiókot, kap egy fiókazonosítót és egy hitelesítési jogkivonatot. Mindkettő re van szükség a Twilio API-hívások. A fiókjához való jogosulatlan hozzáférés megakadályozása érdekében tartsa biztonságban a hitelesítési jogkivonatot. A fiókazonosító és a hitelesítési jogkivonat a [Twilio-fiók lapon][twilio_account]tekinthető meg, a **ACCOUNT SID,** illetve az **AUTH TOKEN**mezőkben.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Azure-alkalmazás létrehozása
Egy Azure-alkalmazás, amely egy Twilio-kompatibilis alkalmazást üzemeltet, nem különbözik bármely más Azure-alkalmazástól. Hozzáadja a Twilio .NET függvénytárat, és konfigurálja a szerepkört a Twilio .NET-kódtárak használatára.
A kezdeti Azure-projektek létrehozásáról az [Azure-projekt létrehozása a Visual Studio használatával][vs_project]című témakörben talál.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Az alkalmazás konfigurálása twilio-tárak használatára
A Twilio a Twilio REST API-val és a Twilio-ügyféllel a TwiML-válaszok létrehozásához egyszerű és egyszerű módon körbefolyatható .

A Twilio öt könyvtárat biztosít a .NET fejlesztők számára:

| Erőforrástár | Leírás |
| --- | --- |
| Twilio.API | A core Twilio-könyvtár, amely a Twilio REST API-t egy barátságos .NET-könyvtárba csomagolja. Ez a könyvtár a .NET, a Silverlight és a Windows Phone 7 rendszerhez érhető el. |
| Twilio.TwiML | A TwiML-jelölések .NET-barát létrehozásának módját biztosítja. |
| Twilio.MVC | Az MVC-t használó ASP.NET fejlesztők számára ez a tár tartalmaz egy TwilioController, TwiML ActionResult és request validation attribútumot. |
| Twilio.WebMatrix | A Microsoft ingyenes WebMatrix fejlesztői eszközét használó fejlesztők számára ez a könyvtár razor szintaxist segítőket tartalmaz a különböző Twilio-műveletekhez. |
| Twilio.Client.Képesség | A Képesség token generátort tartalmazza a Twilio Client JavaScript SDK-hoz való használatra. |

> [!Important]
> Minden tárak hoz .NET 3.5, Silverlight 4 vagy Windows Phone 7 vagy újabb.

Az ebben az útmutatóban megadott minták a Twilio.API-kódtár használatával.

A könyvtárak a Visual Studio 2010-hez 2010-ig elérhető [NuGet csomagkezelő bővítményhasználatával telepíthetők.](https://www.twilio.com/docs/csharp/install)  A forráskód a [GitHubon][twilio_github_repo]található, amely tartalmaz egy wikit, amely tartalmazza a könyvtárak teljes dokumentációját.

Alapértelmezés szerint a Microsoft Visual Studio 2010 a NuGet 1.2-es verzióját telepíti. A Twilio-kódtárak telepítéséhez a NuGet vagy újabb 1.6-os verziója szükséges. A NuGet telepítéséről és frissítésével [https://nuget.org/][nuget]kapcsolatos további tudnivalók: .

> [!NOTE]
> A NuGet legújabb verziójának telepítéséhez először el kell távolítania a betöltött verziót a Visual Studio Extension Manager segítségével. Ehhez a Visual Studio alkalmazást rendszergazdaként kell futtatnia. Ellenkező esetben az Eltávolítás gomb le van tiltva.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>A Twilio-tárak hozzáadása a Visual Studio-projekthez:
1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobb gombbal **a Hivatkozások elemre.**
3. Kattintson **a NuGet-csomagok kezelése lehetőségre...**
4. Kattintson **az Online gombra.**
5. A keresés online mezőbe írja be a *twilio*kifejezést.
6. Kattintson a **Telepítés** a Twilio-csomagon.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Útmutató: Kimenő hívás
Az alábbiakban bemutatjuk, hogyan lehet kimenő hívást kezdeményezni a **CallResource** osztály használatával. Ez a kód is használ egy Twilio által biztosított hely a Twilio Markup Language (TwiML) válasz ad vissza. Helyettesítse az értékeket a **be-** és a **telefonszámok,** és győződjön meg arról, hogy ellenőrizze a **telefonszámát** a Twilio-fiók a kód futtatása előtt.

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

A **CallResource.Create** metódusnak átadott paraméterekről további [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]információt a.

Mint említettük, ez a kód egy Twilio által biztosított hely a TwiML-válasz visszaadásához. Ehelyett használhatja a saját webhelyét a TwiML-válasz megadásához. További információ: [Hogyan: TwiML-válaszok biztosítása a saját webhelyéről.](#howto_provide_twiml_responses)

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Útmutató: SMS küldése
Az alábbi képernyőkép bemutatja, hogyan küldhet SMS-üzenetet a **MessageResource** osztály használatával. A **from** number a Twilio által biztosított próbaverziós fiókok SMS-üzenetek küldéséhez. A **to** kód futtatása előtt ellenőrizni kell a to number ellenőrizni kell a Twilio-fiók.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Útmutató: TwiML-válaszok biztosítása a saját webhelyéről
Amikor az alkalmazás kezdeményezegy hívást a Twilio API-t – például a **CallResource.Create** metóduson keresztül – Twilio elküldi a kérelmet egy URL-címre, amely várhatóan egy TwiML-választ ad vissza. A példa a [Hogyan: Kimenő hívás a](#howto_make_call) Twilio [https://twimlets.com/message][twimlet_message_url] által megadott URL-címet használja a válasz visszaadásához.

> [!NOTE]
> Bár a TwiML-t a webszolgáltatások számára tervezték, a TwiML a böngészőben tekinthető meg. Ide kattintva [https://twimlets.com/message][twimlet_message_url] például `<Response>` egy üres elem jelenik meg; másik példaként [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) kattintson `<Response>` ide egy &lt;Say&gt; elemet tartalmazó elem megtekintéséhez.
>

Ahelyett, hogy a Twilio által megadott URL-címre támaszkodna, létrehozhat saját URL-címet, amely HTTP-válaszokat ad vissza. A webhelyet bármely olyan nyelven létrehozhatja, amely HTTP-válaszokat ad vissza. Ez a témakör feltételezi, hogy egy ASP.NET általános kezelő url-címét fogja üzemeltetni.

A következő ASP.NET Handler egy TwiML-választ hoz, amely a hívás során **a Hello World-öt** mondja.

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
    
Amint az a fenti példából is látható, a TwiML-válasz egyszerűen EGY XML-dokumentum. A Twilio.TwiML könyvtár olyan osztályokat tartalmaz, amelyek twiML-t hoznak létre. Az alábbi példa a fenti módon adja meg az egyenértékű választ, de a **VoiceResponse osztályt** használja.

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

A TwiML-ről további [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)információt a .

Miután beállította a TwiML-válaszok megadásának módját, átadhatja az URL-címet a **CallResource.Create** metódusnak. Ha például egy MyTwiML nevű webalkalmazás telepítve van egy Azure-felhőszolgáltatásba, és a ASP.NET Kezelőjének neve mytwiml.ashx, az URL-cím átadható a **CallResource.Create-nak** a következő kódminta szerint:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

A Twilio azure-beli használatával kapcsolatos további tudnivalókat ASP.NET a [Twilio használatával az Azure-beli webes szerepkörben című telefonhívás lehívása][howto_phonecall_dotnet]című témakörben talál.

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
