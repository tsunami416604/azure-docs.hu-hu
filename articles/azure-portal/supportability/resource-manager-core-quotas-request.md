---
title: Azure Resource Manager vCPU-kvóta-növelési kérelmek | Microsoft Docs
description: Azure Resource Manager vCPU-kvóta növelésére vonatkozó kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897103"
---
# <a name="quota-increase-requests"></a>Kvótanövelési kérések

A virtuális gépekhez és a virtuálisgép-méretezési csoportokhoz tartozó Resource Manager-vCPU-kvóták minden egyes régióban az egyes előfizetések esetében két szinten érvényesülnek. 

Az első szint a teljes regionális vCPU korlát (az összes virtuálisgép-sorozatban), a második szint pedig a virtuálisgép-sorozat vCPU korlátja (például a D sorozat vCPU). Amikor új virtuális gépet kíván üzembe helyezni, az adott virtuálisgép-sorozat új és meglévő vCPU-használatának összege nem haladhatja meg az adott virtuálisgép-sorozathoz jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozat összes új és meglévő vCPU száma nem haladhatja meg az előfizetéshez jóváhagyott teljes regionális vCPU-kvótát. Ha túllépi a kvótákat, a virtuális gép üzembe helyezése nem lesz engedélyezett.
A virtuálisgép-sorozat vCPU-kvótájának növelését Azure Portal-ból kérheti. A virtuálisgép-sorozat kvótájának növekedése automatikusan megnöveli a teljes regionális vCPU-korlátot ugyanazzal az összeggel. 

Új előfizetés létrehozásakor előfordulhat, hogy az alapértelmezett teljes regionális vCPU nem egyenlő az egyes virtuálisgép-sorozatok alapértelmezett vCPU-kvótáinak összegével. Ez egy olyan előfizetést eredményezhet, amely elegendő kvótával rendelkezik minden olyan virtuálisgép-sorozathoz, amelyet telepíteni kíván, de nincs elég kvóta az összes üzemelő példány regionális vCPU. Ebben az esetben kérelmet kell benyújtania a teljes regionális vCPU-korlát explicit módon történő növeléséhez. A regionális vCPU-határértékek teljes száma nem haladhatja meg a jóváhagyott kvóta összegét a régió összes virtuálisgép-sorozatában.

További információk a [virtuális gépek vCPU kvótái](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és az Azure- [előfizetések és-szolgáltatások korlátozásait](https://aka.ms/quotalimits) ismertető oldalon. 

