---
title: Az Azure-előfizetések állapotai
description: A cikk az Azure-előfizetés különféle állapotait ismerteti.
keywords: azure-előfizetés állapota
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270236"
---
# <a name="azure-subscription-states"></a>Az Azure-előfizetések állapotai

A cikk az Azure-előfizetések különféle állapotait ismerteti. Ezek az állapotok az Azure Portal előfizetési területének **Állapot** részén jelennek meg.

| Előfizetés állapota | Leírás |
|-------------| ----------------|
| **Aktív** | Az Azure-előfizetés aktív. Az előfizetés használatával üzembe helyezhet új erőforrásokat, és kezelheti a meglévőket.|
| **Törölve** | Az Azure-előfizetés törölve lett az összes alapul szolgáló erőforrással/adattal együtt. |
| **Letiltva** | Az Azure-előfizetés le van tiltva, és a továbbiakban nem használható Azure-erőforrások létrehozására vagy kezelésére. Ha az előfizetés ebben az állapotban van, a virtuális gépek felszabadulnak, az ideiglenes IP-címek szabadok, a tárterület csak olvasható, és az egyéb szolgáltatások is le vannak tiltva. Az előfizetés letiltásának a következő okai lehetnek: Előfordulhat, hogy lejárt a kreditje. Lehetséges, hogy elérte a költségkeretet. Lejárt esedékességű számlája van. Túllépte a hitelkártyakeretét. Esetleg az előfizetés explicit módon le lett tiltva vagy törölve lett. Az előfizetés típusától függően az előfizetés 1–90 napig lehet letiltva. Ezt követően az előfizetés véglegesen törölve lesz. További információ: [Letiltott Azure-előfizetés újraaktiválása](subscription-disabled.md). |
| **Lejárt** | Azure-előfizetése lejárt, mert törölve lett. A lejárt előfizetés újraaktiválható. További információ: [Letiltott Azure-előfizetés újraaktiválása](subscription-disabled.md).|
| **Lejárt** | Az Azure-előfizetés esetében kiegyenlítetlen tartozás áll fenn. Az előfizetés továbbra is aktív, de a díjak meg nem fizetése az előfizetés letiltásához vezethet. További információ: [Az Azure-előfizetés lejárt esedékességű tartozásának rendezése](resolve-past-due-balance.md). |
