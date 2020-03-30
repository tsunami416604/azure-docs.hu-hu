---
title: Windows virtuális asztali felhasználói kapcsolat késése - Azure
description: Kapcsolatkés a Windows virtuális asztali felhasználók számára.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128191"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>A felhasználói kapcsolat késésének meghatározása a Windows Virtuális asztalban

A Windows virtuális asztal globálisan elérhető. A rendszergazdák virtuális gépeket (VM-eket) hozhatnak létre bármely Azure-régióban, ahol csak akarnak. A kapcsolat késése a felhasználók és a virtuális gépek helyétől függően változhat. A Windows virtuális asztali szolgáltatásai folyamatosan jelennek meg az új földrajzi területeken a késés javítása érdekében. 
 
A [Windows virtuális asztali élmény becslő eszközével](https://azure.microsoft.com/services/virtual-desktop/assessment/) meghatározhatja a virtuális gépek késésének optimalizálásához legmegfelelőbb helyet. Javasoljuk, hogy két-három havonta használja az eszközt, hogy megbizonyosodjon arról, hogy az optimális hely nem változott, amikor a Windows virtuális asztal új területekre kerül. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Windows virtuális asztal az Azure Traffic Manager t használja, amely ellenőrzi a felhasználó DNS-kiszolgálójának helyét a legközelebbi Windows virtuális asztali szolgáltatáspéldány megkereséséhez. Javasoljuk, hogy a rendszergazdák tekintse át a felhasználó DNS-kiszolgálójának helyét, mielőtt kiválasztanák a virtuális gépek helyét.

## <a name="next-steps"></a>További lépések

- Az optimális késés legjobb helyének ellenőrzéséhez tekintse meg a [Windows virtuális asztali élmény becslőeszközét.](https://azure.microsoft.com/services/virtual-desktop/assessment/)
- A díjcsomagokról a [Windows virtuális asztal díjszabása ( Windows virtuális asztal díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/)) oldalon található.
- A Windows virtuális asztal központi telepítésének megkezdéséhez tekintse meg [oktatóanyagunkat.](tenant-setup-azure-active-directory.md)