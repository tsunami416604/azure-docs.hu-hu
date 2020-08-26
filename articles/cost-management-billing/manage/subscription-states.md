---
title: Az Azure-előfizetések állapotai
description: A cikk az Azure-előfizetés különféle állapotait ismerteti.
keywords: azure-előfizetés állapota
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 70d03b4aeeaecee522552b381ce1309485719dc6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686683"
---
# <a name="azure-subscription-states"></a>Az Azure-előfizetések állapotai

A cikk az Azure-előfizetések különféle állapotait ismerteti. Ezek az állapotok az Azure Portal előfizetési területének **Állapot** részén jelennek meg.

| Előfizetés állapota | Leírás |
|-------------| ----------------|
| **Aktív**/**Engedélyezve** | Az Azure-előfizetés aktív. Az előfizetés használatával üzembe helyezhet új erőforrásokat, és kezelheti a meglévőket.<br><br>Minden művelet (PUT, PATCH, DELETE, POST, GET) elérhető azon erőforrás-szolgáltatók számára, amelyek [regisztrálva vannak az előfizetéshez](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Törölve** | Az Azure-előfizetés törölve lett az összes alapul szolgáló erőforrással/adattal együtt.<br><br>Nincsenek elérhető műveletek. |
| **Letiltva** | Az Azure-előfizetés le van tiltva, és a továbbiakban nem használható Azure-erőforrások létrehozására vagy kezelésére. Ha az előfizetés ebben az állapotban van, a virtuális gépek felszabadulnak, az ideiglenes IP-címek szabadok, a tárterület csak olvasható, és az egyéb szolgáltatások is le vannak tiltva. Az előfizetés letiltásának a következő okai lehetnek: Előfordulhat, hogy lejárt a kreditje. Lehetséges, hogy elérte a költségkeretet. Lejárt esedékességű számlája van. Túllépte a hitelkártyakeretét. Esetleg az előfizetés explicit módon le lett tiltva vagy törölve lett. Az előfizetés típusától függően az előfizetés 1–90 napig lehet letiltva. Ezt követően az előfizetés véglegesen törölve lesz. További információ: [Letiltott Azure-előfizetés újraaktiválása](subscription-disabled.md).<br><br>Az erőforrások létrehozására vagy frissítésére szolgáló műveletek (PUT, PATCH) le vannak tiltva. A műveletet végrehajtó műveletek (POST) szintén le vannak tiltva. Az erőforrások lekérhetők vagy törölhetők (GET, DELETE). Az erőforrások továbbra is elérhetők. |
| **Lejárt** | Azure-előfizetése lejárt, mert törölve lett. A lejárt előfizetés újraaktiválható. További információ: [Letiltott Azure-előfizetés újraaktiválása](subscription-disabled.md).<br><br>Az erőforrások létrehozására vagy frissítésére szolgáló műveletek (PUT, PATCH) le vannak tiltva. A műveletet végrehajtó műveletek (POST) szintén le vannak tiltva. Az erőforrások lekérhetők vagy törölhetők (GET, DELETE).|
| **Lejárt** | Az Azure-előfizetés esetében kiegyenlítetlen tartozás áll fenn. Az előfizetés továbbra is aktív, de a díjak meg nem fizetése az előfizetés letiltásához vezethet. További információ: [Az Azure-előfizetés lejárt esedékességű tartozásának rendezése](resolve-past-due-balance.md).<br><br>Minden művelet elérhető. |
| **Figyelmeztetett** | Az Azure-előfizetés figyelmeztetett állapotban van, és bár a megszokott módon használható, a rendszer rövidesen letiltja, ha a figyelmeztetés okát nem vizsgálja ki. Az előfizetés többek között akkor lehet figyelmeztetett állapotban, ha lejárt esedékességű, a felhasználó lemondta vagy az előfizetés lejárt.<br><br>Minden művelet elérhető. |
