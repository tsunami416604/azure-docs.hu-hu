---
title: Az Azure-előfizetések állapotai
description: Az Azure-előfizetés különféle állapotait ismerteti.
keywords: azure-előfizetés állapota
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200537"
---
# <a name="azure-subscription-states"></a>Az Azure-előfizetések állapotai
A cikk az Azure-előfizetések különféle állapotait ismerteti. Ezek az állapotok az előfizetési panelek Állapot részén jelennek meg.

| Előfizetés állapota | Leírás |
|-------------| ----------------|
| **Aktív** | Az Azure-előfizetés aktív. Az előfizetés használatával üzembe helyezhet új erőforrásokat, és kezelheti a meglévőket.|
| **Lejárt** | Az Azure-előfizetés esetében kiegyenlítetlen tartozás áll fenn. Az előfizetés továbbra is aktív, de a díjak meg nem fizetése az előfizetés letiltásához vezethet. [Rendezze az Azure-előfizetés lejárt esedékességű tartozását.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Letiltva** | Az Azure-előfizetés le van tiltva, és a továbbiakban nem használható Azure-erőforrások létrehozására vagy kezelésére. Ha az előfizetés ebben az állapotban van, a virtuális gépek felszabadulnak, az ideiglenes IP-címek szabadok, a tárterület csak olvasható, és az egyéb szolgáltatások is le vannak tiltva. Az Azure-előfizetés letiltásának az lehet az oka, hogy lejártak a kreditjei, elérte a költségkeretét, az egyik számlája lejárt esedékességű, túllépte a hitelkártyalimitjét, vagy le lett tiltva/le lett mondva az előfizetés. Az előfizetés típusától és a letiltás okától függően az előfizetés 1–90 napig marad letiltva, mielőtt véglegesen törölve lesz. [Aktiválja újra a letiltott Azure-előfizetést.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Törölve** | Az Azure-előfizetés törölve lett az összes alapul szolgáló erőforrással/adattal együtt. |
