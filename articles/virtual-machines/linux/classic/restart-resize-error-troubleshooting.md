---
title: "Virtuális gép újraindításával és átméretezésével kapcsolatos problémák |} Microsoft Docs"
description: "Klasszikus üzembe helyezési elhárítása újraindításával és átméretezésével egy meglévő Linux virtuális gépet az Azure-ban"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 73f2672c-602e-4766-8948-2b180115d299
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: required
ms.date: 01/10/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 7f5718a7e1ab2b14902fa61ffb3053910e584ac6
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Klasszikus üzembe helyezési elhárítása újraindításával és átméretezésével egy meglévő Linux virtuális gépet az Azure-ban
> [!div class="op_single_selector"]
> * [Klasszikus](restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

Indítsa el a leállított Azure virtuális gép (VM), vagy egy meglévő Azure virtuális gép átméretezése megkísérlésekor előforduló gyakori hiba: egy memóriafoglalási hiba. Ez a hiba eredménye, ha a fürt vagy a régió nincs rendelkezésre álló erőforrások vagy nem támogatja a kért Virtuálisgép-méretet.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A Resource Manager-verziójáért lásd: [Itt](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>A gyűjtés auditnaplókat
A hibaelhárítás indítása gyűjteni a naplókat a probléma társított hiba azonosításához.

Az Azure portálon kattintson **Tallózás** > **virtuális gépek** > *a Linux virtuális gép* > **beállítások** > **naplók**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Hiba: Hiba a leállított virtuális gép indításakor
A leállt virtuális gépek elindul, de az beszerzése egy memóriafoglalási hiba próbál.

### <a name="cause"></a>Ok
A leállt virtuális gépek indítására vonatkozó kérelem rendelkezik kísérli meg a következő az eredeti fürt, amelyen a felhőalapú szolgáltatás. A fürt azonban nem rendelkezik szabad lemezterület a kérelem teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Új felhőalapú szolgáltatás létrehozása és társítása vagy egy régiót vagy régió-alapú virtuális hálózatokon, de nem affinitáscsoport.
* Törölje a leállított virtuális Gépet.
* Hozza létre újra a virtuális Gépet az új felhőalapú szolgáltatás, a lemezek használatával.
* Indítsa el a újból létrehozott virtuális Gépet.

Ha hibaüzenetet kap egy új felhőalapú szolgáltatás létrehozása közben, próbálja meg újra később, vagy módosítsa a terület a felhőalapú szolgáltatáshoz.

> [!IMPORTANT]
> Az új felhőszolgáltatás fog egy új nevet és a VIP, úgy kell módosítani ezt az információt a meglévő felhőalapú szolgáltatást használó összes függősége az adatokat.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Hiba: Hiba a meglévő virtuális átméretezése
Meglévő virtuális átméretezése, de egy foglalási hiba próbál.

### <a name="cause"></a>Ok
A kérelem átméretezni a virtuális gép ki lehet megkísérelni, az eredeti fürt, amelyen a felhőalapú szolgáltatás. A fürt azonban nem támogatja a kért Virtuálisgép-méretet.

### <a name="resolution"></a>Megoldás:
Csökkentse a kért Virtuálisgép-méretet, és próbálja megismételni a átméretezési kérelmet.

* Kattintson a **összes tallózása** > **virtuális gépek (klasszikus)** > *a virtuális gép* > **beállítások** > **mérete**. Részletes útmutató: [méretezze át a virtuális gép](https://msdn.microsoft.com/library/dn168976.aspx).

Ha nem lehetséges a virtuális gép méretének csökkentésére, kövesse az alábbi lépéseket:

* Hozzon létre egy új felhőalapú szolgáltatás, amely nem kapcsolódik egy affinitáscsoporthoz, és nincs hozzárendelve virtuális hálózat, amely egy affinitáscsoporthoz van csatolva.
* Hozzon létre egy új, a nagyobb méretű virtuális azt.

A virtuális gépeinek az ugyanazon a felhőalapú szolgáltatás képes egyesíteni. Ha a meglévő felhőalapú szolgáltatást a terület-alapú virtuális hálózathoz társítva, az új felhőszolgáltatás csatlakozhat a meglévő virtuális hálózat.

Ha a meglévő felhőalapú szolgáltatás nem régió-alapú virtuális hálózathoz társítva, majd kell törölni a virtuális gépek a meglévő felhőalapú szolgáltatást, és hozza létre őket az új felhőalapú szolgáltatás, a lemezekről. Fontos azonban, ne feledje, hogy az új felhőszolgáltatás lesz egy új nevet és a VIP, így ezeket a függőségeket, amelyek jelenleg használják ezeket az információkat a meglévő felhőszolgáltatás frissíteni kell.

## <a name="next-steps"></a>Következő lépések
Ha tapasztal, amikor új Linux virtuális gép létrehozása az Azure-ban, tekintse meg [egy új Linux virtuális gép létrehozása az Azure-ban a telepítési problémák elhárításához](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

