---
title: Az Azure-előfizetések állapotai
description: Az Azure-előfizetés különféle állapotait ismerteti.
keywords: azure-előfizetés állapota
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 741e7cf0869e36b5788d0a883a4e982caf041512
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995935"
---
# <a name="azure-subscription-states"></a>Az Azure-előfizetések állapotai
A cikk az Azure-előfizetések különféle állapotait ismerteti. Ezek az állapotok az előfizetési panelek Állapot részén jelennek meg.

| Előfizetés állapota | Leírás |
|-------------| ----------------|
| **Aktív** | Az Azure-előfizetés aktív. Az előfizetés használatával üzembe helyezhet új erőforrásokat, és kezelheti a meglévőket.|
| **Lejárt** | Az Azure-előfizetés esetében kiegyenlítetlen tartozás áll fenn. Az előfizetés továbbra is aktív, de a díjak meg nem fizetése az előfizetés letiltásához vezethet. [Rendezze az Azure-előfizetés lejárt esedékességű tartozását.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Letiltva** | Az Azure-előfizetés le van tiltva, és a továbbiakban nem használható Azure-erőforrások létrehozására vagy kezelésére. Ha az előfizetés ebben az állapotban van, a virtuális gépek felszabadulnak, az ideiglenes IP-címek szabadok, a tárterület csak olvasható, és az egyéb szolgáltatások is le vannak tiltva. Az Azure-előfizetés letiltásának az lehet az oka, hogy lejártak a kreditjei, elérte a költségkeretét, az egyik számlája lejárt esedékességű, túllépte a hitelkártyalimitjét, vagy le lett tiltva/le lett mondva az előfizetés. Az előfizetés típusától és a letiltás okától függően az előfizetés 1–90 napig marad letiltva, mielőtt véglegesen törölve lesz. [Aktiválja újra a letiltott Azure-előfizetést.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Törölve** | Az Azure-előfizetés törölve lett az összes alapul szolgáló erőforrással/adattal együtt. |
