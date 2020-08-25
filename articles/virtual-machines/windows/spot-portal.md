---
title: Az Azure spot virtuális gépek üzembe helyezése a portál használatával
description: A Azure PowerShell használata a helyszíni virtuális gépek üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: ef5fa0aafca1312480f51614a1ba1692c09a13b8
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816575"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Helyszíni virtuális gépek üzembe helyezése a Azure Portal használatával

A [helyszíni virtuális gépek](../spot-vms.md) használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](../spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. Az érték például a `0.05701` maximális díj $0,05701 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

A virtuális gép kizárásakor lehetősége van törölni a virtuális gépet és a mögöttes lemezt, vagy felszabadítani a virtuális gépet, hogy később újra lehessen indítani.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

A helyszíni virtuális gépeket használó virtuális gépek létrehozásának folyamata [megegyezik a rövid](quick-create-portal.md)útmutatóban részletezett eljárással. A virtuális gépek üzembe helyezésekor dönthet úgy, hogy egy Azure spot-példányt használ.


Az alapbeállítások lap **példány részletei** **szakaszában a** **nem** érték az Azure spot-példányok használatának alapértelmezett értéke.

![Képernyőfelvétel a nem lehetőség kiválasztásához, ne használjon Azure spot-példányt](media/spot-portal/no.png)

Válassza az **Igen**, a szakasz kibontása lehetőséget, és kiválaszthatja a [kizárási típusát és a kizárási szabályzatot](../spot-vms.md#eviction-policy). 

![Képernyőfelvétel az igen lehetőség kiválasztásához, Azure spot-példány használata](media/spot-portal/yes.png)


## <a name="next-steps"></a>További lépések

A [PowerShell](spot-powershell.md)használatával helyszíni virtuális gépeket is létrehozhat.