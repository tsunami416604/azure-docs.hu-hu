---
title: Modul Twin JSON-sémája – Azure
description: Ez a témakör a IoT Edge élő videó-elemzési moduljának kettős JSON-sémáját ismerteti.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a342c59b35c7ebb4b6021163da76bdd3e0d449c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266814"
---
# <a name="module-twin-json-schema"></a>Modul Twin JSON-sémája

Az ikrek olyan JSON-dokumentumok, amelyek az eszköz állapotával kapcsolatos információkat tárolnak, beleértve a metaadatokat, a konfigurációkat és a feltételeket. Az IoT Hub minden egyes rácsatlakoztatott eszközhöz fenntart egy ikereszközt. Részletes magyarázat: az [ikrek megismerése és használata IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)

Ez a témakör a IoT Edge élő videó-elemzési moduljának kettős JSON-sémáját ismerteti.

> [!NOTE]
> A Media Services erőforrások és a Media Services API elérésének engedélyezéséhez először hitelesítenie kell magát. További információ: [hozzáférés a Azure Media Services API](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api)-hoz.

## <a name="module-twin-properties"></a>Modul Twin tulajdonságai

A IoT Edge Live Video Analytics a következő modul két tulajdonságát teszi elérhetővé: 

|Tulajdonság |Kötelező |Dinamikus |Description |
|---|---|---|---|
|applicationDataDirectory |Yes |No |Egy csatlakoztatott kötet elérési útja a konfiguráció megőrzése érdekében. |
|azureMediaServicesArmId |Yes |No |A Media Services fiók egyedi Azure-erőforrás-kezelési azonosítója.|
|aadTenantId |Yes |No |Ügyfél Azure AD-bérlő azonosítója.|
|aadServicePrincipalAppId |Igen |Igen |Az ügyfél létrehozta az Azure AD-AppId.|
|aadServicePrincipalCertificate |igen<sup>*</sup>  |Yes |Az ügyfél létrehozta az Azure AD AppId tanúsítványát.|
|aadServicePrincipalPassword |igen<sup>*</sup>  |Yes |Az ügyfél létrehozta az Azure AD AppId jelszavát.|
|aadEndpoint |Nem |Nem |Felhő-specifikus Azure AD-végpont. <br/>Alapértelmezett`https://login.microsoftonline.com` |
|aadResourceId |Nem |Nem |Felhő-specifikus Azure AD-célközönség/erőforrás-azonosító <br/>Alapértelmezett`https://management.core.windows.net/` |
|armEndpoint |Nem |Nem |Felhő-specifikus Azure-erőforrás kezelése végpont. <br/>Alapértelmezett`https://management.azure.com/` |
|diagnosticsLevel |No |Yes |Események részletessége: <br/>Információ & # x02758; Figyelmeztetés & # x02758; Hiba & # x02758; Kritikus & # x02758; NEz egy |
|diagnosticsEventsOutputName |No |Yes |Központi kimenet diagnosztikai eseményekhez. <br/>(Az üres érték azt jelenti, hogy a diagnosztika nincs közzétéve.)|
|operationalEventsOutputName|No|Yes|Az operatív események központi kimenete.<br/>(Az üres érték azt jelenti, hogy az operatív események nincsenek közzétéve)
|Naplózási szint|No|Yes|Az alábbiak valamelyikének telepítve kell lennie: <br/>& # x000B7; Részletes<br/>& # x000B7; Információ (alapértelmezett)<br/>& # x000B7; Figyelmeztetés<br/>& # x000B7; Hiba<br/>& # x000B7; NEz egy|
|logCategories|No|Yes|A következők vesszővel tagolt listája: alkalmazás, MediaPipeline, események <br/>Alapértelmezett: alkalmazás, események|
|debugLogsDirectory|No|Yes|Hibakeresési naplók könyvtára. Ha a rendszer a jelenleg létrehozott naplókat állítja elő, a rendszer letiltja a hibakeresési naplókat.

<sup>*</sup>Az egyszerű szolgáltatás tanúsítványát vagy jelszavát kell megadnia. 

A dinamikus tulajdonságok a modul újraindítása nélkül is frissíthetők. A következő tulajdonságok értékeinek beszerzéséhez kövesse a [hozzáférés Media Services API-hoz](../latest/access-api-cli-how-to.md) című cikk utasításait. 

A választható diagnosztikai beállításokkal kapcsolatos további információkért tekintse meg a [figyelés és naplózás](monitoring-logging.md) című cikket.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>További lépések

[Közvetlen metódusok](direct-methods.md)
