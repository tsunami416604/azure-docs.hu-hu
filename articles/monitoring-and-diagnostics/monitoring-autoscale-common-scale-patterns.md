---
title: Közös automatikus skálázás minták áttekintése
description: Ismerje meg a közös minták automatikus méretezési némelyike az erőforrás az Azure-ban.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 84727ec3694f64d40ad002a248a255df9074d7f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263261"
---
# <a name="overview-of-common-autoscale-patterns"></a>Közös automatikus skálázás minták áttekintése
Ez a cikk ismerteti az egyes közös mintázatokat az erőforrás méretezése az Azure-ban.

Az Azure a figyelő automatikus méretezési csak a virtuális gép méretezési készletek (VMSS), a felhőszolgáltatások, az app service-csomagokról és a app service Environment-környezetek vonatkozik. 

# <a name="lets-get-started"></a>Lehetővé teszi, hogy első lépései

Ez a cikk feltételezi, hogy Ön ismeri a automatikus méretezési. Is [első lépések az erőforrás méretezése][1]. Az alábbiakban néhány gyakori méretezési mintázatokat.

## <a name="scale-based-on-cpu"></a>A skála CPU alapján

A webes alkalmazás (/ VMSS/felhő szerepkör-szolgáltatás) és 

- Kívánt méretezési out/skála alapján CPU.
- Emellett szeretné gondoskodjon arról, hogy a példányok minimális száma. 
- Emellett szeretne biztosítani a maximális korlát értékre révén példányainak számát.

![A skála CPU alapján][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Hétköznapokon vs hétvégén másképp méretezése

A webes alkalmazás (/ VMSS/felhő szerepkör-szolgáltatás) és

- (A hétköznapokon) alapértelmezés szerint a 3 példányokat szeretne.
- Nem várt forgalom hétvégén, és ezért szeretné hétvégén le 1 példány méretezése.

![Hétköznapokon vs hétvégén másképp méretezése][3]

## <a name="scale-differently-during-holidays"></a>Méretezhető másképp ünnepek során

A webes alkalmazás (/ VMSS/felhő szerepkör-szolgáltatás) és 

- Kívánt felfelé vagy lefelé méretezési alapértelmezés szerint a CPU-használat alapján
- Azonban során ünnepi (vagy adott nap, a vállalkozása számára fontos) szeretne felülírja az alapértelmezett értékeket, és további kapacitás a rendelkezésére.

![Skála eltérően a munkaszüneti napokat is][4]

## <a name="scale-based-on-custom-metric"></a>Egyéni metrika alapuló méretezési

Rendelkezik egy előtér-webkiszolgáló és a háttérkiszolgáló kommunikáló API réteget. 

- Az API-réteg elérhető az előtérben lévő egyéni események alapján méretezésére (Példa: a bevásárlókocsiban elemek száma. a kivétel folyamat méretezésére)

![Egyéni metrika alapuló méretezési][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png