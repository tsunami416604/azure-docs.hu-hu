---
title: Gyakori automatikus skálázási minták áttekintése
description: Bemutatjuk a gyakori minták automatikus méretezése az Azure-erőforrás.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: c7084a10aceafcdd1039893b810fcbd8b74b874b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967404"
---
# <a name="overview-of-common-autoscale-patterns"></a>Gyakori automatikus skálázási minták áttekintése
Ez a cikk bemutatja az erőforrás méretezése az Azure-ban használt mintáit.

Az Azure Monitor automatikus skálázása csak érvényes [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/), és [APIManagement-szolgáltatások](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

# <a name="lets-get-started"></a>Lehetővé teszi, hogy első lépései

Ez a cikk azt feltételezi, hogy Ön ismeri az automatikus méretezés. Is [itt kezdhet az erőforrás méretezése][1]. Az alábbiakban gyakori méretezési mintáit.

## <a name="scale-based-on-cpu"></a>A CPU kihasználtságához

Webes alkalmazás (/ VMSS/felhőszolgáltatási szerepkör) rendelkezik, és

- Kívánt méretezési out/horizontális leskálázási alapján Processzor.
- Emellett szeretné biztosítani a példányok minimális száma.
- Emellett szeretné arról, hogy a példányok is maximális korlát beállítva.

![A CPU kihasználtságához][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Hétköznapokon vs hétvégén eltérően méretezése

Webes alkalmazás (/ VMSS/felhőszolgáltatási szerepkör) rendelkezik, és

- (Hétköznapokon) alapértelmezés szerint a 3-példányokat szeretne.
- Nem várt adatforgalom hétvégén, és ezért szeretne hétvégén 1 példány lefelé skálázhatja.

![Hétköznapokon vs hétvégén eltérően méretezése][3]

## <a name="scale-differently-during-holidays"></a>Munkaszüneti napok során eltérően méretezése

Webes alkalmazás (/ VMSS/felhőszolgáltatási szerepkör) rendelkezik, és

- Szeretné felfelé és lefelé méretezését alapértelmezés szerint a CPU-használat alapján
- Azonban az ünnepi időszakban (vagy adott nap fontos üzleti) során érdemes felülírja az alapértelmezett értékeket, és további kapacitás a rendelkezésére.

![Méretezési eltér a munkaszüneti napok][4]

## <a name="scale-based-on-custom-metric"></a>Méretezési csoport egyéni metrika alapján

Rendelkezik egy webes előtérrendszert és a egy API-szint, amely kommunikál a háttérrendszerrel.

- Az egyéni események az előtér-alapú API-réteg méretezésére (Példa: méretezésére a fizetési folyamat a bevásárlókocsiba elemeinek száma alapján)

![Méretezési csoport egyéni metrika alapján][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png
