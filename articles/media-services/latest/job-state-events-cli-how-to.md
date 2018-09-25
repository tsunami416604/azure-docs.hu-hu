---
title: Az Azure Media Services-események átirányítása egyéni webes végpontra |} A Microsoft Docs
description: Azure Event Grid használatával előfizetni a Media Services feladat állapotváltozási esemény.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/20/2018
ms.author: juliako
ms.openlocfilehash: e7268a066acf41c454de0c66aa21603199d85a60
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034841"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Az Azure Media Services-események átirányítása egyéni webes végpontra parancssori felület használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben az Azure CLI használata az Azure Media Services feladat állapotváltozási események előfizetni, és elindítjuk az eseményt az eredmény megtekintéséhez. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. Ez az oktatóanyag bemutatja, hogyan hozhat létre, és állítsa be a webhook.

A cikkben leírt lépések elvégzése után látni fogja, hogy az eseményadatokat egy végpontnak küldte el a rendszer.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetéssel rendelkezik.
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

    Ellenőrizze, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve.

- Telepítse a [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Ez a cikk az Azure CLI 2.0-s vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Is használhatja a [Azure Cloud Shell](https://shell.azure.com/bash).

## <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése

Először is szüksége, győződjön meg arról, hogy rendelkezik-e Event Grid erőforrás-szolgáltató engedélyezve van az előfizetésben. 

Az a **Azure** portál tegye a következőket:

1. Ugrás az előfizetésekre.
2. Válassza ki előfizetését.
3. A beállítások területen válassza ki az erőforrás-szolgáltatók.
4. Keresse meg "EventGrid".
5. Ellenőrizze, hogy regisztrálva van az Event Griddel. Ha nem, nyomja le az **regisztrálása** gombra.  

## <a name="create-a-generic-azure-function-webhook"></a>Általános webhook Azure-függvény létrehozása 

### <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Az Event Grid cikk való feliratkozás, előtt hozzon létre egy végpontot, amely gyűjti az üzeneteket, hogy meg tudja őket tekinteni.

Leírtak szerint az általános webhook által aktivált függvény létrehozása a [általános webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) cikk. Ebben az oktatóanyagban a **C#** kódot használja.

A webhook létrehozása után másolja az URL-címet kattintva a *függvény URL-Címének lekérése* tetején található hivatkozásra a **Azure** portál ablakának. Nem kell az URL-cím utolsó része (*& clientID = default*).

![A webhook létrehozása](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>A webhook ellenőrzése

Ha regisztrálja az Event Griddel saját webhook-végpontot, azt, egy POST kérést küld egy egyszerű érvényesítési kóddal végpont tulajdonjogának igazolásához. Az alkalmazás kell válaszolnia a által echo vissza az érvényesítési kódot. Event Grid webHook végpontok, amelyek még nem telt az érvényesítés nem kézbesíteni. További információkért lásd: [Event Grid biztonsági és hitelesítési](https://docs.microsoft.com/azure/event-grid/security-authentication). Ez a szakasz két részből áll, amely át az ellenőrzés céljából meg kell határozni határozza meg.

#### <a name="update-the-source-code"></a>A Forráskód módosítása

A webhook létrehozása után a **run.csx** fájl így jelenik meg a böngészőben. Cserélje le az alapértelmezett kód a következő kóddal. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Frissítse a tesztelési kérelem törzse

Jobb oldalán a **Azure** portál ablakban megjelenik a két lap található: **fájlok megtekintését** és **teszt**. Kattintson a **Teszt** fülre. Az a **kérelem törzse**, illessze be a következő JSON-fájllal. Nem, nincs szükség sem tudják módosítani, beillesztheti.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Nyomja meg **mentés és Futtatás** az ablak tetején.

![Kérelem törzse](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Az Event Grid-előfizetés regisztrálása 

Event Grid megállapítani, hogy mely eseményeket kívánja nyomon követni egy cikk fizet. Az alábbi példa feliratkozik a Media Services-fiók létrehozása, és az Azure-függvény webhook hozott létre az eseményértesítés végpontjaként adja át az URL-címet. 

Cserélje le `<event_subscription_name>` az esemény-feliratkozás egyedi nevére. A `<resource_group_name>` és `<ams_account_name>`, használja a Media Services-fiók létrehozásakor használt értékeket. Az a `<endpoint_URL>` illessze be a végpont URL-CÍMÉT. Távolítsa el *& clientID = default* az URL-címből. Ha megadja a végpontot a feliratkozáskor, az Event Grid az adott végpontra irányítja az eseményeket. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

A Media Services fiók erőforrás-azonosító értéke ehhez hasonlóan néz ki:

```
/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount
```

## <a name="test-the-events"></a>Az események tesztelése

Egy kódolási feladat futtatásához. Például leírtak szerint a [videofájlok Stream](stream-files-dotnet-quickstart.md) rövid.

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Keresse meg a korábban létrehozott webhook. Kattintson a **figyelő** és **frissítése**. Láthatja, hogy a feladat állapota események: "Sorba állított", "Ütemezett", "Feldolgozási", "Kész", "Error", "Megszakítva", "Megszakítás".  További információkért lásd: [Media Services Eseménysémák](media-services-event-schemas.md).

Az alábbi példa bemutatja a séma JobStateChange esemény:

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Vizsgálati esemény](./media/job-state-events-cli-how-to/test_events.png)

## <a name="next-steps"></a>További lépések

[Reagálás eseményekre](reacting-to-media-services-events.md)

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
