---
title: "Windows virtuális gép üzembe helyezéséhez az Azure-ban hibaelhárítása |} Microsoft Docs"
description: "Erőforrás-kezelő telepítési problémák elhárításához, amikor egy új Windows virtuális gép létrehozása az Azure-ban"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fff29f6cfed4989386ca5bbd12184dce525add76
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Telepítési problémák elhárításához, amikor egy új Windows virtuális gép létrehozása az Azure-ban
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Más virtuális gép telepítési problémákat és kérdéseket: [elhárítása telepítését Windows virtuális gép az Azure-ban](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>A gyűjtés tevékenység naplói
Hibaelhárítás indítása gyűjtünk azonosítsa a hibát a probléma társított a tevékenység-naplókat. A következő hivatkozások a folyamatot, hogy részletes információkat tartalmaznak.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Az Azure-erőforrások kezeléséhez tevékenység naplók megtekintése](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y** Ha az operációs rendszer általánosított, Windows és feltöltött, illetve a általánosított beállítással rögzített, akkor nem lesz ki a hibákat. Hasonlóképpen ha az operációs rendszer Windows speciális, feltöltött, illetve a speciális beállítás rögzített, majd hibák nem lesz.

**Töltse fel a hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer általánosított Windows, mint feltöltött speciális, elérhetővé válik egy üzembe helyezési időtúllépési hiba a a virtuális Géphez a OOBE képernyő állapotnál Beragadt.

**N<sup>2</sup>:** Ha az operációs rendszer a Windows speciális, és töltheti fel, mivel általánosítva van, egy üzembe helyezési hiba kap a virtuális gép Beragadt a OOBE képernyő, mert az új virtuális gép fut az eredeti számítógépnévvel rendelkező felhasználónév és jelszó.

**Megoldás**

Mindkét ezek a hibák elhárításához használja [Add-AzureRmVhd az eredeti virtuális merevlemez feltöltéséhez](https://msdn.microsoft.com/library/mt603554.aspx), érhető el a helyszínen, ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára. Mivel általánosítva feltölteni, ne felejtse el először futtassa a Sysprep parancsot.

**Rögzítés a hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosított Windows, mint a rögzített speciális, elérhetővé válik egy üzembe helyezési időtúllépési hiba, mert az eredeti virtuális gép már nem használható, mert meg van jelölve, mivel általánosítva van.

**N<sup>4</sup>:** Windows kifejezetten, és mivel általánosítva rögzítése az operációs rendszer esetén elérhetővé válik egy üzembe helyezési hiba, mert az új virtuális gép fut-e az eredeti számítógép neve, a felhasználónevet és jelszót. Az eredeti virtuális gép is nem használható, mert meg van jelölve a speciális.

**Megoldás**

Mindkét hibák elhárításához, az aktuális lemezkép törlése a portálról, és [vegye fel újra az aktuális merevlemezekről](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára.

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probléma: Egyéni/gyűjtemény/Piactéri lemezképhez; foglalási hiba
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
Ha problémát tapasztal a leállított Windows virtuális gépek elindításakor vagy egy meglévő Windows Azure-ban átméretezése, lásd: [újraindításával és átméretezésével egy meglévő Windows rendszerű virtuális gép az Azure erőforrás-kezelő hibaelhárítása telepítési problémákat](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

