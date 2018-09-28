---
title: Linux rendszerű virtuális gép üzembe helyezési hibáinak elhárítása |} A Microsoft Docs
description: Resource Manager üzembe helyezési hibák elhárításához, ha egy új Linux rendszerű virtuális gépet hoz létre az Azure-ban
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 08009ca7f9faaa75e593670c22cf864c12236e8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414107"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Új Linux rendszerű virtuális gép létrehozása az Azure Resource Manager üzembe helyezés hibáinak elhárítása
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Más virtuális gép üzembe helyezési problémák és kérdések: [az Azure-beli üzembe helyezése Linux rendszerű virtuális gép hibáinak elhárítása](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Collect-Tevékenységnaplók
Hibaelhárítás indítása, hogy azonosítsa a hibát a probléma társított a vizsgálati naplók összegyűjtése. Az alábbi hivatkozások a folyamat, kövesse a részletes adatokat tartalmaznak.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezelése](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** az operációs rendszer általánosított, Linux és a feltöltött, illetve rögzíteni az általánosított beállítást, akkor a hibák nem lesz. Hasonlóan ha az operációs rendszer Linux specializált, feltöltött, illetve a speciális beállítással rögzített, majd hibákat nem lesz.

**Feltöltéssel kapcsolatos hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer általánosított Linux, és a feltöltés speciális, kap egy kiépítési időtúllépési hiba, mert a virtuális gép elakadt a kiépítési szakaszában.

**N<sup>2</sup>:** az operációs rendszer Linux kifejezetten, és fel lesz töltve általánosítottként, ha egy kiépítési hibák kap, mert az új virtuális gép fut-e az eredeti számítógép neve, felhasználónévvel és jelszóval.

**Megoldás:**

Mindkét hibák elhárításához, töltse fel az eredeti virtuális merevlemez, rendelkezésre álló helyi, ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára. Általánosként feltölteni, ne felejtse el run - először megszüntetése.

**Rögzítés a hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosított Linux, és azt rögzíti speciális, kap egy kiépítési időtúllépési hiba, mert az eredeti virtuális gép már nem használható, mert általánosként van megjelölve.

**N<sup>4</sup>:** Linux kifejezetten, és általánosként rögzítése az operációs rendszer esetén a kiépítési hibák kap, mert az új virtuális gép fut-e az eredeti számítógép neve, felhasználónévvel és jelszóval. Ezenkívül az eredeti virtuális gép nem áll használható, mert meg van jelölve a speciális.

**Megoldás:**

Mindkét a hibák elhárításához, az aktuális lemezkép törlése a portálról, és [vegye fel újra a jelenlegi VHD-ből](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probléma: Egyéni / katalógus / Piactéri lemezképhez; foglalási hibák
Ez a hiba helyzetekben akkor keletkezik, ha az új virtuális gép kérelem rögzítve van egy fürtöt, amely nem támogatja a kért Virtuálisgép-méretet, vagy nem rendelkezik a rendelkezésre álló szabad területet a kérelem befogadásához.

**1. ok:** a fürt nem támogatja a kért Virtuálisgép-méretet.

**1. megoldás:**

* Próbálja megismételni a kérést kisebb Virtuálisgép-mérettel.
* Ha a kért virtuális gép mérete nem lehet módosítani:
  * Állítsa le a rendelkezésre állási csoport összes virtuális gépen.
    Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport*  >  **Virtuális gépek** > *a virtuális gép* > **leállítása**.
  * Után állítsa le a virtuális gépek, a új virtuális gép létrehozása a kívánt méretet.
  * Először indítsa el az új virtuális Gépet, majd válassza ki a leállított virtuális gépek mindegyike és kattintson **Start**.

**2. ok:** a fürt nem rendelkezik ingyenes erőforrások.

**2. megoldás:**

* Próbálja megismételni a kérést később.
* Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet.
  * Hozzon létre egy új virtuális Gépet egy másik rendelkezésre állási csoportot (ugyanabban a régióban).
  * Adja hozzá az új virtuális gép ugyanazon a virtuális hálózaton.

## <a name="next-steps"></a>További lépések
Ha a leállított Linux rendszerű virtuális gép elindítása vagy egy meglévő Linux az Azure-beli virtuális gép átméretezése során problémákat tapasztal, tekintse meg [újraindításával vagy átméretezésével Linux rendszerű virtuális gép az Azure-ban üzembe helyezés hibáinak elhárítása Resource Manager](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

