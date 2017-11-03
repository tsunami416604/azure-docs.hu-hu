---
title: "Közös automatikus skálázás minták áttekintése |} Microsoft Docs"
description: "Ismerje meg a közös minták automatikus méretezési némelyike az erőforrás az Azure-ban."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: fce51546e041c8989d813c3935e058c52b38ba77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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