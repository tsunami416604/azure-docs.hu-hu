<properties
    pageTitle="Service Bus-névtér létrehozása az Azure Portal használatával | Microsoft Azure"
    description="A Service Bus használatának megkezdéséhez szükség van egy névtérre. Az alábbiakban leírtak szerint hozhat létre egyet az Azure Portal használatával."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# Service Bus-névtér létrehozása az Azure Portal használatával

A névtér egy közös tároló az üzenetkezelés összes összetevője számára. Egyetlen névtér több üzenetsort és témakört tartalmazhat, és a névterek gyakran alkalmazástárolókként is szolgálnak. A Service Bus-névterek létrehozásának jelenleg különböző két módja van.

1.  Azure Portal (ez a cikk)

2.  [Resource Manager-sablonok][create-namespace-using-arm]

## Névtér létrehozása az Azure Portalon

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Gratulálunk! Létrehozott egy Service Bus üzenetkezelési névteret.

## Következő lépések

Tekintse meg a [GitHub-tárunkat](https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples), ahol további példákat talál, amelyek az Azure Service Bus üzenetkezelési szolgáltatásának speciális funkcióit mutatják be.

[create-namespace-using-arm]: ./service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


<!--HONumber=sep16_HO1-->


