---
title: API-k figyelése az Azure API-kezeléssel, az Event Hubs-szal és a Moesif-fel
titleSuffix: Azure API Management
description: Mintaalkalmazás, amely bemutatja a log-to-eventhub házirendet az Azure API Management, az Azure Event Hubs és a Moesif HTTP-naplózáshoz és figyeléshez való csatlakoztatásával
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 4a0717bf7a284668af4808acae3050cc7f42f836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442526"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Api-k figyelése az Azure API-kezelés, az Event Hubs és a Moesif segítségével
Az [API Management szolgáltatás](api-management-key-concepts.md) számos lehetőséget biztosít a HTTP API-ba küldött HTTP-kérelmek feldolgozásának fokozására. A kérelmek és válaszok létezése azonban átmeneti. A kérelem történik, és az API Management szolgáltatáson keresztül a háttér-API-t. Az API feldolgozza a kérelmet, és a válasz az API-fogyasztóhoz kerül vissza. Az API Management szolgáltatás néhány fontos statisztikát vezet az API-k megjelenítéséhez az Azure Portal irányítópultján, de azon túl, a részletek eltűntek.

Az API Management szolgáltatás ban a log-to-eventhub-szabályzat használatával a kérelemből és a válaszból bármilyen adatot elküldhet egy [Azure Event Hub-nak.](../event-hubs/event-hubs-what-is-event-hubs.md) Számos oka lehet annak, hogy miért érdemes eseményeket generálni az API-knak küldött HTTP-üzenetekből. Néhány példa a frissítések naplózási nyomvonala, a használati elemzések, a kivételriasztás és a külső integrációk.

Ez a cikk bemutatja, hogyan rögzítsék a teljes HTTP-kérelem- és válaszüzenetet, hogyan küldhetik el egy eseményközpontba, majd továbbítsák az üzenetet egy külső szolgáltatásnak, amely HTTP naplózási és figyelési szolgáltatásokat biztosít.

## <a name="why-send-from-api-management-service"></a>Miért érdemes az API Management szolgáltatásból küldeni?
Lehetséges, hogy írjon HTTP middleware, amely csatlakoztatható a HTTP API-keretrendszerek rögzítésére HTTP-kérelmek és válaszok, és betáplálni őket a naplózási és felügyeleti rendszerek. A hátránya ennek a megközelítésnek a HTTP middleware integrálni kell a háttér-API-t, és meg kell egyeznie az API platformjának. Ha több API van, akkor mindegyiknek telepítenie kell a köztes szoftvert. Gyakran vannak oka annak, hogy a háttér-API-k nem frissíthetők.

Az Azure API Management szolgáltatás használatával integrálható a naplózási infrastruktúrával, központosított és platformfüggetlen megoldást biztosít. Emellett méretezhető, részben az Azure API [Management georeplikációs](api-management-howto-deploy-multi-region.md) képességei nek köszönhetően.

## <a name="why-send-to-an-azure-event-hub"></a>Miért érdemes az Azure Event Hubra küldeni?
Ésszerű megkérdezni, hogy miért hozzon létre egy szabályzatot, amely kifejezetten az Azure Event Hubs? Sok különböző helyen, ahol érdemes naplózni a kéréseket. Miért nem küldi el a kéréseket közvetlenül a végső célállomásra?  Ez is egy lehetőség. Azonban egy API-felügyeleti szolgáltatás naplózási kérelmek teljesítésekor figyelembe kell venni, hogy a naplózási üzenetek hogyan befolyásolják az API teljesítményét. A terhelés fokozatos növekedése a rendszerösszetevők rendelkezésre álló példányainak növelésével vagy a georeplikáció kihasználásával kezelhető. A forgalom rövid kiugrásai azonban a kérelmek késleltetését okozhatják, ha az infrastruktúra naplózására irányuló kérelmek terhelés alatt lelassulnak.

Az Azure Event Hubs úgy van kialakítva, hogy hatalmas mennyiségű adatot küld, és sokkal több eseményt képes kezelni, mint a legtöbb API-folyamat HTTP-kérések száma. Az Event Hub egyfajta kifinomult pufferként működik az API-felügyeleti szolgáltatás és az üzeneteket tároló és feldolgozó infrastruktúra között. Ez biztosítja, hogy az API teljesítménye nem szenved a naplózási infrastruktúra miatt.

Miután az adatok átlettek adva egy Eseményközpontnak, megmarad, és megvárja, amíg az Event Hub-felhasználók feldolgozzák azokat. Az Event Hub nem törődik a feldolgozás módjával, csak az üzenet sikeres kézbesítésének biztosítása.

Az Event Hubs képes eseményeket streamelni több fogyasztói csoportba. Ez lehetővé teszi, hogy az eseményeket különböző rendszerek dolgozzák fel. Ez lehetővé teszi számos integrációs forgatókönyvek támogatása anélkül, hogy az API-kérelem feldolgozásának az API Management szolgáltatáson belül, mivel csak egy eseményt kell létrehozni.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Alkalmazás/http üzenetek küldésére vonatkozó házirend
Az Event Hub az eseményadatokat egyszerű karakterláncként fogadja el. A madzag tartalma csak önön múlik. Ahhoz, hogy http-kérelmet csomagolhat, és elküldhesse az Event Hubs-nak, a karakterláncot a kérés- vagy válaszadatokkal kell formáznia. Az ilyen helyzetekben, ha van egy meglévő formátumot tudjuk újra, akkor lehet, hogy nem kell írni a saját elemzési kódot. Kezdetben úgy vélte, a [HAR](http://www.softwareishard.com/blog/har-12-spec/) küldésére HTTP kérések és válaszok. Ez a formátum azonban úgy van optimalizálva, hogy A HTTP-kérések sorozatát JSON-alapú formátumban tárolja. Számos kötelező elemet tartalmazott, amelyek szükségtelen bonyolultságot adtak hozzá a HTTP-üzenet vezetékes továbbításának forgatókönyvéhez.

Egy másik lehetőség az `application/http` volt, hogy az adathordozó-típust az [RFC 7230](https://tools.ietf.org/html/rfc7230)HTTP-specifikációban leírtak szerint használják. Ez az adathordozó-típus pontosan ugyanazt a formátumot használja, mint amely et használnak a HTTP-üzenetek hálózaton keresztüli tényleges küldéséhez, de a teljes üzenet egy másik HTTP-kérelem törzsében helyezhető el. A mi esetünkben csak a testet fogjuk használni üzenetként az Event Hubs-nak. Kényelmesen létezik egy elemző a [Microsoft ASP.NET Web API 2.2 ügyféltárakban,](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) amelyek elemzik `HttpRequestMessage` `HttpResponseMessage` ezt a formátumot, és natív vá és objektumokká alakítják.

Ahhoz, hogy ezt az üzenetet létre tudja hozni, ki kell használnunk a C# alapú [szabályzatkifejezések et](/azure/api-management/api-management-policy-expressions) az Azure API Managementben. Itt van a szabályzat, amely http-kérelem üzenetet küld az Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Politikai nyilatkozat
Van néhány különleges dolog, amit érdemes megemlíteni erről a politikai kifejezésről. A log-to-eventhub házirend rendelkezik egy attribútum nevű logger-id, amely az API Management szolgáltatáson belül létrehozott naplózó nevét. Az Event Hub-naplózó API-felügyeleti szolgáltatásban való beállításának részletei a dokumentumban [találhatók: Események naplózása az Azure API-kezelés ben.](api-management-howto-log-event-hubs.md) A második attribútum egy nem kötelező paraméter, amely utasítja az Event Hubs, amely partíciótárolja az üzenetet. Az Event Hubs partíciókat használ a méretezhetőség engedélyezéséhez, és legalább két szükséges. Az üzenetek megrendelt kézbesítése csak egy partíción belül garantált. Ha nem utasítja az Event Hub, amely partíciót az üzenet elhelyezéséhez, ciklikus multiplexelési algoritmust használ a terhelés elosztásához. Ez azonban azt eredményezheti, hogy egyes üzeneteinket nem sorrendben dolgozzák fel.

### <a name="partitions"></a>Partíciók
Annak érdekében, hogy üzeneteinket a fogyasztók nak a partíciók terheléselosztási képességének megfelelően kézbesítsék, és kihasználhassam a partíciók terheléselosztási képességét, úgy döntöttem, hogy HTTP-kérésüzeneteket küldök egy partícióra, és HTTP válaszüzeneteket egy második partícióra. Ez biztosítja az egyenletes terheléselosztást, és garantáljuk, hogy minden kérés sorrendben kerül felhasználásra, és minden válasz sorrendben kerül felhasználásra. Lehetséges, hogy a választ a megfelelő kérelem előtt kell felhasználni, de mivel ez nem jelent problémát, mivel más mechanizmusunk van a kérelmek és a válaszok összevetésére, és tudjuk, hogy a kérelmek mindig a válaszok előtt érkeznek.

### <a name="http-payloads"></a>HTTP-hasznos teher
A létrehozása `requestLine`után a , ellenőrizzük, hogy a kérelem törzse kell csonkolni. A kérelem törzse csak 1024-re van csonkolva. Ez növelhető, azonban az egyes Event Hub-üzenetek legfeljebb 256 KB, így valószínű, hogy egyes HTTP-üzenettörzsek nem fér el egyetlen üzenetben. Naplózás és elemzés során jelentős mennyiségű információ származtatható csak a HTTP-kérelem sorából és fejléceiből. Emellett sok API-k csak kis testeket adnak vissza, így a nagy testek csonkolásával az információérték elvesztése meglehetősen minimális az átviteli, feldolgozási és tárolási költségek csökkenéséhez képest, hogy az összes testtartalmat megtartsa. Egy utolsó megjegyzés a szervezet feldolgozásáról `true` az, `As<string>()` hogy át kell adnunk a módszert, mert a test tartalmát olvassuk, de azt is akarta, hogy a háttér API képes legyen olvasni a testet. Azáltal, hogy igaz, hogy ezt a módszert, azt eredményezjük, hogy a szervezet pufferelt, hogy el lehet olvasni a második alkalommal. Ez fontos, hogy tudatában legyünk, ha van egy API, amely nem nagy fájlok feltöltése, vagy használja a hosszú lekérdezést. Ezekben az esetekben, az lenne a legjobb, hogy ne olvassa el a test egyáltalán.

### <a name="http-headers"></a>HTTP-fejlécek
A HTTP-fejlécek egyszerű kulcs/értékpár formátumban vihetők át az üzenetformátumba. Úgy döntöttünk, hogy bizonyos biztonsági szempontból érzékeny mezőket kivonunk, hogy elkerüljük a hitelesítő adatok szükségtelen kiszivárogtatását. Nem valószínű, hogy az API-kulcsok at és más hitelesítő adatokat elemzési célokra használná. Ha azt szeretnénk, hogy nem elemzést a felhasználó és az adott `context` termék általuk használt, akkor lehet kapni, hogy az objektum, és adjunk hozzá, hogy az üzenetet.

### <a name="message-metadata"></a>Üzenet metaadatai
Az eseményközpontba küldő teljes üzenet létrehozásakor az első sor `application/http` valójában nem része az üzenetnek. Az első sor további metaadatok, amelyek abból állnak, hogy az üzenet kérés vagy válaszüzenet, valamint egy üzenetazonosító, amely a kérelmek válaszokkal való korrelációjára szolgál. Az üzenetazonosító egy másik, a következőképpen megjelenő házirend del jön létre:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Létrehozhattuk volna a kérési üzenetet, tárolhattuk volna egy változóban, amíg a válasz vissza nem érkezett, majd egyetlen üzenetben elküldtük a kérést és a választ. Azonban a kérés és a válasz egymástól függetlenül történő elküldésével és egy üzenetazonosító használatával a kettő korrelációjára, egy kicsit nagyobb rugalmasságot kapunk az üzenet méretében, a több partíció előnyeinek kihasználására, miközben fenntartjuk az üzenet sorrendet, és a kérés megjelenik a mi naplózási műszerfal hamarabb. Előfordulhatnak olyan esetek is, amikor egy érvényes válasz soha nem küldi el az eseményközpontba, valószínűleg az API Management szolgáltatás végzetes kérési hibája miatt, de továbbra is rendelkezünk a kérelem rekordjával.

A válasz HTTP-üzenetének elküldésére vonatkozó házirend hasonlónak tűnik a kéréshez, így a teljes házirend-konfiguráció így néz ki:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

A `set-variable` házirend olyan értéket hoz létre, amely `<inbound>` a `<outbound>` szakaszban lévő `log-to-eventhub` házirend és a szakasz által is elérhető.

## <a name="receiving-events-from-event-hubs"></a>Események fogadása az Eseményközpontokból
Az Azure Event Hub eseményei az [AMQP protokoll](https://www.amqp.org/)használatával érkeznek. A Microsoft Service Bus csapata elérhetővé tette az ügyfélkönyvtárakat a felhasználói események megkönnyítése érdekében. Két különböző megközelítések támogatott, az egyik az, hogy a `EventProcessorHost` *közvetlen fogyasztó,* a másik pedig használja az osztályt. E két megközelítés például az [Eseményközpontok programozási útmutatójában található.](../event-hubs/event-hubs-programming-guide.md) A rövid változata a `Direct Consumer` különbségek, ad `EventProcessorHost` teljes ellenőrzést, és nem néhány vízvezeték munka az Ön számára, de teszi bizonyos feltételezéseket arról, hogyan dolgozza fel ezeket az eseményeket.

### <a name="eventprocessorhost"></a>EventProcessorHost
Ebben a példában `EventProcessorHost` az egyszerűség kedvéért használjuk, de nem biztos, hogy ez a legjobb választás erre a konkrét forgatókönyvre. `EventProcessorHost`nem a kemény munka, hogy megbizonyosodjon arról, hogy nem kell aggódnia threading kérdések egy adott esemény processzor osztály. A mi forgatókönyvünkben azonban egyszerűen konvertáljuk az üzenetet egy másik formátumra, és egy másik szolgáltatásnak adjuk át egy aszinkron módszerrel. Nincs szükség a megosztott állapot frissítésére, és ezért nem áll fenn a szálkal kapcsolatos problémák kockázata. A legtöbb forgatókönyv, talán a legjobb választás, `EventProcessorHost` és ez minden bizonnyal a könnyebb megoldás.

### <a name="ieventprocessor"></a>IEventProcessor
A központi fogalom `EventProcessorHost` használata során az `IEventProcessor` interfész implementációjának `ProcessEventAsync`létrehozása, amely tartalmazza a módszert . Ennek a módszernek a lényege itt látható:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Az EventData objektumok listáját a rendszer átad a metódusnak, és átteroljuk a listát. Az egyes metódusok bájtjai egy HttpMessage-objektumba kerülnek, és az objektum átkerül az IHttpMessageProcessor egy példányába.

### <a name="httpmessage"></a>HttpMessage
A `HttpMessage` példány három adatot tartalmaz:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

A `HttpMessage` példány `MessageId` tartalmaz egy GUID, amely lehetővé teszi számunkra, hogy csatlakoztassa a HTTP-kérelmet a megfelelő HTTP-válasz és a logikai érték, amely azonosítja, ha az objektum tartalmaz egy példányt a HttpRequestMessage és HttpResponseMessage. A beépített HTTP-osztályok használatával `System.Net.Http`a alkalmazásban ki tudtam `application/http` használni a rendszerben `System.Net.Http.Formatting`található elemzési kódot.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcesszor
A `HttpMessage` példány ezután továbbítja `IHttpMessageProcessor`a végrehajtását, amely egy felület, amit azért hoztam létre, hogy leválasztsa az esemény fogadását és értelmezését az Azure Event Hubés a tényleges feldolgozása.

## <a name="forwarding-the-http-message"></a>A HTTP-üzenet továbbítása
Ehhez a mintához úgy döntöttem, hogy érdekes lenne a HTTP-kérést átnyomni a [Moesif API Analytics-be.](https://www.moesif.com) A Moesif egy felhőalapú szolgáltatás, amely http-elemzésre és hibakeresésre specializálódott. Ingyenes szinttel rendelkeznek, így könnyen megpróbálhatjuk, és lehetővé teszi számunkra, hogy valós időben lássuk a HTTP-kérelmeket az API Management szolgáltatásunkon keresztül.

A `IHttpMessageProcessor` megvalósítás így néz ki:

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

A `MoesifHttpMessageProcessor` kihasználja a [C# API-kódtár Moesif,](https://www.moesif.com/docs/api?csharp#events) amely megkönnyíti a HTTP-eseményadatok leküldése a szolgáltatásba. Ahhoz, hogy HTTP-adatokat küldjön a Moesif Collector API-nak, szüksége van egy fiókra és egy alkalmazásazonosítóra. Kapsz egy Moesif alkalmazásazonosító t, ha létrehoz egy fiókot [a Moesif webhelyén,](https://www.moesif.com) majd lépjen a _jobb felső menü_ -> _alkalmazásbeállítása ._

## <a name="complete-sample"></a>Teljes minta
A [minta forráskódja](https://github.com/dgilling/ApimEventProcessor) és tesztjei a GitHubon találhatók. A minta futtatásához szüksége van egy [API-felügyeleti szolgáltatásra,](get-started-create-service-instance.md) [egy csatlakoztatott eseményközpontra](api-management-howto-log-event-hubs.md)és egy [tárfiókra.](../storage/common/storage-create-storage-account.md)   

A minta csak egy egyszerű konzolalkalmazás, amely figyeli az Event Hubról `EventRequestModel` érkező `EventResponseModel` eseményeket, moesifté és objektumokká alakítja őket, majd továbbítja őket a Moesif Collector API-ra.

A következő animált képen láthatja a fejlesztői portálon egy API-ra irányuló kérelmet, a konzolalkalmazást, amely az üzenetet fogad, dolgozza fel és továbbítja, majd az eseménystreamben megjelenik a kérés és a válasz.

![A Runscope-nak továbbítandó kérelem bemutatása](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Összefoglalás
Az Azure API Management szolgáltatás ideális hely az API-kba érkező és onnan érkező HTTP-forgalom rögzítésére. Az Azure Event Hubs egy jól méretezhető, alacsony költségű megoldás a forgalom rögzítésére és másodlagos feldolgozó rendszerekbe való betáplálására naplózás, figyelés és más kifinomult elemzések esetén. A moesifhez hasonló külső forgalomfigyelő rendszerekhez való csatlakozás olyan egyszerű, mint néhány tucat sornyi kód.

## <a name="next-steps"></a>További lépések
* További információ az Azure Event Hubs-ról
  * [Ismerkedés az Azure Event Hubs-szal](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost szolgáltatással](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ az API-kezelés és az Eseményközpontok integrációjáról
  * [Események naplózása az Azure Event Hubs szolgáltatásba az Azure API Management szolgáltatásban](api-management-howto-log-event-hubs.md)
  * [Naplózó entitás hivatkozása](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub házirend-hivatkozás](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
