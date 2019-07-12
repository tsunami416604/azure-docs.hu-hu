---
title: Az Azure App Service-erőforrások áthelyezése új előfizetést vagy az erőforrás-csoport
description: Azure Resource Manager használatával App Service-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723570"
---
# <a name="move-guidance-for-app-service-resources"></a>Útmutató az App Service-erőforrások áthelyezése

Ha áthelyez egy előfizetésen belül vagy egy új előfizetést az erőforrások áthelyezése az App Service-erőforrások eltérőek a lépések alapján.

## <a name="move-in-same-subscription"></a>Azonos előfizetésben található áthelyezése

Ha a webalkalmazás áthelyezése _ugyanazon az előfizetésen belül_, külső SSL-tanúsítványok nem helyezhetők át. Azonban áthelyezheti egy webalkalmazást az új erőforráscsoport áthelyezése a külső tanúsítvány nélkül, és az alkalmazás SSL funkció továbbra is működik.

Ha szeretné helyezni az SSL-tanúsítványt a webalkalmazással, kövesse az alábbi lépéseket:

1. Törölje a külső tanúsítványt a webalkalmazásból, de a tanúsítvány egy példányát megtartása
2. Helyezze át a webes alkalmazás.
3. A külső tanúsítvány feltöltése az áthelyezett webalkalmazáshoz.

## <a name="move-across-subscriptions"></a>Áthelyezése előfizetések között

Ha a webalkalmazás áthelyezése _előfizetésekben_, az alábbi korlátozások érvényesek:

- A cél erőforráscsoport nem kell rendelkeznie minden olyan meglévő App Service-erőforrásokat. App Service-erőforrások a következők:
    - Web Apps
    - App Service-csomagok
    - Feltöltött vagy importált SSL-tanúsítványok
    - App Service-környezetek
- Az erőforráscsoportban lévő összes App Service-erőforrások együtt lehessen áthelyezni.
- App Service-erőforrások csak akkor helyezhető el az erőforráscsoportból, amelyben eredetileg létrehozták őket. Ha egy App Service-erőforrás már nem az eredeti erőforráscsoportba, helyezze vissza az eredeti erőforrás-csoportba. Ezt követően az erőforrás áthelyezése előfizetések között.

Ha nem emlékszik az eredeti erőforráscsoportot, megtalálhatja diagnosztikai keresztül. Válassza ki a webalkalmazás **diagnosztizálása és a problémák megoldásához**. Ezután válassza ki **konfigurációs és felügyeleti**.

![Válassza ki a diagnosztika](./media/app-service-move-limitations/select-diagnostics.png)

Válassza ki **áttelepítési lehetőségek**.

![Válassza ki az áttelepítési lehetőségek](./media/app-service-move-limitations/select-migration.png)

Válassza ki a webes alkalmazás áthelyezése szükséges lépésekről beállítását.

![Válassza ki a javasolt lépések](./media/app-service-move-limitations/recommended-steps.png)

Láthatja, hogy az erőforrások áthelyezése előtt elvégzendő műveleteket. Az információ tartalmazza a webalkalmazás az eredeti erőforráscsoportot.

![Javaslatok](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Helyezze át az App Service-tanúsítvány

Az App Service-tanúsítvány áthelyezheti egy új erőforráscsoportot vagy előfizetést. Ha az App Service-tanúsítványt egy webalkalmazáshoz kötött, előtt az erőforrások áthelyezése új előfizetésre kell végeznie néhány lépést. Törölje az erőforrások áthelyezése előtti az SSL-kötés és a privát tanúsítványt a webalkalmazásból. Az App Service-tanúsítvány törlése, nem kell az web app alkalmazásban csak a privát tanúsítványt.

## <a name="move-support"></a>Támogatás áthelyezése

Annak eldöntéséhez, hogy melyik App Service-erőforrások is áthelyezhetők, lásd: helyezze át a támogatási állapota:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>További lépések

Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../resource-group-move-resources.md).
