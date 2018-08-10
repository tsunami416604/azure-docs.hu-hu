---
title: Hibaelhárítás Windows virtuális gép üzembe helyezése, az Azure-ban |} A Microsoft Docs
description: Resource Manager üzembe helyezési hibák elhárításához, ha egy új Windows virtuális gép létrehozása az Azure-ban
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d406d6d8f6432b3555e34876854147c4945f7a8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634304"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Az Azure-ban új Windows virtuális gépek létrehozásakor felmerülő üzembehelyezési hibák elhárítása
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Más virtuális gép üzembe helyezési problémák és kérdések: [az Azure-beli üzembe helyezése Windows virtuális gép hibáinak elhárítása](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>Collect-Tevékenységnaplók
Hibaelhárítás indítása, hogy azonosítsa a hibát a probléma társított a vizsgálati naplók összegyűjtése. Az alábbi hivatkozások a folyamat, kövesse a részletes adatokat tartalmaznak.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezelése](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** az operációs rendszer általánosított, Windows és a feltöltött, illetve rögzíteni az általánosított beállítást, akkor a hibák nem lesz. Hasonlóan ha az operációs rendszer Windows specializált, feltöltött, illetve a speciális beállítással rögzített, majd hibákat nem lesz.

**Feltöltéssel kapcsolatos hibák:**

**N<sup>1</sup>:** , ha az operációs rendszer általánosított Windows és a feltöltés speciális, kap egy kiépítési időtúllépési hiba a virtuális géppel a Kezdőélmény képernyőn elakadt.

**N<sup>2</sup>:** Ha az operációs rendszer Windows kifejezetten, és fel lesz töltve általánosítottként, kap a virtuális géppel a Kezdőélmény képernyő megakad, mert az új virtuális gép fut-e az eredeti számítógépnévvel egy kiépítési hibák felhasználónév és jelszó.

**Felbontás**

Mindkét ezek a hibák elhárításához használja [Add-AzureRmVhd az eredeti virtuális merevlemez feltöltéséhez](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd), elérhető a helyszínen, az ugyanazt a beállítást, amely az operációs rendszer (általánosítva/speciális) számára. Általánosként feltölteni, ne felejtse el először futtassa a Sysprep eszközt.

**Rögzítés a hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosított Windows, mint rögzítése specializált, kap egy kiépítési időtúllépési hiba, mert az eredeti virtuális gép már nem használható, mert általánosként van megjelölve.

**N<sup>4</sup>:** Windows kifejezetten, és általánosként rögzítése az operációs rendszer esetén a kiépítési hibák kap, mert az új virtuális gép fut-e az eredeti számítógép neve, felhasználónévvel és jelszóval. Ezenkívül az eredeti virtuális gép nem áll használható, mert meg van jelölve a speciális.

**Felbontás**

Mindkét a hibák elhárításához, az aktuális lemezkép törlése a portálról, és [vegye fel újra a jelenlegi VHD-ből](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára.

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probléma: Egyéni/katalógus/Piactéri lemezképhez; foglalási hibák
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
Ha Windows leállított virtuális gép elindítása vagy egy meglévő Windows Azure-beli virtuális gép átméretezése során problémákat tapasztal, tekintse meg [újraindításával vagy átméretezésével, meglévő Windows virtuális gépként az Azure-ban üzembe helyezés hibáinak elhárítása Resource Manager](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

