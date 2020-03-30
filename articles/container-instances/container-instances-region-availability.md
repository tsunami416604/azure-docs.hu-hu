---
title: Erőforrás elérhetősége régiónként
description: Számítási és memória-erőforrások rendelkezésre állása az Azure Container Instances szolgáltatás különböző Azure-régiókban.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: f429a165fe26cc9fc7aa973231f5a77163feef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247136"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Az Azure Container-példányok erőforrások elérhetősége az Azure-régiókban

Ez a cikk részletezi az Azure Container Instances számítási, memória- és tárolási erőforrások rendelkezésre állását az Azure-régiókban és a cél operációs rendszer szerint. 

A bemutatott értékek a [tárolócsoport](container-instances-container-groups.md)telepítésénként rendelkezésre álló maximális erőforrások. Az értékek a közzététel időpontjában aktuálisak. 

> [!NOTE]
> Az erőforráskorlátokon belül létrehozott tárolócsoportok a központi telepítési régión belüli rendelkezésre állástól függenek. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Egy ilyen központi telepítési hiba csökkentése érdekében próbálja meg alacsonyabb erőforrás-beállításokkal telepíteni a példányokat, vagy próbálja meg később a központi telepítést.

A kvótákról és a központi telepítések egyéb korlátairól az [Azure Container-példányok kvótái és korlátai](container-instances-quotas.md)című témakörben talál.

## <a name="availability---general"></a>Elérhetőség - Általános

A következő régiók és maximális erőforrások állnak rendelkezésre a Linux és [a támogatott](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-alapú tárolók tárolócsoportjai számára.

| Régiók | Operációs rendszer | Max CPU (Max. CPU) | Maximális memória (GB) | Tárhely (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brazília Déli, Kanada Közép-, Közép-India, USA középső régiója, Usa keleti régiója, USA keleti régiója 2, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli, nyugat-európai, USA nyugati régiója 2 | Linux | 4 | 16 | 50 |
| Ausztrália Kelet, Japán Kelet | Linux | 2 | 8 | 50 |
| USA északi középső régiója | Linux | 2 | 3.5 | 50 |
| Brazília Dél, Japán Kelet, Nyugat-Európa | Windows | 4 | 16 | 20 |
| USA keleti része, USA nyugati része | Windows | 4 | 14 | 20 |
| Kelet-Ausztrália, Kanada Közép-, Közép-India, USA középső része, Kelet-Ázsia, USA keleti régiója 2, USA északi középső régiója, Észak-Európa, USA középső középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli, USA nyugati régiója 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Elérhetőség – Windows Server 2019 LTSC, 1809-es telepítések (előzetes verzió)

A következő régiók és maximális erőforrások érhetők el a Windows Server 2019-alapú tárolókkal rendelkező tárolócsoportok számára (előzetes verzió).

| Régiók | Operációs rendszer | Max CPU (Max. CPU) | Maximális memória (GB) | Tárhely (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Kelet-Ausztrália, Dél-Brazília, Kanada Közép-, Közép-India, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA északkeleti középső része, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság Déli, Nyugat-Európa | Windows | 4 | 16 | 20 |
| USA keleti része 2, USA nyugati része 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Rendelkezésre állás – Virtuális hálózat telepítése

A következő régiók és maximális erőforrások érhetők el az [Azure virtuális hálózatban](container-instances-vnet.md)üzembe helyezett tárolócsoport számára.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Elérhetőség - GPU-erőforrások (előzetes verzió)

A következő régiók és a maximális erőforrások érhetők el a [GPU-erőforrásokkal](container-instances-gpu.md) (előzetes verzió) üzembe helyezett tárolócsoport számára.

> [!IMPORTANT]
> A GPU-erőforrások csak kérésre állnak rendelkezésre. A GPU-erőforrásokhoz való hozzáférés kéréséhez nyújtson be egy [Azure-támogatási kérelmet.][azure-support]

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>További lépések

Tudassa a csapattal, hogy további régiókat vagy az erőforrások elérhetőségének növelését szeretné-e látni [aka.ms/aci/feedback.](https://aka.ms/aci/feedback)

A tárolópéldányok telepítésének elhárításáról az [Azure Container Instances alkalmazással kapcsolatos telepítési problémák elhárítása című témakörben](container-instances-troubleshooting.md)talál további információt.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest