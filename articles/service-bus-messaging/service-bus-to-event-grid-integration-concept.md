---
title: Az Azure Service Bus – Event Grid integráció áttekintése | Microsoft Docs
description: A Service Bus-üzenetkezelés és az Event Grid integrációjának leírása
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 8bd1c431788d78ae937cc047e82cb41504a19075
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Az Azure Service Bus – Azure Event Grid integráció áttekintése

Az Azure Service Bus újdonsága, hogy integrálható az Azure Event Griddel. A funkció által nyújtott legfontosabb előny, hogy a kevés üzenettel rendelkező Service Bus üzenetsoroknak vagy előfizetéseknek nem kell olyan fogadóval rendelkezniük, amely folyamatosan üzeneteket kérdez le. 

A Service Bus mostantól az Event Gridre bocsáthatja ki az eseményeket, amikor üzenetek találhatók egy üzenetsorban vagy előfizetésben, és nincsenek jelen fogadók. Létrehozhat Event Grid-előfizetéseket a Service Bus-névterekhez, megfigyelheti ezeket az eseményeket, és egy fogadó indításával reagálhat rájuk. Ezzel a funkcióval a Service Bus reaktív programozási modellekben használható.

A funkció engedélyezéséhez a következőkre van szüksége:

* Egy prémium szintű Service Bus-névtér legalább egy Service Bus-üzenetsorral, vagy pedig egy legalább egy előfizetéssel rendelkező Service Bus-témakör.
* Közreműködői hozzáférés a Service Bus-névtérhez.
* Ezenkívül szükség van egy Event Grid-előfizetésre a Service Bus-névtérhez. Ez az előfizetés értesítést kap az Event Gridtől, ha üzeneteket kell átvenni. Tipikus előfizetők lehetnek az Azure App Service Logic Apps funkciója, az Azure Functions vagy egy webalkalmazáshoz csatlakozó webhook. Az üzeneteket ezután az előfizető dolgozza fel. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Győződjön meg arról, hogy rendelkezik közreműködői hozzáféréssel

Keresse meg a Service Bus-névteret, és válassza az alább látható **Hozzáférés-vezérlés (IAM)** lehetőséget:

![1][]

### <a name="events-and-event-schemas"></a>Események és eseménysémák

A Service Bus jelenleg két forgatókönyvhöz küld eseményeket.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Emellett a Service Bus a standard Event Grid biztonsági és [hitelesítési mechanizmusokat](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication) használja.

További információk: [Azure Event Grid-eseménysémák](https://docs.microsoft.com/en-us/azure/event-grid/event-schema).

#### <a name="active-messages-available-event"></a>Elérhető aktív üzenetek esemény

Ez az esemény akkor jön létre, ha aktív üzenetek találhatók egy üzenetsorban vagy előfizetésben, és nincsenek figyelő fogadók.

Az esemény sémája a következő:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>Elérhető feldolgozatlan üzenetek esemény

Legalább egy eseményt kap egy feldolgozatlan üzenetek sorhoz, amelyben üzenetek találhatók, de aktív fogadók nincsenek.

Az esemény sémája a következő:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Hány eseményt bocsát ki és milyen gyakran?

Ha több üzenetsora és témaköre / előfizetése található a névtéren, üzenetsoronként vagy előfizetésenként legalább egy eseményt kap. Az események kibocsátása azonnal megtörténik, ha nincsenek üzenetek a Service Bus entitásban, és új üzenet érkezik. Vagy pedig két percenként, ha az Azure Service Bus nem észlel aktív fogadót. Az üzenetek közötti böngészés nem szakítja meg az eseményeket.

Alapértelmezés szerint a Service Bus a névtérben lévő összes entitáshoz eseményeket bocsát ki. Ha csak adott entitásokhoz szeretne eseményeket kapni, tekintse meg a következő szakaszt.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Az események forrásainak korlátozása szűrők használatával

Ha a névtérben például csak egy üzenetsor vagy egy előfizetés eseményeit szeretné megkapni, az Event Grid *Kezdete* és *Vége* szűrőit használhatja. Egyes interfészek esetén a szűrők *Előtag* és *Utótag* néven szerepelnek. Ha több üzenetsorból és előfizetésből, de nem az összesből szeretne eseményeket kapni, létrehozhat több különböző Event Grid-előfizetést, és mindegyikhez megadhat egy-egy szűrőt.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Event Grid-előfizetések létrehozása Service Bus-névterekhez

Három különböző módon hozhatók létre Event Grid-előfizetések a Service Bus-névterekhez.

* Az [Azure Portalon](#portal-instructions)
* Az [Azure CLI](#azure-cli-instructions) használatával
* A [PowerShell](#powershell-instructions) használatával

## <a name="azure-portal-instructions"></a>Azure-portálutasítások

Új Event Grid-előfizetést a következő módon hozhat létre:
1. Az Azure Portalon lépjen a névteréhez.
2. A bal oldali panelen válassza az **Event Grid** elemet. 
3. Válassza az **Esemény-előfizetés** lehetőséget.  

   Az alábbi képen egy olyan névtér látható, amely már rendelkezik néhány Event Grid-előfizetéssel.

   ![20][]

   Az alábbi kép azt mutatja, hogyan lehet előfizetni egy függvényre vagy webhookra szűrés nélkül.

   ![21][]

## <a name="azure-cli-instructions"></a>Azure CLI utasítások

Győződjön meg róla, hogy telepítve van az Azure CLI 2.0-s vagy újabb verziója. [Töltse le a telepítőt](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Nyomja le a **Windows + X** billentyűkombinációt, és nyisson meg egy új PowerShell-konzolt rendszergazdai jogosultságokkal. Parancsrendszerhéjat is használhat az Azure Portalon.

Hajtsa végre a következő kódot:

```PowerShell-interactive
Az login

Az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell-utasítások

Győződjön meg róla, hogy telepítve van az Azure PowerShell. [Töltse le a telepítőt](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.4.0). Nyomja le a **Windows + X** billentyűkombinációt, és nyisson meg egy új PowerShell-konzolt rendszergazdai jogosultságokkal. Parancsrendszerhéjat is használhat az Azure Portalon.

```PowerShell-interactive
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

Innen felfedezheti a többi beállítási lehetőséget, vagy [tesztelheti az események áramlását](#test-that-events-are-flowing).

## <a name="next-steps"></a>További lépések

* Service Bus és Event Grid [példák](service-bus-to-event-grid-integration-example.md) megtekintése.
* További tudnivalók az [Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/) szolgáltatásról.
* További tudnivalók az [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/) szolgáltatásról.
* További tudnivalók a [Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/) szolgáltatásról
* További tudnivalók a [Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/) szolgáltatásról.

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
