---
title: "Végezzen hibaelhárítást az Azure virtuális hálózati átjáró és a kapcsolatok - portál |} Microsoft Docs"
description: "Ez a lap ismerteti, hogyan Azure hálózati figyelőt portál hibaelhárítása"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: fb8f60eea0eaa24649f584ae4183aa8e552a433e
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>Virtuális hálózati átjáró és az Azure portálról hálózati figyelőt alkalmazó kapcsolatok hibáinak elhárítása

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Hálózati figyelőt sok képességeket biztosít, a hálózati erőforrások az Azure-ban ismertetése vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. Erőforrások hibaelhárítása hívható a portálon, a PowerShell, a CLI vagy a REST API-n keresztül. Meghívásakor, a hálózati figyelőt megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és visszaadja az eredményekről.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

Látogasson el a támogatott átjáró típusok, listája [támogatott átjárótípusok](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Áttekintés

Erőforrás hibakeresési lehetővé teszi a virtuális hálózati átjárók és kapcsolatok felmerülő problémák hibaelhárításához. A kérelem erőforrás hibáinak elhárításához, amikor folyamatban van-e a naplók lekérdezett és megvizsgálni. Ha vizsgálat befejeződött, a rendszer visszairányítja az eredményeket. Erőforrás-hibaelhárítási kérelmek hosszú ideig futniuk kér, amely több percig is beletelhet visszaküldeni az eredményt. A naplók hibaelhárítási egy tárolót, a megadott tárfiók tárolja.

## <a name="troubleshoot-a-gateway-or-connection"></a>Átjáró vagy csatlakozási hibáinak elhárítása

1. Keresse meg a [Azure-portálon](https://portal.azure.com) kattintson **hálózati** > **hálózati figyelőt** > **VPN diagnosztika**
2. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**.
3. Az erőforrás állapotával kapcsolatos adatokat erőforrás hibakeresési ad vissza. Felülvizsgálandó tárfiókba megfelelő naplókat is menti. Kattintson a **Tárfiók** jelölje be a tárfiók.
4. Az a **tárfiókok** panelen válasszon ki egy meglévő tárfiókot használ, vagy kattintson **+ tárfiók** számára hozzon létre egy újat.
5. Az a **tárolók** panelen válasszon ki egy meglévő tárolót, vagy kattintson **+ tároló** egy új tároló létrehozásához. Ha végzett a kattintson **kiválasztása**
6. Válassza ki a hibakeresésre, és kattintson az átjáró és a kapcsolat erőforrások **hibaelhárítás indítása**

Ha több erőforrás van kiválasztva, hibaelhárítási van egyidejű futtatását átjáró-erőforrásokat. A kapcsolat nem futtatható, és egyszerre átjáró kapcsolódik. Javasoljuk, hogy az átjárókkal kapcsolatos problémák elhárítása először, mert a kapcsolat hibaelhárítási hosszabb folyamat. Erőforrás VPN diagnosztika futtatása közben a **HIBAELHÁRÍTÁSI állapot** oszlop megjelenik egy állapotának **fut**

Amikor végzett, az Állapot oszlopban vált **kifogástalan** vagy **nem kifogástalan állapotú**.

![teljes hibaelhárítása][2]

## <a name="understanding-the-results"></a>Az eredmények ismertetése

Az a **részletek** szakasz az ablak a **állapot** lapon állapotát jeleníti meg az utolsó hibaelhárítási futtatása a kiválasztott erőforrás. A legújabb diagnosztikai eredményei látható xx perc után utolsó futtatása.

|Tulajdonság  |Leírás  |
|---------|---------|
|Erőforrás     | Az erőforrás mutató hivatkozást.        |
|Tárolási elérési útja     |  A tárfiók és tároló, amely tartalmazza a naplók (Ha bármelyik keletkezett a futtatás során) elérési útja. Ez a beállítás nem maradnak, ha kilép a portálon.        |
|Összegzés     | Az erőforrás állapotának összegzését.        |
|Részletek     | Az erőforrás állapota részletes tájékoztatást.        |
|Legutóbb futtatva     | Az az idő az utolsó idejű hibaelhárításhoz lett futott.        |


A **művelet** lapon általános útmutatást nyújt a probléma megoldásához. Is művelet az a probléma, ha egy hivatkozás által biztosított további útmutatást. Abban az esetben nincs további útmutatás, ha a válasz biztosít nyissa meg a támogatási esetet URL-címét.  A válasz és tartalmát képező tulajdonságaival kapcsolatos további információkért látogasson el a [hálózati figyelő hibaelhárítása – áttekintés](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>További lépések

Ha a beállítások módosítása, hogy stop VPN-kapcsolatot, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/virtual-network-manage-nsg-arm-portal.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági szabályok, amelyek lehet, hogy a szóban forgó.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
