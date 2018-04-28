---
title: Azure Mobile Engagement Service API-k elérésére a REST API használatával
description: A Mobile Engagement REST API-k használata az Azure Mobile Engagement Service API-k elérésére
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: ea7fe6a58d8173c410573e3aa978cb1975ab5da7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Azure Mobile Engagement Service API-k elérésére használt többi
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

Az Azure Mobile Engagement biztosít a [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx) felügyelheti az eszközöket, Reach/leküldéses kampányokra stb.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

Ha nem szeretné, hogy közvetlenül a REST API-k használatával, azt is adja meg a [Swagger-fájl](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) használható az eszközök SDK-k létrehozására az a kívánt nyelvet. Azt javasoljuk, a [AutoRest](https://github.com/Azure/AutoRest) eszköz használatával hozzon létre az SDK a Swagger-fájl. A .NET SDK létrehoztunk egy hasonló módon, amely teszi lehetővé ezen API-k együttműködhet egy C# burkoló használatával, és nem kell a hitelesítési jogkivonat egyeztetést és frissítse magát. Lásd: [API a .NET SDK minta](mobile-engagement-dotnet-sdk-service-api.md) megtudhatja, hogyan használhatja a .net SDK API-hoz.
