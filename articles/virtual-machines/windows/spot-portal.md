---
title: Az Azure spot virtuális gépek üzembe helyezése a portál használatával
description: Megtudhatja, hogyan helyezhet üzembe helyszíni virtuális gépeket a költségek csökkentése érdekében a Azure PowerShell használatával.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158978"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Előzetes verzió: helyszíni virtuális gépek üzembe helyezése a Azure Portal használatával

A [helyszíni virtuális gépek](spot-vms.md) használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](spot-vms.md#pricing).

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. A `0.05701`érték például egy óránként $0,05701 USD maximális díj. Ha a maximális árat `-1`értékre állítja, a virtuális gép ára nem lesz kizárva. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.

> [!IMPORTANT]
> A helyszíni példányok jelenleg nyilvános előzetes verzióban érhetők el.
> Ez az előzetes verzió nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>


## <a name="create-the-vm"></a>Virtuális gép létrehozása

A helyszíni virtuális gépeket használó virtuális gépek létrehozásának folyamata [megegyezik a rövid](quick-create-portal.md)útmutatóban részletezett eljárással. A virtuális gépek üzembe helyezésekor dönthet úgy, hogy egy Azure spot-példányt használ.


Az alapbeállítások lap **példány részletei** **szakaszában a** **nem** érték az Azure spot-példányok használatának alapértelmezett értéke.

![Képernyőfelvétel a nem lehetőség kiválasztásához, ne használjon Azure spot-példányt](media/spot-portal/no.png)

Válassza az **Igen**, a szakasz kibontása lehetőséget, és kiválaszthatja a [kizárási típusát és a kizárási szabályzatot](spot-vms.md#eviction-policy). 

![Képernyőfelvétel az igen lehetőség kiválasztásához, Azure spot-példány használata](media/spot-portal/yes.png)


## <a name="next-steps"></a>Következő lépések

A [PowerShell](spot-powershell.md)használatával helyszíni virtuális gépeket is létrehozhat.