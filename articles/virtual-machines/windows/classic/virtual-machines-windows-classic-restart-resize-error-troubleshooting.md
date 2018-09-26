---
title: Virtuális gép újraindításával vagy átméretezésével kapcsolatos problémák |} A Microsoft Docs
description: Újraindítása vagy átméretezése egy meglévő Azure-beli Windows virtuális gépet klasszikus üzembe helyezés hibáinak elhárítása
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 06/15/2018
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 400b20e474257650a22e04c89ddde581bf0552f4
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091730"
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Újraindítása vagy átméretezése egy meglévő Azure-beli Windows virtuális gépet klasszikus üzembe helyezés hibáinak elhárítása
> [!div class="op_single_selector"]
> * [Klasszikus](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Próbálja meg elindítani egy leállított Azure virtuális gép (VM) vagy egy meglévő Azure virtuális gép átméretezése, tapasztal a gyakori hiba esetén egy foglalási hiba. Ez a hiba eredményeként, ha a fürt vagy a régió nem rendelkezik elérhető erőforrások vagy nem támogatja a kért Virtuálisgép-méretet.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Naplók gyűjtése
Hibaelhárítás indítása, hogy azonosítsa a hibát a probléma társított a-naplók összegyűjtése.

Az Azure Portalon kattintson a **Tallózás** > **virtuális gépek** > *a Windows virtuális gép*  >   **Beállítások** > **Auditnaplók**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Probléma: Hiba a leállított virtuális gép indítása
A leállított virtuális gép elindítása, de a foglalási hiba fordul elő, próbálja meg.

### <a name="cause"></a>Ok
A kérelem a leállított virtuális gép elindításához, lehetséges, az eredeti fürthöz, amelyen a felhőszolgáltatás rendelkezik. A fürt azonban nem rendelkezik szabad terület lesz elérhető a kérés teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Új felhőszolgáltatás hozható létre, és társítsa a következők egyikével, régió vagy régió-alapú virtuális hálózat, de nem az affinitáscsoportot.
* Törölje a leállított virtuális Gépet.
* A lemezek használatával hozza létre a virtuális Gépet az új cloud service-ben.
* Indítsa el az újból létrehozott virtuális Gépet.

Új felhőszolgáltatás hozható létre tett kísérlet során hibaüzenetet kap, ha vagy próbálkozzon újra később, vagy módosítsa a felhőszolgáltatás a régióban.

> [!IMPORTANT]
> Az új felhőszolgáltatás lesz egy új nevet és egy virtuális IP-CÍMEK, úgy kell módosítani ezt az információt, amely ezt az információt használja a már meglévő felhőszolgáltatás összes függőséget.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Probléma: Hibaüzenet, ha egy meglévő virtuális gép átméretezése
Próbálja ki egy meglévő virtuális gép átméretezése, de a foglalási hiba fordul elő.

### <a name="cause"></a>Ok
A kérelem a virtuális gép átméretezése, lehetséges, az eredeti fürthöz, amelyen a felhőszolgáltatás rendelkezik. A fürt azonban nem támogatja a kért Virtuálisgép-méretet.

### <a name="resolution"></a>Megoldás:
Csökkentheti a kért Virtuálisgép-méret, és ismételje meg a átméretezése kérelmet.

* Kattintson a **összes tallózása** > **virtuális gépek (klasszikus)** > *a virtuális gép* > **beállításai**  >  **Mérete**. Részletes lépéseiért lásd: [méretezze át a virtuális gép](https://msdn.microsoft.com/library/dn168976.aspx).

Ha nem lehetséges a virtuális gép méretének csökkentéséhez, tegye a következőket:

* Új felhőszolgáltatás hozható létre, biztosítva, hogy nem kapcsolódik az affinitáscsoportot, és nincs hozzárendelve egy virtuális hálózatot, amely egy affinitáscsoporthoz van csatolva.
* Hozzon létre egy új, nagyobb méretű virtuális gép azt.

Összevonhatja a azonos felhőben lévő összes virtuális gépet. Ha a meglévő felhőszolgáltatáshoz társított régió-alapú virtuális hálózat, az új felhőszolgáltatásra csatlakozhat a meglévő virtuális hálózat.

Ha a meglévő felhőszolgáltatás nem régió-alapú virtuális hálózathoz társítva, majd akkor törölje a virtuális gépek a meglévő cloud service-ben, és létrehozhatja a saját lemezekből új felhőszolgáltatás. Azonban fontos megjegyezni, hogy az új felhőszolgáltatás lesz egy új nevet és egy virtuális IP-cím, így ezeket a függőségeket, amelyek jelenleg használják ezeket az adatokat a már meglévő felhőszolgáltatás frissíteni kell.

## <a name="next-steps"></a>További lépések
Ha problémái vannak az Azure-beli Windows virtuális gép létrehozásakor, [az Azure-beli Windows virtuális gépek létrehozásakor felmerülő üzembehelyezési hibák elhárítása](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

