---
title: Az Azure Cloud Services számára elérhető méretek (bővített támogatás)
description: Az Azure Cloud Services (kiterjesztett támogatás) üzemelő példányok számára elérhető méretek
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744398"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Az Azure Cloud Services számára elérhető méretek (bővített támogatás)

Ez a cikk a Cloud Services (kiterjesztett támogatás) példányok számára elérhető virtuálisgép-méreteket ismerteti.   

| Termékváltozat-család |  ACU/mag | 
|---|---|
| [A5 – 7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 – 190 * |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 – 190 * |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 – 190 *
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt. A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Méretek konfigurálása Cloud Serviceshoz (bővített támogatás)

A szolgáltatási modell részeként megadhatja egy szerepkör-példány virtuálisgép-méretét a szolgáltatás definíciós fájljában. A szerepkör mérete határozza meg a CPU-magok számát, a memória kapacitását és a helyi fájlrendszer méretét.

Például a webes szerepkörhöz tartozó példány méretének beállítása a következőre `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Meglévő szerepkör méretének módosítása

Egy meglévő szerepkör méretének módosításához módosítsa a virtuálisgép-méretet a szolgáltatás definíciós fájljába (csdef), csomagolja ki újra a Cloud Service-t, majd telepítse újra. 

## <a name="get-a-list-of-available-sizes"></a>Az elérhető méretek listájának beolvasása 

Az elérhető méretek listájának lekéréséhez tekintse meg az [erőforrás-SKU-lista](https://docs.microsoft.com/rest/api/compute/resourceskus/list) lehetőséget, és alkalmazza a következő szűrőket:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>További lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
