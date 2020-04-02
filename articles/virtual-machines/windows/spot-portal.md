---
title: A portál használata azure spot virtuális gépek üzembe helyezéséhez
description: Ismerje meg, hogyan használhatja az Azure PowerShellt a direkt virtuális gépek üzembe helyezéséhez a költségek csökkentése érdekében.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547383"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Azonnali virtuális gépek telepítése az Azure Portalhasználatával

A [spot virtuális gépek](spot-vms.md) használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek. Ezért a direkt virtuális gépek kiválóan szolgálnak olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A direkt virtuális gépek díjszabása változó, a régió és a termékváltozat alapján. További információ: VM-díjszabás [Linuxra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windowsra.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) A maximális ár beállításáról további információt a Azonnali virtuális gépek – árképzés című témakörben [talál.](spot-vms.md#pricing)

Lehetősége van arra, hogy állítsa be a maximális árat, amelyet hajlandó fizetni, óránként, a virtuális gép. A direktvirtuális gép maximális ára usa dollárban (USD) állítható be, legfeljebb 5 tizedesjegy használatával. Az érték `0.05701`például óránként $0,05701 USD max ár. Ha a maximális árat `-1`állítja be, a virtuális gép nem lesz kizárva az ár alapján. A virtuális gép ára az azonnali aktuális ár vagy egy szabványos virtuális gép ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta áll rendelkezésre.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

A virtuális gépeket használó virtuális gépek létrehozásának folyamata megegyezik a [rövid útmutatóban részletezett eljárással.](quick-create-portal.md) Virtuális gép telepítésekor választhat, hogy egy Azure-direktpéldány.


Az **alapok** lapon a **példány részletei** szakaszban a **Nem** az azure-beli direktpéldányok alapértelmezett használata.

![Képernyőfelvétel a nem et, az Azure-direktpéldány használatát nem használva](media/spot-portal/no.png)

Válassza az **Igen**lehetőséget , a szakasz kibővül, és kiválaszthatja a [kilakoltatás típusát és a kilakoltatási házirendet.](spot-vms.md#eviction-policy) 

![Képernyőfelvétel az igen kiválasztásához, azure-spotpéldány használata](media/spot-portal/yes.png)


## <a name="next-steps"></a>További lépések

Direkt virtuális gépeket is létrehozhat a [PowerShell](spot-powershell.md)használatával.