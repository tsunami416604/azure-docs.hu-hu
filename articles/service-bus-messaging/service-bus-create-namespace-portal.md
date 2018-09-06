---
title: Service Bus-névtér létrehozása az Azure Portalon | Microsoft Docs
description: Service Bus-névtér létrehozása az Azure Portal használatával.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/29/2018
ms.author: spelluru
ms.openlocfilehash: 699129e9f75cce76d1682e3e2e2fb83d248ea92e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696025"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Service Bus-névtér létrehozása az Azure Portal használatával

A névtér egy hatókörkezelési tároló az üzenetkezelés összes összetevője számára. Egyetlen névtér több üzenetsort és témakört tartalmazhat, és a névterek gyakran alkalmazástárolókként is szolgálnak. A Service Bus-névterek létrehozásának két módja van:

1. Azure Portal (ez a cikk)
2. [Resource Manager-sablonok][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Névtér létrehozása az Azure Portalon

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Gratulálunk! Létrehozott egy Service Bus üzenetkezelési névteret.

## <a name="next-steps"></a>További lépések

Tekintse meg a Service Bus [GitHub-mintáit][github-samples], ahol további példákat talál a Service Bus üzenetkezelési szolgáltatásának speciális funkcióihoz.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
