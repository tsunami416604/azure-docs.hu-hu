---
title: Az Azure Resource Manager vCPU-kvóta növeléséhez kérelmek |} A Microsoft Docs
description: Az Azure Resource Manager vCPU-kvóta növelésére irányuló kérelmekről
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076815"
---
# <a name="quota-increase-requests"></a>Kvótanövelési kérések

Virtuális gépek és virtuálisgép-méretezési csoportok erőforrás-kezelő vCPU-kvóták kényszerítettek, az egyes előfizetésekhez, minden régióban két szinten. 

Az első szinten az összes regionális vcpu-k korlát (között az összes Virtuálisgép-sorozat), és a második rétegű a Virtuálisgép-sorozatok Vcpu-korlát (például a D-sorozat vcpu-k). A Virtuálisgép-sorozat az új és meglévő Vcpu használati összege egy új virtuális Gépet, hogy kell telepíteni, amikor nem haladhatja meg a vCPU-kvóta az adott virtuális gép adatsor jóváhagyott. További az összes meglévő és új vCPU-számot telepített összes Virtuálisgép-sorozatok nem haladhatja meg a jóváhagyott az előfizetéshez tartozó összes regionális vcpu-k kvótáját. Ha ezeket a kvótákat valamelyikét túllépése esetén a virtuális gép üzembe helyezése nem engedélyezett lesz.
A Virtuálisgép-sorozatok vcpu-k kvótakorlátozását növekedést is kérhető az Azure Portalról. A Virtuálisgép-sorozatok kvóta emelése automatikusan megnövelje a teljes regionális vcpu-k korlátot is ugyanilyen mértékben csökken. 

Ha egy új előfizetés jön létre, az alapértelmezett teljes regionális vcpu-k nem lehet alapértelmezett vCPU-kvóták minden egyes Virtuálisgép-sorozatok összege. Emiatt egy előfizetésben, de a teljes regionális vcpu-k az összes üzemelő példányhoz nincs elegendő kvótája elegendő kvótával, minden egyes Virtuálisgép-sorozatot, amely számára telepíteni kívánja. Ebben az esetben kell igényelnie explicit módon teljes regionális vcpu-k korlát növelését. Regionális vcpu-k teljes korlát nem lehet hosszabb jóváhagyott kvóta összege az összes Virtuálisgép-sorozatok a régió között.

A kvóták tájékozódhat a [virtuális gép vCPU-kvóták lap](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és [Azure-előfizetés- és Szolgáltatáskorlátok](https://aka.ms/quotalimits) lap. 

