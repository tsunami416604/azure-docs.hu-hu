---
title: Azure Media Services eseményeket továbbítani egy egyéni webes végpontjának |} Microsoft Docs
description: Esemény rács Azure Media Services feladat állapotmódosítási esemény használni.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 2780438e715b6f6cb04d820c02d09f14e14b480f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Azure Media Services eseményeket továbbítani egy egyéni webkiszolgáló-végpont parancssori felület használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben Azure CLI használata az Azure Media Services feladat állapotváltozási események előfizetni, és az eredmények megjelenítéséhez esemény következik be. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. Ez az oktatóanyag bemutatja, hogyan hozzon létre, és állítsa be a webhook.

A cikkben leírt lépések elvégzése után látni fogja, hogy az eseményadatokat egy végpontnak küldte el a rendszer.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be a [Azure-portálon](http://portal.azure.com) , majd indítsa el **CloudShell** végrehajtani parancssori felület parancsait, ahogy az az alábbi lépéseket.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Ügyeljen arra, hogy ne feledje az értékeket, amelyeket Ön a Media Services-fiók neve, a tároló neve és az erőforrás neve.

## <a name="enable-event-grid-resource-provider"></a>Erőforrás-szolgáltató esemény rács engedélyezése

Először is végre kell hajtani, győződjön meg arról, hogy rendelkezik-e esemény rács erőforrás-szolgáltató engedélyezve van az előfizetésben. 

Az a **Azure** portal tegye a következőket:

1. Nyissa meg az előfizetésekhez.
2. Válassza ki előfizetését.
3. A beállítások válassza ki az erőforrás-szolgáltatók.
4. Keresse meg "EventGrid".
5. Ellenőrizze, hogy regisztrálva van-e az esemény rács. Ha nem, nyomja meg a **regisztrálása** gombra.  

## <a name="create-a-generic-azure-function-webhook"></a>Egy Azure-függvény általános webhook létrehozása 

### <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Előfizetés az esemény rács cikk, mielőtt hozzon létre egy végpontot, amely összegyűjti az üzeneteket, így meg lehet tekinteni őket.

Általános webhook váltja ki, a függvény létrehozása a [általános webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) cikk. Ebben az oktatóanyagban a **C#** kódot használja.

A webhook létrehozása után másolja az URL-címet parancsával a *függvény URL-cím beszerzése* tetején található hivatkozásra a **Azure** portál ablakának. Nem kell az URL-cím utolsó része (*& clientID alapértelmezett =*).

![A webhook létrehozása](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>A webhook ellenőrzése

Ha saját webhook végpont regisztrálása esemény rács, küld, akkor egy POST kérést egy egyszerű érvényességi kóddal igazolnia a végpont tulajdonosa. Az alkalmazás kell válaszolnia által echo vissza az érvényesítési kódot. Esemény rács webHook végpontok, amelyek még nem kapott az érvényesítés nem kézbesíteni. További információkért lásd: [esemény rács biztonsági és hitelesítési](https://docs.microsoft.com/azure/event-grid/security-authentication). Ez a szakasz két részből áll, amelyek felelt meg az érvényesítési definiálni kell határozza meg.

#### <a name="update-the-source-code"></a>A Forráskód módosítása

Miután létrehozta a webhook a **run.csx** fájl jelenik meg a böngészőben. Cserélje le az alapértelmezett kód a következő kódra. 

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

#### <a name="update-test-request-body"></a>Tesztelési kérelem törzsében frissítése

Jobb oldalán a **Azure** két lap megjelenik, a portál ablakának: **-fájlokat tekinthetnek meg** és **teszt**. Kattintson a **Teszt** fülre. Az a **Request body**, illessze be a következő json. Mivel, nincs szükség semmilyen értéket módosítására is beilleszthető legyen.

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

Nyomja le az **mentéséhez, és futtassa** az ablak tetején.

![Kérelem törzse](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Az esemény rács előfizetés regisztrálása 

Előfizet egy cikkben esemény rács mely eseményeket kíván nyomon követni. A következő példa a Media Services-fiók létrehozása, és adja át az URL-cím és a végpontnak a eseményértesítés létrehozott Azure-függvény webhook a számítógépcsoportra fizetett elő. 

Cserélje le `<event_subscription_name>` esemény előfizetési egyedi névvel. A `<resource_group_name>` és `<ams_account_name>` elemnél a korábban létrehozott értékeket adja meg.  Az a `<endpoint_URL>` illessze be a végpont URL-CÍMÉT. Távolítsa el *& clientID alapértelmezett =* az URL-címről. Ha megadja a végpontot a feliratkozáskor, az Event Grid az adott végpontra irányítja az eseményeket. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

A Media Services-fiók erőforrás-azonosító értéke ehhez hasonlóan néz ki:

/Subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/Providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Az események tesztelése

Egy kódolási feladat futtatásához. Például, amint a [videofájlok adatfolyam](stream-files-dotnet-quickstart.md) gyors üzembe helyezés.

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tallózással keresse meg a korábban létrehozott webhook. Kattintson a **figyelő** és **frissítése**. Láthatja, hogy a feladat állapota megváltozik események: "Aszinkron", "Ütemezett", "Feldolgozásra", "Kész", "Error", "Megszakítva", "Visszavonás".  További információkért lásd: [Media Services esemény sémák](media-services-event-schemas.md).

Példa:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a tárfiók és az esemény előfizetésének használatát, akkor ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

A `<resource_group_name>` elemet cserélje le a fent létrehozott erőforráscsoportra.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>További lépések

[Események reagálnak](reacting-to-media-services-events.md)
