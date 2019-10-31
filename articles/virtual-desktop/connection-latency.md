---
title: Windows rendszerű virtuális asztali felhasználói kapcsolat késése – Azure
description: A Windows rendszerű virtuális asztali felhasználók kapcsolati késése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
ms.openlocfilehash: 7ef35bdf6c7470d425826d7a30755cc216e69158
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164717"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Felhasználói kapcsolat késésének meghatározása a Windows rendszerű virtuális asztalon

A Windows rendszerű virtuális asztal globálisan elérhető. A rendszergazdák bármilyen Azure-régióban létrehozhatnak virtuális gépeket (VM). A kapcsolatok késése a felhasználók és a virtuális gépek helyétől függően változhat. A Windows rendszerű virtuális asztali szolgáltatások folyamatosan új földrajzi területekre kerülnek a késés javítása érdekében. 
 
A [Windows rendszerű virtuális asztali élmény kalkulátor eszköz](https://azure.microsoft.com/services/virtual-desktop/assessment/) segítségével meghatározhatja a virtuális gépek késésének optimális helyét. Azt javasoljuk, hogy az eszközt két-három hónapon belül használja, hogy az optimális hely ne változzon meg, mivel a Windows rendszerű virtuális asztal kikerül az új területekre. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Windows virtuális asztal az Azure Traffic Manager használja, amely ellenőrzi a felhasználó DNS-kiszolgálójának helyét, hogy megtalálja a legközelebbi Windows virtuális asztali szolgáltatás példányát. Azt javasoljuk, hogy a rendszergazdák a virtuális gépek helyének kiválasztása előtt tekintsék át a felhasználó DNS-kiszolgálójának helyét.

## <a name="next-steps"></a>Következő lépések

- Az optimális késés eléréséhez tekintse meg a [Windows rendszerű virtuális asztali élmény kalkulátor eszközét](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- A díjszabási csomagokért lásd: a [Windows rendszerű virtuális asztali díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- A Windows rendszerű virtuális asztalok üzembe helyezésének első lépéseihez tekintse meg [az oktatóanyagot](tenant-setup-azure-active-directory.md).