---
title: Azure App Service erőforrások áthelyezése
description: A Azure Resource Manager használatával áthelyezheti App Service erőforrásait egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150899"
---
# <a name="move-guidance-for-app-service-resources"></a>Útmutató a App Service-erőforrások áthelyezéséhez

App Service erőforrások áthelyezésének lépései attól függően változnak, hogy egy előfizetésen belül vagy egy új előfizetésben helyezi át az erőforrásokat.

## <a name="move-in-same-subscription"></a>Áthelyezés ugyanabba az előfizetésbe

Ha egy webalkalmazást _ugyanabba az előfizetésbe_helyez át, a külső féltől származó SSL-tanúsítványok nem helyezhetők át. A webalkalmazásokat azonban áthelyezheti az új erőforráscsoporthoz anélkül, hogy áthelyezi a külső gyártó tanúsítványát, és az alkalmazás SSL-funkciója továbbra is működik.

Ha szeretné helyezni az SSL-tanúsítványt a webalkalmazással, kövesse az alábbi lépéseket:

1. A külső gyártótól származó tanúsítvány törlése a webalkalmazásból, de a tanúsítvány másolatának megtartása
2. Helyezze át a webes alkalmazás.
3. Töltse fel a külső gyártótól származó tanúsítványt az áthelyezett webalkalmazásba.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Ha a webalkalmazás áthelyezése _előfizetésekben_, az alábbi korlátozások érvényesek:

- A cél erőforráscsoport nem kell rendelkeznie minden olyan meglévő App Service-erőforrásokat. App Service-erőforrások a következők:
    - Web Apps
    - App Service-csomagok
    - Feltöltött vagy importált SSL-tanúsítványok
    - App Service-környezetek
- Az erőforráscsoportban lévő összes App Service-erőforrások együtt lehessen áthelyezni.
- App Service-erőforrások csak akkor helyezhető el az erőforráscsoportból, amelyben eredetileg létrehozták őket. Ha egy App Service erőforrás már nem szerepel az eredeti erőforráscsoporthoz, helyezze vissza az eredeti erőforráscsoporthoz. Ezután helyezze át az erőforrást az előfizetések között.

Ha nem emlékszik az eredeti erőforráscsoporthoz, megkeresheti azt a diagnosztika használatával. A webalkalmazáshoz válassza a **diagnosztizálás és problémák megoldása**lehetőséget. Ezután válassza a **konfiguráció és kezelés**lehetőséget.

![Diagnosztika kiválasztása](./media/app-service-move-limitations/select-diagnostics.png)

Válassza az **áttelepítési beállítások**lehetőséget.

![Áttelepítési beállítások kiválasztása](./media/app-service-move-limitations/select-migration.png)

A webalkalmazás áthelyezéséhez válassza a javasolt lépések lehetőséget.

![Javasolt lépések kiválasztása](./media/app-service-move-limitations/recommended-steps.png)

Az erőforrások áthelyezése előtt el kell végeznie az ajánlott műveleteket. Az információ tartalmazza a webalkalmazás eredeti erőforrás-csoportját.

![Javaslatok](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>App Service-tanúsítvány áthelyezése

A App Service-tanúsítvány áthelyezheti egy új erőforráscsoporthoz vagy előfizetésbe. Ha a App Service-tanúsítvány egy webalkalmazáshoz van kötve, néhány lépést végre kell hajtania az erőforrások új előfizetésbe való áthelyezése előtt. Az erőforrások áthelyezése előtt törölje az SSL-kötést és a privát tanúsítványt a webalkalmazásból. A App Service-tanúsítvány nem kell törölni, csak a privát tanúsítványt a webalkalmazásban.

## <a name="move-support"></a>Támogatás áthelyezése

A következő témakörben megtudhatja, hogy mely App Service erőforrásokat lehet áthelyezni:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../resource-group-move-resources.md).
