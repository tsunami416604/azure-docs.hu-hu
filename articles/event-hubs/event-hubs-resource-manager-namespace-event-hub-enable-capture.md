---
title: "Azure Event Hubs-névtér létrehozása és a Rögzítés funkció engedélyezése sablon használatával | Microsoft Docs"
description: "Azure Event Hubs-névtér létrehozása egy eseményközponttal és a Rögzítés funkció engedélyezése az Azure Resource Manager sablonjának használatával"
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>

# Event Hubs-névtér létrehozása egy eseményközponttal és a Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával
Ez a cikk egy olyan Azure Resource Manager-sablon használatát ismerteti, amellyel egy eseményközponti példánnyal rendelkező Event Hubs-névtér hozható létre, és engedélyezhető az eseményközpont Rögzítés funkciója. A cikk leírja továbbá, hogyan kell meghatározni, hogy mely erőforrások lesznek üzembe helyezve, és hogyan kell meghatározni az üzembe helyezés végrehajtásakor megadandó paramétereket. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

Az Azure-erőforrások elnevezési konvencióiról további információkat az [Azure Resources Naming Conventions][Azure Resources Naming Conventions] (Azure-erőforrások elnevezési konvenciói) című témakörben talál.

A teljes sablont a GitHub [Event hub and enable Capture template][Event Hub and enable Capture template] (Sablon – Eseményközpont létrehozása és a Rögzítés engedélyezése) oldalán találja.

> [!NOTE]
> A legújabb sablonokért keresse fel az [Azure-gyorssablonok][Azure Quickstart Templates] gyűjteményt, és keressen az Event Hubs kifejezésre.
> 
> 

<a id="what-will-you-deploy" class="xliff"></a>

## Mit fog üzembe helyezni?
Ezzel a sablonnal egy Event Hubs-névteret helyez üzembe eseményközponttal, továbbá engedélyezi az [az Event Hubs Rögzítés funkcióját](event-hubs-capture-overview.md).

Az [Event Hubs](event-hubs-what-is-event-hubs.md) egy eseményfeldolgozási szolgáltatás, amely az Azure-ba irányuló, nagy léptékű esemény- és telemetriabevitelt biztosít alacsony késéssel és nagy megbízhatósággal. Az Event Hubs Rögzítés funkciójával az Event Hubsban tárolt streamelési adatokat automatikusan továbbíthatja bármelyik Azure Blob Storage-tárolóba tetszőleges időközönként és méretben.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

<a id="parameters" class="xliff"></a>

## Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket adja meg.

<a id="eventhubnamespacename" class="xliff"></a>

### eventHubNamespaceName
A létrehozandó Event Hubs-névtér neve.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

<a id="eventhubname" class="xliff"></a>

### eventHubName
Az Event Hubs-névtérben létrehozott eseményközpont neve.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

<a id="messageretentionindays" class="xliff"></a>

### messageRetentionInDays
A napok száma, amíg az üzenetek meg lesznek őrizve az eseményközpontban. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

<a id="partitioncount" class="xliff"></a>

### partitionCount
Az eseményközpontban létrehozandó partíciók száma.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

<a id="captureenabled" class="xliff"></a>

### captureEnabled
A Rögzítés funkció engedélyezése az eseményközpontban.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
<a id="captureencodingformat" class="xliff"></a>

### captureEncodingFormat
Az eseményadat szerializálásához megadott kódolási formátum.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

<a id="capturetime" class="xliff"></a>

### captureTime
Az az időintervallum, amely során az Event Hubs Rögzítés funkciója elkezdi az adatok Azure Blob Storage-ba való rögzítését.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

<a id="capturesize" class="xliff"></a>

### captureSize
Az a méretintervallum, amelyben a Rögzítés funkció elkezdi az adatok Azure Blob Storage-ba való rögzítését.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

<a id="destinationstorageaccountresourceid" class="xliff"></a>

### destinationStorageAccountResourceId
A Rögzítés használatához egy Azure Storage-fiókhoz tartozó erőforrás-azonosító szükséges, amellyel engedélyezheti a rögzítést a választott Storage-fiókban.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

<a id="blobcontainername" class="xliff"></a>

### blobContainerName
A blobtároló, amelyben rögzíti az eseményadatokat.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


<a id="apiversion" class="xliff"></a>

### apiVersion
A sablon API-verziója.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

<a id="resources-to-deploy" class="xliff"></a>

## Üzembe helyezendő erőforrások
**EventHubs** típusú névteret hoz létre egy eseményközponttal, és engedélyezi a Rögzítés funkciót.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

<a id="commands-to-run-deployment" class="xliff"></a>

## Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

<a id="powershell" class="xliff"></a>

## PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

<a id="azure-cli" class="xliff"></a>

## Azure CLI
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
<a id="next-steps" class="xliff"></a>

## Következő lépések

Az Event Hubs Rögzítés funkcióját az [Azure Portal](https://portal.azure.com) segítségével is konfigurálhatja. További információért tekintse meg az [Enable Event Hubs Capture using the Azure portal](event-hubs-capture-enable-through-portal.md) (Az Event Hubs Rögzítés funkciójának engedélyezése az Azure Portalon) című témakört.

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

