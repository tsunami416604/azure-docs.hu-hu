---
title: Azure bizalmas számítástechnika – GYIK
description: Válaszok az Azure bizalmas számítástechnikagal kapcsolatos gyakori kérdésekre.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 2bd9430fc6f48d72faa2c1850af0bb8432a7c5f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149501"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Gyakran ismételt kérdések az Azure bizalmas számítástechnikai szolgáltatásáról

Ez a cikk a [bizalmas számítási feladatok Azure-on](overview.md)való futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az [MSDN webhelyen](https://azure.microsoft.com/support/forums/)található Azure-fórumokra, és stack overflow. Felteheti a problémát ezekben a fórumokon, vagy közzéteheti [ @AzureSupport a Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérést is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás lapon](https://azure.microsoft.com/support/options/)válassza a támogatás lekérése lehetőséget.

## <a name="confidential-computing-virtual-machines"></a>Bizalmas számítástechnika Virtual Machines<a id="vm-faq"></a>

1. **Hogyan kezdheti meg a DCsv2 sorozatú virtuális gépek üzembe helyezését?**

   Az alábbi módokon telepítheti a DCsv2 virtuális gépeket:
   - [Azure Resource Manager sablon](../virtual-machines/windows/template-description.md) használata
   - A [Azure Portal](https://portal.azure.com/#create/hub)
   - Az [Azure bizalmas számítási (virtuális gép)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Piactéri megoldás sablonjában. A Piactéri megoldás sablonja segít korlátozni az ügyfeleket a támogatott forgatókönyvekre (régiók, képek, rendelkezésre állás, lemez titkosítás). 

1. **Az operációsrendszer-lemezképek az Azure bizalmas számítástechnikai szolgáltatásával működnek?**

   Nem. A virtuális gépeket csak 2. generációs virtuális gépekre lehet telepíteni. Az Ubuntu Server 18,04, az Ubuntu Server 16,04 és a Windows Server 2016 Datacenter támogatja a 2. generációs támogatást. További információ a 2. generációs virtuális gépekről [Linux](../virtual-machines/linux/generation-2.md) és [Windows](../virtual-machines/windows/generation-2.md) rendszeren

1. **A DCsv2 virtuális gépek szürkén jelennek meg a portálon, és nem tudok kiválasztani egyet**

   A virtuális gép melletti információs buborék alapján különböző műveletek szükségesek:
    -   **UnsupportedGeneration**: módosítsa a virtuális gép rendszerképének generációját "Gen2" értékre.
    -   **NotAvailableForSubscription** : a régió még nem érhető el az előfizetéséhez. Válasszon a rendelkezésre álló régiók közül.
    -   **InsufficientQuota**: [hozzon létre egy támogatási kérést a kvóta növeléséhez](../azure-portal/supportability/per-vm-quota-requests.md). Az ingyenes próbaverziós előfizetések esetében nincs kvóta a bizalmas számítástechnikai virtuális gépekhez. 

1. **A DCsv2 virtuális gépek nem jelennek meg, amikor megpróbálok megkeresni őket a portál méretének választójában**

   Győződjön meg arról, hogy egy elérhető régiót jelölt ki. Győződjön meg arról is, hogy a méret választóban az "összes szűrő törlése" lehetőséget választotta. 

1. **Mi a különbség a DCsv2 és a DC sorozatú virtuális gépek között?**

   A DC sorozatú virtuális gépek régebbi 6 Magos Intel processzorokon futnak a SGX ENKLÁVÉHOZ. Ezek kevesebb teljes memóriával rendelkeznek, kevesebb EPC (enklávé lap ketrec) memóriával rendelkeznek, és kevesebb régióban érhetők el. Ezek a virtuális gépek csak az USA keleti és európai nyugati régiójában érhetők el, két méretben: Standard_DC2s és Standard_DC4s. Nem fognak megjelenni a GA-ban, és csak a [bizalmas számítási DC sorozatú VM [előzetes verzió] Marketplace-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) példányban helyezhetők üzembe.

1. **Globálisan elérhetők a DCsv2 virtuális gépek?**

   Nem, ezek a virtuális gépek csak a kiválasztott régiókban érhetők el. A legújabb elérhető régiókat a [termékek régiók szerint lapon](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) találja. 

1. **Hogyan telepíteni az Open enklávé SDK-t?**
   
   Az OE SDK számítógépeken az Azure-ban vagy a helyszíni gépen való telepítésével kapcsolatos útmutatásért kövesse az [Open ENKLÁVÉ SDK githubon](https://github.com/openenclave/openenclave)megjelenő utasításokat.
     
   Az Open enklávé SDK GitHubon az operációs rendszerre vonatkozó telepítési útmutatót is használhatja:
     - [Az OE SDK telepítése Windows rendszeren](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Az OE SDK telepítése Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Az OE SDK telepítése Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
