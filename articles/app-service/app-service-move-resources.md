---
title: "Az Azure Web Apps áthelyezése egy másik erőforráscsoportban |} Microsoft Docs"
description: "Ez a témakör a ahol áthelyezheti webes alkalmazások és az App Service szolgáltatások az egyik erőforráscsoportból a másikba."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Az Azure Web Apps áthelyezése egy másik erőforráscsoportban
Egy másik erőforráscsoportban ugyanazt az előfizetést, vagy egy másik előfizetésben található erőforráscsoport áthelyezheti egy webalkalmazás és/vagy azok kapcsolódó erőforrásait. Ez történik, mint a szokásos erőforrás-kezelés az Azure-ban. További információkért lásd: [áthelyezése Azure-erőforrások új előfizetés vagy az erőforrás-csoporthoz](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Ha egyazon előfizetésen belül korlátozásai

Ha áthelyezi a webes alkalmazás _egyazon előfizetésen belül_, a feltöltött SSL-tanúsítványok nem helyezhető át. Azonban áthelyezheti egy webalkalmazást az új erőforráscsoport anélkül, hogy a feltöltött SSL-tanúsítványt, és az alkalmazás SSL funkció továbbra is működik. 

Ha szeretné helyezni az SSL-tanúsítványt a Web App, kövesse az alábbi lépéseket:

1.  Törölje a feltöltött tanúsítványt a webalkalmazásból.
2.  Helyezze át a webes alkalmazás.
3.  A tanúsítvány feltöltése az áthelyezett webalkalmazásban.

## <a name="limitations-when-moving-across-subscriptions"></a>Az előfizetések közötti áthelyezés vonatkozó korlátozások

Ha áthelyezi a webes alkalmazás _előfizetésekhez_, a következő korlátozások vonatkoznak:

- A célként megadott erőforráscsoport nem lehet a meglévő App Service-erőforrásokat. App Service-erőforrások többek között:
    - Web Apps
    - App Service-csomagok
    - Feltöltött vagy importált SSL-tanúsítványok
    - App Service-környezetek
- Az erőforráscsoport összes App Service erőforrások csak együtt helyezhetők.
- App Service-erőforrások csak eltolható az erőforráscsoport, amelyekben azokat eredetileg létrehozott. Ha egy App Service-erőforrás már nem az eredeti erőforráscsoportban, azt kell vissza kell helyezni az eredeti erőforráscsoport először, és majd akkor helyezheti át előfizetésekhez. 
