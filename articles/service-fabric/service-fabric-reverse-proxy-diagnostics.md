---
title: Fordítottproxy-diagnosztika az Azure Service Fabric |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti és diagnosztizálhatja a fordított proxy, a kérelem feldolgozása.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: c9c8c649208cff95f4ee515d39cc8cca3e2c64bf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121485"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Figyelheti és diagnosztizálhatja a kérelem feldolgozását, a fordított proxy

A Service Fabric 5.7 kiadásával kezdődően, fordított proxy események érhetők el a gyűjteményhez. Az események két csatornákon, a kérelem feldolgozása sikertelen, a fordított proxy és a részletes események és a sikeres és a sikertelen kéréseket tartalmazó második csatorna kapcsolatos hibaeseményeket csak egy érhető el.

Tekintse meg [fordított proxy eseményeinek gyűjtése](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) ahhoz, hogy ezek a csatornák a helyi és Azure Service Fabric-fürtök események gyűjtését.

## <a name="troubleshoot-using-diagnostics-logs"></a>Hibaelhárítás a diagnosztikai naplók használatával
Szívesen adunk néhány ötletet a merülhetnek fel az egyik gyakori hiba naplók értelmezése:

1. Fordított proxy válasz állapotkód: 504 (időtúllépés) adja vissza.

    Egyik oka a szolgáltatás nem a kérés megadott időn belül érkezik válasz okozhatja.
   Az alábbi naplók első esemény a kérelem részleteit a fordított proxy kézbesítve. 
   A második esemény azt jelzi, hogy a kérelem nem sikerült Service, továbbítás közben: "belső hiba = ERROR_WINHTTP_TIMEOUT" 

    A hasznos adatokat tartalmazza:

   * **traceId**: A GUID korrelációját, ha egy kérelemhez tartozó összes esemény használható. Az az alább két események, a traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, ami azt jelenti, az azonos kéréshez tartoznak.
   * **requestUrl**: Az URL-címe (fordított proxy URL-cím), amelyhez a kérés érkezett.
   * **verb**: HTTP-műveletet.
   * **remoteAddress**: A kérést küldő ügyfél címe.
   * **resolvedServiceUrl**: Végpont URL-címe, amelyhez a bejövő kérelem lett megoldva. 
   * **errorDetails**: A hibával kapcsolatos további információkat.

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

2. Fordított proxy válasz állapotkódja 404 (nem található) adja vissza. 
    
    Íme egy példa esemény, amelyeken fordított proxy visszaadja-e 404-es óta nem sikerült az egyező-szolgáltatásvégpont megkeresése.
    A mondanivalóját adattartalom-bejegyzések a következők:
   * **processRequestPhase**: Azt jelzi, hogy a hiba történt, amikor a kérelem feldolgozása közben a fázis ***TryGetEndpoint*** azaz úgy, hogy továbbítsa a szolgáltatásvégpont beolvasása közben. 
   * **errorDetails**: A végpont keresési feltételek sorolja fel. Itt láthatja, hogy a listenerName megadott = **FrontEndListener** , mivel a replika végponti listában csak a figyelő, amelynek a neve tartalmazza **OldListener**.
    
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
     Egy másik példa, ahol adhatnak vissza fordított proxy 404 nem található van: ApplicationGateway\Http konfigurációs paraméter **SecureOnlyMode** fordított proxyt az igaz értékre kell állítani a figyelést **HTTPS**, azonban a replika végpontok összes nem biztonságos (http-figyelés).
     Fordított proxy értéket ad vissza 404-es, mert nem található a figyelő továbbítja a kérést a HTTPS végpont. A paraméterek elemzése, abban az esetben, ha a probléma szűkítéséhez segítségével hasznos:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. A fordított proxy irányuló kérelem sikertelen, és egy időtúllépés. 
    Az eseménynaplókban egy esemény (itt nem látható) érkezett kérés részleteit.
    A következő esemény mutatja, hogy a 404-es állapotkódot válaszolt a szolgáltatás és a fordított proxy kezdeményezi újra feloldani. 

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
    Gyűjtött összes eseményt, amikor a vonat események minden megoldása és a továbbítás kísérlet láthatja.
    Az utolsó esemény a sorozat bemutatja a kérelem feldolgozása sikertelen volt az időtúllépés, valamint feloldása sikeres kísérletek száma.
    
    > [!NOTE]
    > Azt Javasoltjuk, védheti meg a részletes csatorna eseménygyűjtés alapértelmezés szerint le van tiltva, és engedélyezze a hibaelhárítás szükségességét alapon.

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
    
    Ha a gyűjtemény csak a kritikus hiba/események engedélyezve van, megjelenik egy eseményt az adatait az időkorlátot és a feloldás kísérletek száma. 
    
    Szolgáltatások, amelyek 404-es állapotkódot küldi vissza a felhasználó kívánja hozzá kell adnia egy "X-ServiceFabric" fejlécet a válaszban. A válasz a fejlécet ad hozzá, miután fordított proxy továbbítja az állapotkódot az ügyfélnek.  

4. Olyan esetekben, amikor az ügyfél megszakította a kérelmet.

    Következő eseményt rögzít a fordított proxy továbbító ügyfél válasz, de az ügyfél bontja a kapcsolatot:

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
5. Reverse Proxy returns 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST hibát ad vissza, ha a szolgáltatásjegyzékben a szolgáltatásvégpont nincs megadva az URI-séma.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    A probléma megoldása érdekében adja meg az URI-séma a jegyzékfájlban.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Websocket-kérés feldolgozási kapcsolatos események jelenleg nem jelentkezett. Ez a következő kiadásban fog bővülni.

## <a name="next-steps"></a>További lépések
* [Esemény összesítésére és a Windows Azure Diagnostics segítségével gyűjteményt](service-fabric-diagnostics-event-aggregation-wad.md) Azure fürtök Erőforrásnapló-gyűjtés engedélyezéséhez.
* Service Fabric-események megtekintése a Visual Studióban: [figyelése és diagnosztizálása helyileg](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Tekintse meg [fordított proxy konfigurálása biztonságos serviceshez való csatlakozáshoz](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) az Azure Resource Manager sablonminták konfigurálása védelmét a különböző szolgáltatási tanúsítvány a fordított proxy ellenőrzési lehetőségeit.
* Olvasási [Service Fabric fordított proxyja](service-fabric-reverseproxy.md) további.
