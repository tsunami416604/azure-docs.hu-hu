---
title: Azure-üzenetküldő szolgáltatások – Szolgáltatáskezelő az erőforrás-kezelőnek
description: Ez a cikk elavult Azure Service Manager REST API-& PowerShell-parancsmagok leképezését ismerteti a PowerShell-API-k hoz & a PowerShell-parancsmagokhoz.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589908"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Az Azure Service Manager támogatásának eprektálása az Azure Service Bus, relay és Event Hubs szolgáltatásához

Az Erőforrás-kezelő, a következő generációs felhőalapú infrastruktúra-verem teljes mértékben felváltja a "klasszikus" Azure Service Management modellt (klasszikus üzembe helyezési modell). Ennek eredményeképpen a klasszikus üzembe helyezési modell REST API-k és a Service Bus, relay és Event Hubs támogatása 2021. Ezt az eprecációt először a [Microsoft Tech Community bejelentésén](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)jelentették be , de nemrég úgy döntöttünk, hogy az eredeti bejelentés időpontjától számított két évvel meghosszabbítjuk az aprecációs időszakot. Az egyszerű azonosítás érdekében `management.core.windows.net` ezek az API-k az URI-kban vannak. Tekintse meg az alábbi táblázatot az elavult API-k és az Azure Resource Manager API-verziója, amely most már használható.

A Service Bus, relay és Event Hubs használatának folytatásához 2021. Javasoljuk, hogy minden olyan ügyfél, aki még mindig régi API-kat használ, hogy hamarosan váltson, hogy kihasználja az Erőforrás-kezelő további előnyeit, amelyek magukban foglalják az erőforrás-csoportosítást, a címkéket, az egyszerűsített üzembe helyezési és kezelési folyamatot és a részletes hozzáférést szerepköralapú hozzáférés-vezérléssel (RBAC).

Az Azure Resource Manager és az Azure Service Manager szolgáltatáskezelővel kapcsolatos további információkért tekintse meg a [TechNet blogját.](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)

Az Azure Service Bus, relay és Event Hubs Service Manager és Resource Manager API-jairól a REST API dokumentációjában olvashat bővebben:

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API – Erőforrás-kezelő REST API

| Szolgáltatáskezelő API-k (elavult) | Erőforrás-kezelő – Service Bus API | Erőforrás-kezelő – Event Hub API | Erőforrás-kezelő – továbbító API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Névterek-GetNamespaceAsync** <br/>[Service Bus névtér betöltődése](/rest/api/servicebus/get-namespace)<br/>[Event Hub névtér bekéselése](/rest/api/eventhub/get-event-hub)<br/>[Névtér le- és bekerülési egysége](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Service Bus/Event Hub/Relay GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listabillentyűk](/rest/api/servicebus/namespaces/listkeys) | [listabillentyűk](/rest/api/eventhub/namespaces/listkeys) | [listabillentyűk](/rest/api/relay/namespaces/listkeys) |
| **Témakörök-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [lista](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Várólisták-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Relék-GetRelaysAsync**<br/>[Relék beszerezni](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [lista](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Service Bus/Event Hub/Relay GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) |[getauthorizationrule](/rest/api/eventhub/namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Névterek-DeleteNamespaceAsync**<br/>[Szolgáltatásbusz névtér törlése](/rest/api/servicebus/delete-namespace)<br/>[Az eseményközpontok névtér törlése](/rest/api/eventhub/delete-event-hub)<br/>[Névtér törlése](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Töröl](/rest/api/servicebus/namespaces/delete) | [Töröl](/rest/api/eventhub/namespaces/delete) | [Töröl](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>Szolgáltatásbusz/eseményközpont/továbbító névtér betöltője<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Szolgáltatásbusz/eseményközpont/továbbító névtér betöltője<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Szolgáltatásbusz/eseményközpont/továbbító névtér betöltője<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules -CreateNamespaceAuthorizationRuleAsync**<br/> 
Szervizbusz/eseményközpont/továbbító<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[Service Bus névtér betöltődése](/rest/api/servicebus/get-namespace)<br/>[Eseményközpontok névtér bekéselése](/rest/api/eventhub/get-event-hub)<br/>[Névtér le- és bekerülési egysége](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Service Bus/EventHub/Relay névtér bekéselése<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Szolgáltatási busz/EventHub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Eseményközpontok listázása](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [lista](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Eseményközpontok beszerezése](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Szervizbusz/eseményközpont/továbbító<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Szolgáltatási busz/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/eventhub/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceElérhető**<br/>[Service Bus névtér rendelkezésre állása](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNamespaceAsync**<br/>Szervizbusz/eseményközpont/továbbító<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Témakörök-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell – Erőforrás-kezelő PowerShell
| A Service Manager PowerShell parancs (elavult) | Új erőforrás-kezelői parancsok | Újabb erőforrás-kezelő parancs |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDRKonfiguráció](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRkonfiguráció](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Új-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Új-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Eltávolítás-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>További lépések
Lásd a következő dokumentációt: 

- Legújabb REST API dokumentáció
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- Legújabb PowerShell-dokumentáció
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
