---
title: "Service Bus-névtér létrehozása az Azure Portalon | Microsoft Docs"
description: "Service Bus-névtér létrehozása az Azure Portal használatával."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: ffb134085c8555b22a317213622ca6c9490497d8
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="create-a-service-bus-namespace-using-the-azure-portal" class="xliff"></a>

# Service Bus-névtér létrehozása az Azure Portal használatával

A névtér egy hatókörkezelési tároló az üzenetkezelés összes összetevője számára. Egyetlen névtér több üzenetsort és témakört tartalmazhat, és a névterek gyakran alkalmazástárolókként is szolgálnak. A Service Bus-névterek létrehozásának két különböző módja van:

1. Azure Portal (ez a cikk)
2. [Resource Manager-sablonok][create-namespace-using-arm]

<a id="create-a-namespace-in-the-azure-portal" class="xliff"></a>

## Névtér létrehozása az Azure Portalon

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Gratulálunk! Létrehozott egy Service Bus üzenetkezelési névteret.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Tekintse meg a [GitHub-mintáinkat][github-samples], ahol további példákat talál, amelyek az Azure Service Bus üzenetkezelési szolgáltatásának speciális funkcióit mutatják be.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

