---
title: Service Bus-névtér létrehozása az Azure Portal használatával | Microsoft Docs
description: A Service Bus használatának megkezdéséhez szükség van egy névtérre. Az alábbiakban leírtak szerint hozhat létre egyet az Azure Portal használatával.
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub

---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Service Bus-névtér létrehozása az Azure Portal használatával
A névtér egy közös tároló az üzenetkezelés összes összetevője számára. Egyetlen névtér több üzenetsort és témakört tartalmazhat, és a névterek gyakran alkalmazástárolókként is szolgálnak. A Service Bus-névterek létrehozásának jelenleg különböző két módja van.

1. Azure Portal (ez a cikk)
2. [Resource Manager-sablonok][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Névtér létrehozása az Azure Portalon
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Gratulálunk! Létrehozott egy Service Bus üzenetkezelési névteret.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a [GitHub-mintáinkat][github-samples], amelyek az Azure Service Bus üzenetkezelési szolgáltatásának speciális funkcióit mutatják be.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


<!--HONumber=Oct16_HO3-->


