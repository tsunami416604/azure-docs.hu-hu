---
title: "Virtuális gép újraindításával és átméretezésével kapcsolatos problémák |} Microsoft Docs"
description: "Klasszikus üzembe helyezési elhárítása újraindításával és átméretezésével egy meglévő Windows rendszerű virtuális gép az Azure-ban"
services: virtual-machines-windows
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 11/03/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: bed5da25042d29983bad9a80cd44bdd7df261c2e
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Klasszikus üzembe helyezési elhárítása újraindításával és átméretezésével egy meglévő Windows rendszerű virtuális gép az Azure-ban
> [!div class="op_single_selector"]
> * [Klasszikus](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Indítsa el a leállított Azure virtuális gép (VM), vagy egy meglévő Azure virtuális gép átméretezése megkísérlésekor előforduló gyakori hiba: egy memóriafoglalási hiba. Ez a hiba eredménye, ha a fürt vagy a régió nincs rendelkezésre álló erőforrások vagy nem támogatja a kért Virtuálisgép-méretet.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>A gyűjtés auditnaplókat
A hibaelhárítás indítása gyűjteni a naplókat a probléma társított hiba azonosításához.

Az Azure portálon kattintson **Tallózás** > **virtuális gépek** > *a Windows rendszerű virtuális gép* > **beállítások** > **naplók**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Hiba: Hiba a leállított virtuális gép indításakor
A leállt virtuális gépek elindul, de az beszerzése egy memóriafoglalási hiba próbál.

### <a name="cause"></a>Ok
A leállt virtuális gépek indítására vonatkozó kérelem rendelkezik kísérli meg a következő az eredeti fürt, amelyen a felhőalapú szolgáltatás. A fürt azonban nem rendelkezik szabad lemezterület a kérelem teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Új felhőalapú szolgáltatás létrehozása és társítása vagy egy régiót vagy régió-alapú virtuális hálózatokon, de nem affinitáscsoport.
* Törölje a leállított virtuális Gépet.
* Hozza létre újra a virtuális Gépet az új felhőalapú szolgáltatás, a lemezek használatával.
* Indítsa el a újból létrehozott virtuális Gépet.

Ha hibaüzenetet kap, amikor megpróbált létrehozni egy új felhőalapú szolgáltatás, vagy később próbálja meg újra, vagy a felhőszolgáltatás régió módosítása.

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
Ha hibát tapasztal az Azure-ban a Windows virtuális gépek létrehozásakor, lásd: [egy Windows virtuális gép létrehozása az Azure-ban a telepítési problémák elhárításához](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

