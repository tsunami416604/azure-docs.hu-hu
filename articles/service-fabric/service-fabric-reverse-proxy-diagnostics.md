---
title: "Az Azure Service Fabric fordított proxy diagnosztika |} Microsoft Docs"
description: "Megtudhatja, hogyan figyelése és diagnosztizálása a fordított proxy kérelem feldolgozását."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: 1c62d2390709577bfde6225b783642fb55396a6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Figyelése és diagnosztizálása kérelem feldolgozását a fordított proxy

A Service Fabric 5.7 kiadástól kezdve, fordított proxy események állnak rendelkezésre a következő gyűjtemény számára. Az események két csatorna, egyet a kérelem feldolgozása sikertelen a fordított proxy és a bejegyzéseket a sikeres és a sikertelen kérelmek részletes eseményeket tartalmazó második csatorna kapcsolatos hibaeseményeket csak érhetők el.

Tekintse meg [fordított proxy eseményeinek gyűjtése](service-fabric-diagnostics-event-aggregation-wad.md#collect-reverse-proxy-events) ahhoz, hogy ezeknek a csatornáknak a helyi és az Azure Service Fabric-fürtök gyűjtését eseményekről.

## <a name="troubleshoot-using-diagnostics-logs"></a>Diagnosztikai naplók segítségével hibaelhárítása
Íme néhány példa a találkozhat egy általános hiba naplók értelmezése:

1. Fordított proxy válasz állapotkódja 504 (Timeout) adja vissza.

    Több ok miatt a szolgáltatás válaszolni a kérés megadott időn belül nem lehet.
Az első esemény naplók alatt a fordított proxy fogadja a kérelem részletes adatait. A második esemény azt jelzi, hogy a kérelem-továbbítást a szolgáltatás, miközben miatt "belső hiba = ERROR_WINHTTP_TIMEOUT" 

    A tartalom tartalmazza:

    *  **traceId**: A GUID egy kérelemhez tartozó összes esemény összefüggéseket használható. Az az alábbi két esemény, a traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, úgy, hogy a kérésben tartoznak.
    *  **requestUrl**: az URL-cím (fordított proxy URL-cím), amelyhez a kérés lett elküldve.
    *  **művelet**: HTTP-műveletet.
    *  **remoteAddress**: a kérést küldő ügyfél címe.
    *  **resolvedServiceUrl**: végpont URL-címét, amelyhez a bejövő kérelem lett feloldva. 
    *  **Hiba részletei**: a hibával kapcsolatos további információkat.

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

2. Fordított proxy válasz állapotkódja 404-es (nem található) adja vissza. 
    
    Íme egy példa esemény, fordított proxy ahol adja vissza a 404-es a mivel az nem található a megfelelő szolgáltatásvégpontot.
    A tartalom itt egyik fontos a rendszer:
    *  **processRequestPhase**: azt jelzi, a fázis, a hiba történt, amikor a kérelem feldolgozása közben ***TryGetEndpoint*** Egytényezős a szolgáltatásvégpont továbbítania beolvasása közben. 
    *  **Hiba részletei**: a végpont keresési feltételek sorolja fel. Itt láthatja, hogy a listenerName megadott = **FrontEndListener** mivel a replika végpontlistát csak figyelő, amelynek a neve tartalmaz **OldListener**.
    
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
    Ha fordított proxy 404 adhat vissza egy másik példa van nem található: ApplicationGateway\Http konfigurációs paraméter **SecureOnlyMode** a fordított proxykiszolgálón igaz értékre kell állítani a figyelést **HTTPS**, azonban a replika végpontok összes nem biztonságos (HTTP-figyelő).
    Fordított proxy beolvasása 404, mert nem található a végpont figyel a következőn való továbbítja a kérelmet HTTPS. A paraméterek elemzése, abban az esetben, ha a probléma szűkítéséhez segítségével hasznos:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. A fordított proxykiszolgálóhoz kérelem időtúllépési hiba miatt sikertelen. 
    Az eseménynaplók esemény tartalmaznak, a kérelem érkezett adatokkal (itt nem látható).
    A következő esemény jeleníti meg, hogy a 404-es állapotkóddal válaszolt a szolgáltatás és a fordított proxy indít újra feloldani. 

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
    Gyűjtött összes eseményt, amikor a vonat események megjelenítése minden ki, és előre kísérlet megjelenik.
    Az adatsorozat utolsó esemény jeleníti meg, a kérelem feldolgozása sikertelen volt egy időkorlát együtt sikeres feloldása kísérletek száma.
    
    > [!NOTE]
    > Javasoljuk, hogy alapértelmezés szerint le van tiltva a részletes csatorna eseménygyűjtés tartani, és engedélyezze a hibaelhárításhoz szükséges alapon.

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
    
    Gyűjtemény csak a kritikus hiba/események engedélyezve van, megjelenik egy esemény részleteit az időkorlát és a feloldás kísérletek száma. 
    
    Szolgáltatások a 404 állapotkódot küldi vissza a felhasználót, melyet a válaszban szereplő kell adjon meg egy "X-ServiceFabric" fejlécet. A válaszhoz a fejléc hozzáadása után fordított proxy továbbítja az állapotkód: az ügyfélnek.  

4. Olyan esetekben, amikor az ügyfél a kérés megszakadt.

    Fordított proxy továbbító ügyfél válasz, de az ügyfél kapcsolata megszakad következő eseményt rögzít:

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
5. Fordított Proxy 404-es FABRIC_E_SERVICE_DOES_NOT_EXIST adja vissza.

    FABRIC_E_SERVICE_DOES_NOT_EXIST hiba jelentkezik, ha a szolgáltatási végpont a szolgáltatás jegyzékben nincs megadva az URI-séma.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    A probléma megoldása érdekében adja meg az URI-séma a jegyzékfájlban.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> A websocket-kérelem feldolgozásra kapcsolatos események nem jelenleg bejelentkezett. Ez a következő kiadásban lesz hozzáadva.

## <a name="next-steps"></a>Következő lépések
* [Esemény összesítésére és az adatgyűjtést, a Windows Azure diagnosztikai](service-fabric-diagnostics-event-aggregation-wad.md) naplógyűjtést Azure fürtök engedélyezéséhez.
* Service Fabric-események megtekintése a Visual Studio: [figyelése és diagnosztizálása helyileg](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Tekintse meg [fordított proxy konfigurálása a biztonságos szolgáltatásokhoz](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) az Azure Resource Manager sablon minták konfigurálása biztonságos különböző szolgáltatástanúsítvány fordított proxy ellenőrzési beállítások.
* Olvasási [Service Fabric fordított proxy](service-fabric-reverseproxy.md) további.
