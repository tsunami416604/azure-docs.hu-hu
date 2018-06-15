---
title: Twilio használata hang-és SMS (.NET) |} Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API szolgáltatás SMS üzenet küldése az Azure-on. Kódminták .NET.
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
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877026"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Hang-és SMS-képességek az Azure-ból Twilio használata
Ez az útmutató bemutatja, hogyan Azure a Twilio API szolgáltatás közös programozási feladatok elvégzéséhez. A tárgyalt forgatókönyvekben szerepel, így a telefonhívás, és egy rövid üzenetet szolgáltatás (SMS) üzenet küldésekor. A Twilio- és hang- és SMS használata az alkalmazások további információkért lásd: a [további lépések](#NextSteps) szakasz.

## <a id="WhatIs"></a>Mi az, hogy a Twilio?
Twilio a jövőbeli üzleti kommunikáció, így a fejlesztők hang, a VoIP és a üzenetküldési alkalmazásokba történő beágyazásához van működtetéséhez. Ezek virtualizálása keresztül a Twilio API kommunikációs platform, az ilyen felhőalapú, a globális környezetben szükséges összes infrastruktúrát. Alkalmazások olyan egyszerű hozhat létre és méretezhető. Élvezze a rugalmasságot az árképzés használatalapú fizetés,-felhő megbízhatóság igénybe.

**Twilio hang** lehetővé teszi, hogy az alkalmazások és a telefonhívásokat fogadja. **Twilio SMS** lehetővé teszi, hogy az alkalmazások SMS üzeneteket küldjön és fogadjon. **Twilio-ügyfél** lehetővé teszi a VoIP hívást a telefon, a táblagép vagy a böngésző és WebRTC támogatja.

## <a id="Pricing"></a>Twilio árak és a különleges ajánlatokkal
Az Azure-ügyfelek egy [a különleges ajánlat](http://www.twilio.com/azure): udvarias $10 Twilio-kredit a Twilio-fiók frissítésekor. A Twilio-jóváírási alkalmazhatók minden Twilio-használati (akár 1000 SMS-üzenetek küldésekor vagy fogadásakor 1000 bejövő hang perc, attól függően, hogy a telefon és üzenet vagy hívás cél helye egyenértékűek $10 követel). A Twilio-jóváírási beváltani és első lépések [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio egy olyan használatalapú szolgáltatás. Nincsenek nem beállításról díjakat, és bármikor bezárhatja a fiókját. További részletei: [Twilio árképzési](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Alapfogalmak
A Twilio API egy RESTful API, hang- és SMS-funkciókat biztosít az alkalmazások számára. Ügyféloldali kódtáraknál érhetők el több nyelven is; az útmutató, [Twilio API függvénytárai][twilio_libraries].

A Twilio API fő szempontjait Twilio-műveletek és Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-műveletek
Az API lehetővé teszi, hogy a Twilio használja műveletek; például a  **&lt;szóbeli&gt;**  parancs utasítja a Twilio hallhatóan hívás az üzenet kézbesítését.

A Twilio-műveletek listáját a következő:  További információk az egyéb műveletek és képességek keresztül [Twilio Markup Language dokumentáció](http://www.twilio.com/docs/api/twiml).

* **&lt;Telefonos kapcsolat&gt;**: a hívó kapcsolódik egy másik telefonon.
* **&lt;Gyűjtsön&gt;**: adta meg a telefon billentyűzetén számjegyek gyűjti.
* **&lt;Vonalbontás&gt;**: hívás véget ér.
* **&lt;Lejátszási&gt;**: hangfájl lejátszása.
* **&lt;Felfüggesztés&gt;**: Csendes megvárja-e a megadott számú másodpercnél tovább.
* **&lt;Rekord&gt;**: a hívó hang rögzíti, és egy a felvétel tartalmazó fájl URL-CÍMÉT adja vissza.
* **&lt;Átirányítási&gt;**: hívás vagy SMS irányítását átviszi a TwiML egy másik URL-címen.
* **&lt;Elutasítása&gt;**: a Twilio-szám egy bejövő hívás elutasítása a nélkül, számlázási
* **&lt;Tegyük fel például&gt;**: konvertálja szöveg-beszéd átalakítás hívás készült.
* **&lt;SMS&gt;**: SMS üzenetet küld.

### <a id="TwiML"></a>TwiML
TwiML olyan XML-alapú utasítások a Twilio-műveletek, amely tájékoztatja a Twilio hogyan lehet feldolgozni a hívást vagy SMS alapján.

Tegyük fel, a következő TwiML a szöveg volna átalakítása **Hello World** beszéddé.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Ha egy alkalmazás meghívja a Twilio API-t, az API paraméterek egyike az URL-címet, a TwiML választ ad vissza. Fejlesztési célra a Twilio által megadott URL-címek segítségével az alkalmazások által használt TwiML visszajelzést. Akkor is elhelyezheti a TwiML válaszok létrehozásához a saját URL-címeket, és egy másik lehetőség az **TwiMLResponse** objektum.

Twilio műveletek, az attribútumokat és TwiML kapcsolatos további információkért lásd: [TwiML][twiml]. A Twilio API-val kapcsolatos további információkért lásd: [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Twilio-fiók létrehozása
Amikor készen áll arra, hogy a Twilio-fiókot, feliratkozhat [próbálja Twilio][try_twilio]. Egy ingyenes fiókot kezdődnie, és később frissítse a fiókját.

Amikor regisztrál egy Twilio-fiókja, kapni fog egy Fiókazonosító és egy hitelesítési jogkivonatot. Is szükség lesz Twilio API-hívások indítása. Jogosulatlan hozzáférés elkerülése érdekében a fiókját, hogy a hitelesítési jogkivonat biztonságának megőrzése. A Fiókazonosítót és a hitelesítési jogkivonat megtekinthető a rendszer a [Twilio fióklapját][twilio_account], a mezőket, címkével **fiók SID** és **hitelesítési TOKEN**, illetve.

## <a id="create_app"></a>Az Azure-alkalmazások létrehozása
Az Azure-alkalmazások, amelyen a Twilio-engedélyezett alkalmazás nem eltér a többi Azure alkalmazás. A Twilio .NET könyvtár hozzáadása, és konfigurálja a szerepkört a Twilio .NET-kódtárakra használatára.
A kezdeti Azure-projekt létrehozása információkért lásd: [Azure-projekt létrehozása a Visual Studio][vs_project].

## <a id="configure_app"></a>Használhatók a Twilio-könyvtárak alkalmazás konfigurálása
Twilio .NET segítő szalagtárak, hogy a Twilio egyszerűen és könnyen interaktív módon a Twilio REST API-t és a Twilio-ügyfél TwiML válaszokat állít elő, így különböző aspektusainak burkolása biztosít.

Twilio öt szalagtárak biztosít a .NET-fejlesztők számára:
Részletes ismertetés|Leírás
---|---
Twilio.API|A Twilio Alapkönyvtár tördelve a Twilio-REST API-t egy rövid .NET könyvtárban. A könyvtár a .NET, a Silverlight, a Windows Phone 7 és érhető el.
Twilio.TwiML|Biztosítja az .NET rövid TwiML jelölés létrehozásához.
Twilio.MVC|ASP.NET MVC használó fejlesztők ezt a szalagtárat magában foglalja a TwilioController TwiML ActionResult és kérelem ellenőrző attribútuma.
Twilio.WebMatrix|A fejlesztők a Microsoft szabad WebMatrix fejlesztési eszköz segítségével a könyvtárban Razor szintaxis segítő különböző Twilio-műveletekhez.
Twilio.Client.Capability|A funkció a Twilio-ügyfél JavaScript SDK-val token generátor tartalmazza.

Vegye figyelembe, hogy a kódtárakat .NET 3.5-ös, a Silverlight 4 vagy a Windows Phone 7 vagy újabb szükséges.

A mintákat a jelen útmutatóban Twilio.API tár használatára.

A szalagtárak lehet [a NuGet package manager bővítmény segítségével telepített](http://www.twilio.com/docs/csharp/install) rendelkezésre álló legfeljebb 2015, Visual Studio 2010.  A forráskód üzemelteti [GitHub][twilio_github_repo], mert az egy Wiki, amely tartalmazza a könyvtárak segítségével teljes dokumentációja tartalmazza.

Alapértelmezés szerint a Microsoft Visual Studio 2010 NuGet 1.2-es verzióját telepíti. A Twilio-könyvtárak telepítéséhez az 1.6-os NuGet vagy újabb verzióra. Információ a telepítésekor vagy frissítésekor NuGet: [http://nuget.org/][nuget].

> [!NOTE]
> A NuGet legújabb verziójának telepítéséhez el kell távolítania a betöltött verziót, a Visual Studio-kiterjesztés kezelője segítségével. Ehhez a Visual Studio rendszergazdaként kell futtatnia. Ellenkező esetben az Eltávolítás gomb le van tiltva.
>
>

### <a id="use_nuget"></a>A Visual Studio-projekt a Twilio-kódtárak felvétele:
1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobb gombbal **hivatkozások**.
3. Kattintson a **NuGet-csomagok kezelése...**
4. Kattintson a **Online**.
5. Online keresőmezőbe, írja be a *twilio*.
6. Kattintson a **telepítése** a Twilio-csomagra.

## <a id="howto_make_call"></a>Hogyan: végezhet
A következő bemutatja, hogyan végezheti el egy kimenő hívás használatával a **CallResource** osztály. Ezt a kódot a Twilio által biztosított hely is használja a Twilio Markup Language (TwiML) válasz vissza. Helyettesítse a saját értékeit a **való** és **a** telefonszámokat, és győződjön meg arról, hogy ellenőrizze a **a** telefonszám Twilio-fiókja a kód futtatása előtt.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

További információ a továbbított paraméterek a **CallResource.Create** módszer, lásd: [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Ahogy azt korábban említettük, ez a kód Twilio által biztosított hely használatával a TwiML választ küldi vissza. A saját hely segítségével adja meg a TwiML választ helyette. További információkért lásd: [hogyan: Adjon meg TwiML válaszok a saját webhelyén](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Útmutató: az SMS-üzenet küldése
Az alábbi képernyőfelvételen látható, hogyan küldhet az SMS üzenetet használatával a **MessageResource** osztály. A **a** száma az SMS-üzenetek küldéséhez a próba fiókok Twilio biztosítja. A **való** szám ellenőrizni kell a Twilio-fiókja a kód futtatása előtt.

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

## <a id="howto_provide_twiml_responses"></a>Hogyan: Adja meg a saját webhelyén TwiML válaszok
Ha az alkalmazás kezdeményezi keresztül a hívást a Twilio API - például a **CallResource.Create** metódus - Twilio a kérést küld egy TwiML választ várt URL-CÍMÉT. Példa [hogyan: végezhet](#howto_make_call) a Twilio-megadott URL-címet használ [http://twimlets.com/message] [ twimlet_message_url] vissza a válaszban.

> [!NOTE]
> TwiML web Services használatra szolgál, míg a TwiML tekintheti meg a böngészőben. Kattintson például [http://twimlets.com/message] [ twimlet_message_url] egy üres megjelenítéséhez &lt;válasz&gt; elem; másik példaként, kattintson a [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) megtekintéséhez egy &lt;válasz&gt; elem, amely tartalmazza egy &lt;szóbeli&gt; elem.
>
>

Ahelyett, hogy a Twilio által megadott URL-címen, létrehozhat saját URL-cím hely, amely HTTP-válaszokat ad vissza. A hely bármilyen nyelven HTTP-válaszok visszaadó hozhat létre. Ez a témakör azt feltételezi, hogy lesz az általános ASP.NET-kezelő az URL-címet futtató.

A következő ASP.NET kezelő létrehozza TwiML választ, amely szerint **Hello World** hívásakor.

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
    
Ahogy látja, a fenti példa, TwiML a rendszer a választ csak az XML-dokumentum. A Twilio.TwiML függvénytár TwiML hoz létre az Ön osztályokat tartalmazza. Az alábbi példa hozza létre a megfelelő választ, ahogy fent látható, de használja a **VoiceResponse** osztály.

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

TwiML kapcsolatos további információkért lásd: [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Egy módszerre, amellyel TwiML visszajelzést beállítása után az URL-címet átadhatók a **CallResource.Create** metódust. Például egy webes alkalmazás központi telepítése egy Azure felhőszolgáltatást MyTwiML nevű, és az ASP.NET-kezelő nevét mytwiml.ashx, ha az URL-cím átadhatók **CallResource.Create** a következő kód mintában látható módon:

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Azure és az ASP.NET Twilio használatával kapcsolatos további információkért lásd: [telefonhívás Twilio Azure webes szerepkör használatával hogyan][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
