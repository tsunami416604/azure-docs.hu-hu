---
title: Az Azure spot virtuális gépek üzembe helyezése a portál használatával
description: A Azure PowerShell használata a helyszíni virtuális gépek üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0da650646c35a9a663dd29589f963d23cbe552cf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828490"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Helyszíni virtuális gépek üzembe helyezése a Azure Portal használatával

A [helyszíni virtuális gépek](spot-vms.md) használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. Az érték például a `0.05701` maximális díj $0,05701 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

A virtuális gép kizárásakor lehetősége van törölni a virtuális gépet és a mögöttes lemezt, vagy felszabadítani a virtuális gépet, hogy később újra lehessen indítani.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

A virtuális gépek üzembe helyezésekor dönthet úgy, hogy egy Azure spot-példányt használ.


Az alapbeállítások lap **példány részletei** **szakaszában a** **nem** érték az Azure spot-példányok használatának alapértelmezett értéke.

![Képernyőfelvétel a nem lehetőség kiválasztásához, ne használjon Azure spot-példányt](./media/spot-portal/no.png)

Ha az **Igen**lehetőséget választja, a szakasz kibontása után kiválaszthatja a [kizárási típusát és a kizárási szabályzatot](spot-vms.md#eviction-policy). 

![Képernyőfelvétel az igen lehetőség kiválasztásához, Azure spot-példány használata](./media/spot-portal/yes.png)

A díjszabást és a kizárási arányt más hasonló régiókkal is összehasonlíthatja, ha kiválasztja a **díjszabási előzmények megtekintése lehetőséget, és összehasonlítja az árakat a közeli régiókban**.

Ebben a példában a közép-Kanada középső régiója kevésbé költséges, és alacsonyabb a kizárási arány, mint az USA keleti régiója.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Képernyőfelvétel a régió lehetőségeiről a díjszabási és kizárási arányok különbségével.":::

A régiót úgy változtathatja meg, hogy kiválasztja a legmegfelelőbbet az Ön számára, majd az **OK gombra**kattint.

## <a name="simulate-an-eviction"></a>Kizárás szimulálása

A Direktszínű virtuális gépek [kizárását szimulálhatja](/rest/api/compute/virtualmachines/simulateeviction) , így tesztelheti, hogy az alkalmazás milyen jól fogja kizárni a hirtelen kizárást. 

Cserélje le a következőt az adataira: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Következő lépések

Helyszíni virtuális gépeket a [PowerShell](./windows/spot-powershell.md), a [CLI](./linux/spot-cli.md)vagy a [sablon](./linux/spot-template.md)használatával is létrehozhat.
