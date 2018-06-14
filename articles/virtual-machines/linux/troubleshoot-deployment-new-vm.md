---
title: Linux virtuális gép telepítési-erőforrás-kezelő hibaelhárítása |} Microsoft Docs
description: Erőforrás-kezelő telepítési problémák elhárításához, amikor egy új Linux virtuális gép létrehozása az Azure-ban
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: aea5db05843b0175b8ef8b713944e12262e33010
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
ms.locfileid: "27579263"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Az új Linux virtuális gép létrehozása az Azure Resource Manager telepítési problémák elhárításához
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Más virtuális gép telepítési problémákat és kérdéseket: [elhárítása telepítését Linux virtuális gép az Azure-ban](troubleshoot-deploy-vm.md).
## <a name="collect-activity-logs"></a>A gyűjtés tevékenység naplói
Hibaelhárítás indítása gyűjtünk azonosítsa a hibát a probléma társított a tevékenység-naplókat. A következő hivatkozások a folyamatot, hogy részletes információkat tartalmaznak.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Az Azure-erőforrások kezeléséhez tevékenység naplók megtekintése](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y** Ha az operációs rendszer általánosított, Linux és feltöltött, illetve a általánosított beállítással rögzített, akkor nem lesz ki a hibákat. Hasonlóképpen ha az operációs rendszer speciális Linux, feltöltött, illetve a speciális beállítás rögzített, majd hibák nem lesz.

**Töltse fel a hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer általánosított Linux, és mint feltöltött speciális, elérhetővé válik egy üzembe helyezési időtúllépési hiba, mert a virtuális gép akadt-e az üzembe helyezési ponton.

**N<sup>2</sup>:** Linux kifejezetten, és töltheti fel, mivel általánosítva van az operációs rendszer esetén elérhetővé válik egy üzembe helyezési hiba, mert az új virtuális gép fut az eredeti számítógép neve, felhasználónévvel és jelszóval.

**Megoldás:**

Mindkét hibák elhárításához, az eredeti, érhető el a helyszínen, ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) a virtuális merevlemez feltöltéséhez. Feltölteni, általánosítva van, ne felejtse el futtassa - először kiosztásának megszüntetése.

**Rögzítés a hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosított Linux, mint a rögzített speciális, elérhetővé válik egy üzembe helyezési időtúllépési hiba, mert az eredeti virtuális gép már nem használható, mert meg van jelölve, mivel általánosítva van.

**N<sup>4</sup>:** Linux kifejezetten, és mivel általánosítva rögzítése az operációs rendszer esetén elérhetővé válik egy üzembe helyezési hiba, mert az új virtuális gép fut az eredeti számítógép neve, felhasználónévvel és jelszóval. Az eredeti virtuális gép is nem használható, mert meg van jelölve a speciális.

**Megoldás:**

Mindkét hibák elhárításához, az aktuális lemezkép törlése a portálról, és [vegye fel újra az aktuális merevlemezekről](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probléma: Egyéni / gyűjtemény / Piactéri lemezképhez; foglalási hiba
Ez a hiba helyzetekben akkor keletkezik, ha az új virtuális gép kérelem egy fürt, amely nem támogatja a kért Virtuálisgép-méretet, vagy nem rendelkezik a kérelem olyan rendelkezésre álló szabad területet van rögzítve.

**1. ok:** a fürt nem támogatja a kért Virtuálisgép-méretet.

**1. megoldás:**

* Próbálja megismételni a kérelmet kisebb Virtuálisgép-méretet.
* Ha a kért virtuális gép mérete nem lehet módosítani:
  * Állítsa le a rendelkezésre állási csoport összes virtuális gépet.
    Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport* > **virtuális gépek** > *a virtuális gép* > **leállítása**.
  * Állítsa le a virtuális gépeket, létre kell hoznia az új virtuális gép a kívánt méretet.
  * Indítsa el az új virtuális gép első, majd válassza ki a leállított virtuális gépek mindegyikének, és kattintson **Start**.

**2. ok:** a fürt nem rendelkezik szabad erőforrást.

**2. megoldás:**

* Próbálja megismételni a kérést később.
* Ha az új virtuális Gépet egy másik rendelkezésre állási készlet része lehet.
  * Hozzon létre egy új virtuális Gépet egy másik rendelkezésre állási csoport (ugyanabban a régióban).
  * Adja hozzá az új virtuális gép ugyanahhoz a virtuális hálózathoz.

## <a name="next-steps"></a>További lépések
Ha problémát tapasztal, amikor elindítja a Linux virtuális gép leállított vagy méretezze át egy meglévő Linux virtuális Gépre az Azure-ban, tekintse meg [újraindításával és átméretezésével egy meglévő Linux virtuális gépet az Azure erőforrás-kezelő hibaelhárítása telepítési problémákat](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

