---
title: Az Azure Service Fabric fordított proxydiagnosztikája
description: Ismerje meg, hogyan figyelheti és diagnosztizálhatja a kérelmek feldolgozását egy Azure Service Fabric-alkalmazás fordított proxyjának használatával.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645463"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>A kérelmek feldolgozásának figyelése és diagnosztizálása a fordított proxyn

A Service Fabric 5.7-es kiadásával kezdve fordított proxyesemények gyűjtemények. Az események két csatornán érhetők el, az egyik ben csak hiba események kapcsolatos kérelem feldolgozási hiba a fordított proxy és a második csatorna, amely részletes eseményeket bejegyzéseket mind a sikeres és sikertelen kérelmek.

Tekintse meg [a fordított proxyesemények gyűjtése,](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) amelyek lehetővé teszik az események gyűjtése ezekből a csatornákból a helyi és az Azure Service Fabric-fürtök.

## <a name="troubleshoot-using-diagnostics-logs"></a>Diagnosztikai naplók – problémamegoldás
Íme néhány példa a gyakori hibanaplók értelmezésére, amelyekkel találkozhatunk:

1. A fordított proxy az 504-es (Időszám) válaszállapot-kódot adja vissza.

    Ennek egyik oka az lehet, hogy a szolgáltatás nem válaszolt a kérelem időbeli elállási időszakán belül.
   Az alábbi első esemény naplózza a fordított proxyn kapott kérelem részleteit. 
   A második esemény azt jelzi, hogy a kérés sikertelen volt a szolgáltatásba való továbbítás közben a "belső hiba = ERROR_WINHTTP_TIMEOUT" miatt. 

    A hasznos teher a következőket tartalmazza:

   * **traceId**: Ez a GUID használható az összes esemény korrelációja megfelelő egyetlen kérelmet. Az alábbi két esemény, a traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271,** ami azt jelenti, hogy ugyanahhoz a kéréshez tartoznak.
   * **requestUrl**: Az URL (Reverse proxy URL), amelyre a kérelmet elküldték.
   * **ige**: HTTP ige.
   * **remoteAddress**: A kérést küldő ügyfél címe.
   * **resolvedServiceUrl**: Szolgáltatás végpontURL-címe, amelyre a bejövő kérelem fel lett oldva. 
   * **errorDetails**: További információ a hibáról.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. A fordított proxy a 404-es (nem található) válaszállapot-kódot adja vissza. 
    
    Íme egy példa esemény, ahol fordított proxy 404-et ad vissza, mivel nem találta a megfelelő szolgáltatásvégpontot.
    A hasznos teher bejegyzések kamat itt a következők:
   * **processRequestPhase**: A kérelem feldolgozása során a hiba bekövetkezése során a fázist jelzi, azaz a ***TryGetEndpoint*** a továbbtovábbítandó szolgáltatásvégpont beolvasása közben. 
   * **errorDetails**: A végpontkeresési feltételek listázása. Itt láthatja, hogy a listenerName = **FrontEndListener,** míg a replika végpontlistája csak egy **OldListener**nevű figyelőt tartalmaz.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Egy másik példa, ahol fordított proxy visszaadhat 404 nem található a következő: ApplicationGateway\Http konfigurációs paraméter **SecureOnlyMode** van beállítva, hogy igaz a fordított proxy **figyelése HTTPS,** azonban az összes replika végpontok nem biztonságosak (figyelés http).
     A fordított proxy 404 értéket ad vissza, mivel nem talál végpontot a HTTPS-en a kérelem továbbításához. Az esemény hasznos adatában lévő paraméterek elemzése segít a probléma leszűkítésében:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. A fordított proxyhoz való kérés időkérési hibával sikertelen. 
    Az eseménynaplók tartalmaznak egy eseményt a beérkezett kérelem részleteit (nem jelenik meg itt).
    A következő esemény azt mutatja, hogy a szolgáltatás 404-es állapotkóddal válaszolt, és a fordított proxy újraoldást kezdeményez. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Az összes esemény összegyűjtésekor egy eseménysorozat jelenik meg, amely minden megoldási és továbbítási kísérletet megjelenít.
    A sorozat utolsó eseménye azt mutatja, hogy a kérelem feldolgozása időtúloldalon, valamint a sikeres feloldási kísérletek számával sikertelen volt.
    
    > [!NOTE]
    > Javasoljuk, hogy a részletes csatornaesemény-gyűjtemény alapértelmezés szerint le legyen tiltva, és szükség esetén engedélyezze a hibaelhárítást.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Ha a gyűjtés engedélyezve van a kritikus/hiba események csak, megjelenik egy esemény az időtúloldalon és a megoldási kísérletek számát. 
    
    A 404-es állapotkódot küldeni szándékozó szolgáltatásoknak hozzá kell adniuk egy "X-ServiceFabric" fejlécet a válaszban. Miután a fejlécet hozzáadta a válaszhoz, a fordított proxy továbbítja az állapotkódot az ügyfélnek.  

4. Olyan esetek, amikor az ügyfél leválasztotta a kérelmet.

    A következő esemény akkor kerül rögzítésre, amikor a fordított proxy továbbítja a választ az ügyfélnek, de az ügyfél bontja a kapcsolatot:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. A fordított proxy 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST hibát ad vissza, ha az URI-séma nincs megadva a szolgáltatásvégponthoz a szolgáltatásjegyzékben.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    A probléma megoldásához adja meg az URI-sémát a jegyzékfájlban.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> A websocket-kérelmek feldolgozásával kapcsolatos események jelenleg nincsenek naplózva. Ez a következő kiadásban lesz hozzáadva.

## <a name="next-steps"></a>További lépések
* [Eseményösszesítés és -gyűjtés a Windows Azure Diagnosztika használatával](service-fabric-diagnostics-event-aggregation-wad.md) az Azure-fürtök naplógyűjteményének engedélyezéséhez.
* A Service Fabric-események visual studióban való megtekintéséről a [Helyi figyelés figyelése és diagnosztizálása témakörben](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)található.
* Tekintse meg [a fordított proxy konfigurálása a biztonságos szolgáltatásokhoz való csatlakozáshoz](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) az Azure Resource Manager sablonminták biztonságos proxy konfigurálásához biztonságos fordított proxy a különböző szolgáltatástanúsítvány-ellenőrzési beállításokkal.
* További információért olvassa el a [Service Fabric fordított proxyját.](service-fabric-reverseproxy.md)
