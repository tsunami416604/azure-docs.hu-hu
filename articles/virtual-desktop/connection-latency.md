---
title: Windows rendszerű virtuális asztali felhasználói kapcsolat késése – Azure
description: A Windows rendszerű virtuális asztali felhasználók kapcsolati késése.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 403cf584c79bc0a166054ae36c9d2ea50e4b9d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008729"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Felhasználói kapcsolat késésének meghatározása a Windows rendszerű virtuális asztalon

A Windows rendszerű virtuális asztal globálisan elérhető. A rendszergazdák bármilyen Azure-régióban létrehozhatnak virtuális gépeket (VM). A kapcsolatok késése a felhasználók és a virtuális gépek helyétől függően változhat. A Windows rendszerű virtuális asztali szolgáltatások folyamatosan új földrajzi területekre kerülnek a késés javítása érdekében.

A [Windows rendszerű virtuális asztali élmény kalkulátor eszköz](https://azure.microsoft.com/services/virtual-desktop/assessment/) segítségével meghatározhatja a virtuális gépek késésének optimális helyét. Azt javasoljuk, hogy az eszközt két-három hónapon belül használja, hogy az optimális hely ne változzon meg, mivel a Windows rendszerű virtuális asztal kikerül az új területekre.

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Windows virtuális asztal az Azure Traffic Manager használja, amely ellenőrzi a felhasználó DNS-kiszolgálójának helyét, hogy megtalálja a legközelebbi Windows virtuális asztali szolgáltatás példányát. Azt javasoljuk, hogy a rendszergazdák a virtuális gépek helyének kiválasztása előtt tekintsék át a felhasználó DNS-kiszolgálójának helyét.

## <a name="next-steps"></a>Következő lépések

- Az optimális késés eléréséhez tekintse meg a [Windows rendszerű virtuális asztali élmény kalkulátor eszközét](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- A díjszabási csomagokért lásd: a [Windows rendszerű virtuális asztali díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- A Windows rendszerű virtuális asztalok üzembe helyezésének első lépéseihez tekintse meg [az oktatóanyagot](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).