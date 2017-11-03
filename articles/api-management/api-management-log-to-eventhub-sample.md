---
title: "Az Azure API Management, az Event Hubs és Runscope API-k figyelése |} Microsoft Docs"
description: "A mintaalkalmazás, amely tartalmazza a kapcsolódó Azure API Management, az Azure Event Hubs és a HTTP-naplózás és figyelés Runscope napló eventhub házirend"
services: api-management
documentationcenter: 
author: darrelmiller
manager: erikre
editor: 
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 70ee752c5639c90f77dde104ce85eec0a1062300
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Az API-kat az Azure API Management, az Event Hubs és Runscope figyelése
A [API-kezelés szolgáltatás](api-management-key-concepts.md) javítása érdekében a HTTP API küldött HTTP-kérelmek feldolgozási sok képességeket biztosít. Azonban a kérések és válaszok átmeneti jellegűek. A kérelem, és azt a háttér-API számára az API Management szolgáltatáson keresztül zajlik. Az API-feldolgozza a kérést, és választ áthaladó vissza az API-fogyasztó számára. Az API Management szolgáltatás tartja néhány fontos statisztikai adat kapcsolatos való megjelenítéshez. az API-k Publisher portál irányítópultján, de túl eltűnnek róla, hogy a részletek.

Használatával a [napló eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [házirend](api-management-howto-policies.md) az API Management szolgáltatásban küldhet minden adatát a kérelem és válasz egy [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). Nincsenek számos okból miért érdemes lehet események generálása küldi el az API-kat a HTTP-üzenetek. Például frissítések, a használatelemzés, a kivétel riasztások és a 3. fél integrációja napló.   

Ez a cikk bemutatja, hogyan rögzítése a teljes HTTP kérelem-válasz üzenet. elküldi a Eseményközpontban, majd továbbítják a harmadik fél-szolgáltatás, amely HTTP-naplózás és -szolgáltatások figyelésének üzenetet.

## <a name="why-send-from-api-management-service"></a>Miért küldése az API Management szolgáltatást?
Akkor lehet, amely HTTP-kérések és válaszok, majd azokat a naplózás és figyelés rendszerek hírcsatorna HTTP API keretrendszerek csatlakoztatható HTTP köztes írni. A hátránya, hogy ezt a módszert használja a HTTP köztes a háttér-API integrálni kell, és meg kell egyeznie a platform API. Ha több API-k minden egyes a köztes kell telepíteni. Gyakran oka miért háttér API-k nem lehet frissíteni.

Naplózási infrastruktúra integrálása az Azure API Management szolgáltatással központosított és platformfüggetlen megoldást kínál. Célszerű is méretezhető, részben oka az, hogy a [georeplikáció](api-management-howto-deploy-multi-region.md) Azure API-felügyeleti képességeit.

## <a name="why-send-to-an-azure-event-hub"></a>Miért küldeni az Azure Event Hubs?
Kérje meg, miért a szabályzatban csak az Azure Event Hubs egy ésszerű? Előfordulhat, hogy kívánt bejelentkezni a saját kérések különböző helyeken van. Miért nem csupán a kérelmeket küldeni közvetlenül a végső rendeltetési?  Ez a beállítás. Azonban az API management szolgáltatás naplózási kérelmeinek meghozásakor fontos figyelembe venni, hogyan befolyásolják naplózási üzeneteket az API teljesítményét. Fokozatos nő a terhelés rendszerösszetevő elérhető példányok növelésével vagy georeplikáció kihasználásával lehet kezelni. Azonban a forgalom rövid igényeiben jelentkező okozhat kérelmek jelentősen később, ha lassú terhelés alatt naplózási infrastruktúra kérelmek indul el.

Az Azure Event Hubs célja, hogy hatalmas mennyiségű érkező adatokat, az események sokkal nagyobb számú foglalkoznak, mint a HTTP-kérések száma legtöbb API-k folyamat kapacitással rendelkező átjáróeszközt. Az Event Hubs kifinomult a API management és az infrastruktúra, amely fogja tárolni, és az üzenetek feldolgozásához között egy közvetítő funkcionál. Ez biztosítja, hogy a a API teljesítménye nem érinti a naplózás infrastruktúra miatt.  

Ha az adatokat adtak át az Eseményközpontok tárolva, és megvárja, hogy az Event Hubs fogyasztók feldolgozni azt. Az Event Hubs nem ügyeljen arra, hogyan fogja feldolgozni, akkor csak ügyel meggyőződött arról, hogy az üzenet sikeresen kézbesíti a rendszer.     

Az Event Hubs képesek az adatfolyam események több felhasználói csoporthoz. Ez lehetővé teszi, hogy teljesen más rendszerek által feldolgozandó események. Ez lehetővé teszi több integrációs forgatókönyv támogatása nem helyezi hozzáadása késlelteti az API Management szolgáltatáson belül az API-kérés feldolgozása csak egy eseménynapló igények generálását.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Alkalmazás/HTTP-üzenetek küldése egy házirend
Az Eseményközpontok eseményadatok egyszerű karakterláncként fogad el. A karakterláncokat tartalma teljes mértékben Öntől függ. HTTP-kérelem becsomagolhatja, és küldése az Event Hubs kell a formázó karakterlánc, a kérés vagy válasz adatokkal. Olyan esetekben, például egy meglévő formátum esetén újrafelhasználásához, majd saját elemzése kód írása előfordulhat, hogy nincs. Kezdetben szeretnék venni használatával a [HAR](http://www.softwareishard.com/blog/har-12-spec/) HTTP-kérések és válaszok küldéséhez. Azonban ez a formátum tárolására van optimalizálva HTTP-kérelmek sorozatát alapú JSON formátumban. A forgatókönyvet a HTTP-üzenet átadja a hálózaton keresztül a szükségtelen összetettsége hozzáadott kötelező elemek számos tartalmazott.  

Alternatív lehetőségként volt, hogy a `application/http` médiatípus lásd: a HTTP-specifikáció [RFC 7230](http://tools.ietf.org/html/rfc7230). A médiatípus pontos ugyanazt a formátumot használja, ténylegesen a hálózaton keresztül a HTTP-üzenetek küldéséhez használt, de a teljes üzenet egy másik HTTP-kérelem törzse be lehet. A mi esetünkben csak fogjuk a szervezet használja az üzenet küldése az Event Hubs. Már szerepel egy elemző van kényelmesen, [Microsoft ASP.NET Web API 2.2 ügyfél](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) tárak, amelyek elemezni ezt a formátumot, majd átalakíthatja a natív `HttpRequestMessage` és `HttpResponseMessage` objektumok.

Ez az üzenet előnyeit C#-alapú kell létrehozni a [házirend-kifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx) az Azure API Management. Ez a házirendet, amely HTTP-kérelem üzenetet küld az Azure Event Hubs.

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

### <a name="policy-declaration"></a>Házirend nyilatkozat
Nincs adott szempontokat érdemes megemlíteni kapcsolatban a házirend-kifejezést. A napló eventhub házirendben engedélyezve van a naplózó-azonosítója, amely az API Management szolgáltatáson belül lett létrehozva naplózó nevére hivatkozik nevű attribútum. A dokumentum részletesen ismerteti az API Management szolgáltatásban egy Eseményközpontba naplózó beállítása található [hogyan naplózza az eseményeket az Azure Event Hubs az Azure API Management](api-management-howto-log-event-hubs.md). A második attribútumot nem kötelező paraméter, amely arra utasítja az Event Hubs, amely az üzenet tárolására partícióazonosító. Az Event Hubs használjon partíciókat scalabilty engedélyezése, és legalább két kérheti. Az üzenetek a rendezett kézbesítést csak garantáltan a partíción belül. A Microsoft arra utasítani az Event Hubs mely partíció helyezhető el az üzenetet, ha a terhelés elosztása a ciklikus multiplexelési algoritmus fogja használni. Azonban a nem megfelelő sorrendben feldolgozott üzenetek némelyike esetleg okozó.  

### <a name="partitions"></a>Partíciók
Az üzenetek fogyasztóknak sorrendben érkeznek, valamint partíciók terhelés terjesztési képességének kihasználása érdekében elfogadása HTTP-kérelem üzenetek küldése egy partíciót, és a HTTP-válasz üzenetek egy másik partícióra. Ezzel biztosíthatja, hogy még akkor is, egy terheléselosztási és garantáljuk, hogy minden kérésnél fognak használni ahhoz, és minden válasz fognak használni ahhoz. Lehetséges, hogy a vonatkozó kérés előtt fel választ, de, amely nincs probléma van a Microsoft által küldött válaszokhoz kérelmeket egy másik mechanizmus használatával történik, és tudjuk, hogy kérelmek mindig előznie válaszokat.

### <a name="http-payloads"></a>HTTP hasznos adat található
Létrehozása után a `requestLine` azt ellenőrzi, hogy ha a kérés törzsében csonkolva lesz. A kérelem törzsében csak 1024 méretűre csonkolja. Ez növekedhet, de egyes Eseményközpont üzenetek legfeljebb 256KB, akkor valószínű, hogy néhány HTTP-üzenet szervek lesz nem elférjen egyetlen üzenetben. Naplózás és az elemzés során jelentős mennyiségű információt származtatható csak a HTTP-kérelem sor és a fejlécet. Is sok API-kérelmek csak térjen vissza a kisebb szervezetek és nagy szervezetek betartására értékeinek megszűnését viszonylag minimális tartani minden szervezet tartalma átviteli, feldolgozása és tárolási költségek csökkentése szemben. Egy végső Megjegyzés a törzsének feldolgozására a rendszer, hogy át kell `true` való az As<string>() metódus, mert azt azért olvassák el a szervezet tartalmát, de lett is szeretné, hogy a háttér-API el tudják olvasni a szervezet számára. Ez a módszer az IGAZ értéket történő azt miatt a szervezet számára, hogy azt még egyszer olvasható pufferbe kerüljön. Ez azért fontos tudni, ha az API-k, amelyen a nagyon nagy méretű fájlok feltöltése vagy hosszú lekérdezési használja. Ezekben az esetekben a szervezet minden olvasási elkerülése érdekében ajánlott lenne.   

### <a name="http-headers"></a>HTTP-fejlécek
HTTP-fejléceket is egyszerűen továbbítja egyszerű kulcs/érték pár formátumban üzenet formátumra alakítja. Azt választotta ki feleslegesen a hitelesítő adatok megakadályozására elkerülése érdekében bizonyos biztonsági időérzékeny mezőinek. Nem valószínű, hogy API-kulcsokat és más hitelesítő adatokat használni elemzés céljából. Ha azt szeretné a felhasználó és az adott termék elemzést használják, akkor azt, hogy a lehetett beolvasni a `context` objektumra, és adja hozzá, amely az üzenetet.     

### <a name="message-metadata"></a>Üzenet metaadatok
A teljes üzenetet küldeni az eseményközpont létrehozásakor az első sor nincs ténylegesen része a `application/http` üzenet. Az első sorban álló-e az üzenetet a kérelem vagy válaszüzenetet és egy üzenetazonosítója által küldött válaszokhoz kérelmek összefüggéseket szolgáló metaadatokat. Az üzenetazonosító létrejön egy másik-szabályzattal, amely a következőképpen néz ki:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Azt a kérelemüzenet létrehozott, egy változó tárolja, amíg a válaszban visszaadott és küldi el egyszerűen a rendszer a kérelem-válasz egy üzenet, sikerült. Azonban a kérés- és egymástól függetlenül küldésével, és a két összefüggéseket üzenetet azonosító használatával, azt lekérése egy kicsit nagyobb rugalmasságot biztosít az üzenet mérete, a képes több partíció, miközben megtartja üzenet rendelés és a kérelem jelennek meg a naplózási irányítópult hamarabb kihasználásához. Előfordulhat, hogy is van bizonyos esetekben, ahol egy érvényes válasz nem érkezik az event hubs, valószínűleg egy kérelem végzetes hiba történt az API Management szolgáltatásban, de azt továbbra is rendelkeznek egy bejegyzést a kérelem.

A HTTP válaszüzenetet küldeni a házirend nagyon hasonlít-e a kérelmet, és így a teljes házirend-konfiguráció néz ki:

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

A `set-variable` házirend által egyaránt elérhető értéket hoz létre a `log-to-eventhub` a házirend a `<inbound>` szakasz és a `<outbound>` szakasz.  

## <a name="receiving-events-from-event-hubs"></a>Az események fogadását az Event Hubs
Azure Event Hubs eseményeit fogadásának használatával a [AMQP protokoll](http://www.amqp.org/). A Microsoft Service Bus csapatával végzett ügyfél megkönnyítése érdekében a fogyasztó események elérhető szalagtárak. Támogatott két különböző megközelítés, egy folyamatban van egy *közvetlen fogyasztói* és egyéb-t használ a `EventProcessorHost` osztály. E két megközelítés példái megtalálhatók a [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md). A különbségek rövid verziószáma, `Direct Consumer` lehetővé teszi az irányítást és a `EventProcessorHost` nem egy része a bekötése, az azonban lehetővé teszi bizonyos feltételezéseket hogyan feldolgozza ezeket az eseményeket.  

### <a name="eventprocessorhost"></a>EventProcessorHost
A példában használjuk a `EventProcessorHost` az egyszerűség kedvéért azonban akkor lehetséges, hogy ehhez a forgatókönyvhöz nem a legjobb választás. `EventProcessorHost`nem meggyőződött arról, hogy nem kell foglalkoznia az egy adott esemény processzor osztályon belül problémák szálkezelési rögzített munkáját. Azonban a mi esetünkben azt egyszerűen az üzenet konvertálása más formátumra és átadja mentén azt egy másik szolgáltatást egy aszinkron metódus használatával. Nincs szükség megosztott állapotot, és ezért kockázata problémák szálkezelési frissítéséhez. A legtöbb esetben `EventProcessorHost` valószínűleg a legjobb választás, és biztosan a könnyebb beállítás.     

### <a name="ieventprocessor"></a>IEventProcessor
A központi koncepció használatakor `EventProcessorHost` létrehozása egy megvalósítása a `IEventProcessor` illesztőfelületet, amely tartalmazza a módszert `ProcessEventAsync`. Ez a módszer lényege itt jelenik meg:

```c#
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

A metódusnak átadott EventData objektumok listáját, és azt, hogy a lista ismétlés. Az egyes módszerek bájt elemzésének HttpMessage objektumba és, hogy az objektum egy példányát IHttpMessageProcessor objektumnak átadott.

### <a name="httpmessage"></a>HttpMessage
A `HttpMessage` példány három adatra adatokat tartalmazza:

```c#
public class HttpMessage
{
   public Guid MessageId { get; set; }
   public bool IsRequest { get; set; }
   public HttpRequestMessage HttpRequestMessage { get; set; }
   public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

A `HttpMessage` példány tartalmaz egy `MessageId` GUID, amely lehetővé teszi a HTTP-kérelem csatlakozhatnak a megfelelő HTTP-válasz és egy logikai érték, amely azt jelzi, hogy az objektum tartalmaz egy HttpRequestMessage és HttpResponseMessage példánya. A HTTP-osztályok a beépített használatával `System.Net.Http`, sikerült előnyeit a `application/http` található kód elemzése `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
A `HttpMessage` példány továbbítja végrehajtásának `IHttpMessageProcessor` Ez az illesztőfelület szolgáló használata leválasztja a fogadás és az Azure Event Hubs eseményben értelmezésének és a tényleges feldolgozását.

## <a name="forwarding-the-http-message"></a>A HTTP üzenet továbbítása
Ez a minta az I lezárását lenne érdekes, amelyekkel a HTTP-kérelem keresztül a [Runscope](http://www.runscope.com). Runscope egy felhőalapú szolgáltatás, amely HTTP-hibakeresés, a naplózás és figyelés. Ingyenes szint, így könnyen próbálja, és lehetővé teszi a megállapítását, hogy a valós idejű haladnak keresztül az API-kezelés szolgáltatás a HTTP-kérelmek rendelkeznek.

A `IHttpMessageProcessor` megvalósítási néz ki,

```c#
public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
{
   private HttpClient _HttpClient;
   private ILogger _Logger;
   private string _BucketKey;
   public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
   {
       _HttpClient = httpClient;
       var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
       _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
       _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
       _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
       _Logger = logger;
   }

   public async Task ProcessHttpMessage(HttpMessage message)
   {
       var runscopeMessage = new RunscopeMessage()
       {
           UniqueIdentifier = message.MessageId
       };

       if (message.IsRequest)
       {
           _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
           runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
       }
       else
       {
           _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
           runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
       }

       var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
       messagesLink.BucketKey = _BucketKey;
       messagesLink.RunscopeMessage = runscopeMessage;
       var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
       _Logger.LogDebug("Request sent to Runscope");
   }
}
```

Sikerült kihasználása egy [meglévő ügyféloldali kódtára a Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) , amelyek segítségével könnyebben leküldéses `HttpRequestMessage` és `HttpResponseMessage` példányok be a szolgáltatásba. A Runscope API eléréséhez szüksége lesz a fiók és API-kulcs. API-kulcs beolvasása vonatkozó utasítások megtalálhatók a [hozzáférés Runscope API-alkalmazások létrehozása](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) képernyőfelvétel.

## <a name="complete-sample"></a>Teljes mintát
A [forráskód](https://github.com/darrelmiller/ApimEventProcessor) és meglétének ellenőrzése a mintát a Githubon. Szüksége lesz egy [API Management szolgáltatás](api-management-get-started.md), [a csatlakoztatott Eseményközpontot](api-management-howto-log-event-hubs.md), és egy [Tárfiók](../storage/common/storage-create-storage-account.md) a minta futtatásához a szolgáltatást.   

A minta értéke csak egy egyszerű konzolalkalmazást, amely figyeli az Event Hubs származó események összefogásával alakítja őket egy `HttpRequestMessage` és `HttpResponseMessage` objektumokat, és ezután továbbítja őket a Runscope API be.

A következő animált kép tekintheti meg a kérelem egy API-t a fejlesztői portálra, az üzenet érkezett, feldolgozott és továbbított megjelenítő Konzolalkalmazás majd a kérelem és válasz jelenik meg a Runscope forgalom inspector történik.

![A kérelem Runscope lesznek továbbítva bemutatója](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Összefoglalás
Az Azure API Management szolgáltatás Itt adható meg az ideális ül, illetve onnan az API-kat a HTTP-forgalom rögzítésére. Azure Event Hubs egy kiválóan méretezhető, alacsony költségű megoldás, hogy forgalom rögzítése és elágazó azt másodlagos feldolgozási rendszerek naplózási, figyelés, és más kifinomult analytics. 3. fél forgalom a like Runscope egy egyszerű, néhány dozen sornyi kód rendszerek figyelése csatlakozik.

## <a name="next-steps"></a>Következő lépések
* További tudnivalók az Azure Event Hubs
  * [Ismerkedés az Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További tudnivalók az API Management és az Event Hubs-integráció
  * [Hogyan naplózza az eseményeket az Azure Event Hubs az Azure API Management](api-management-howto-log-event-hubs.md)
  * [Naplózó entitáshivatkozás](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [napló-eventhub szabályzatainak ismertetése](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
