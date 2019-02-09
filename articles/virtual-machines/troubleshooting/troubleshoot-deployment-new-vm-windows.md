---
title: Hibaelhárítás Windows virtuális gép üzembe helyezése, az Azure-ban |} A Microsoft Docs
description: Resource Manager üzembe helyezési hibák elhárításához, ha egy új Windows virtuális gép létrehozása az Azure-ban
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f18fbabe52c9170cde70900933ce96a3a6400c7
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984341"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Az Azure-ban új Windows virtuális gépek létrehozásakor felmerülő üzembehelyezési hibák elhárítása
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Más virtuális gép üzembe helyezési problémák és kérdések: [az Azure-beli üzembe helyezése Windows virtuális gép hibáinak elhárítása](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Collect-Tevékenységnaplók
Hibaelhárítás indítása, hogy azonosítsa a hibát a probléma társított a vizsgálati naplók összegyűjtése. Az alábbi hivatkozások a folyamat, kövesse a részletes adatokat tartalmaznak.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezelése](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Ha az operációs rendszer általánosított Windows, és a feltöltött, illetve rögzíteni az általánosított beállítást, majd nem kell esetleges hibákat. Hasonlóan ha az operációs rendszer Windows specializált, feltöltött, illetve a speciális beállítással rögzített, majd hibákat nem lesz.

**Feltöltéssel kapcsolatos hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer általánosított Windows, mint a feltöltés specializált, kap egy kiépítési időtúllépési hiba a virtuális géppel a Kezdőélmény képernyőn elakadt.

**N<sup>2</sup>:** Ha az operációs rendszer Windows kifejezetten, és fel lesz töltve általánosítottként, kap egy kiépítési hibák és a virtuális gép megakad a Kezdőélmény képernyő, mert az új virtuális gép fut-e az eredeti számítógép neve, felhasználónévvel és jelszóval.

**Felbontás**

Mindkét ezek a hibák elhárításához használja [Add-AzVhd az eredeti virtuális merevlemez feltöltéséhez](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), elérhető a helyszínen, az ugyanazt a beállítást, amely az operációs rendszer (általánosítva/speciális) számára. Általánosként feltölteni, ne felejtse el először futtassa a Sysprep eszközt.

**Rögzítés a hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosított Windows, mint rögzítése specializált, kap egy kiépítési időtúllépési hiba, mert az eredeti virtuális gép már nem használható, mert általánosként van megjelölve.

**N<sup>4</sup>:** Az operációs rendszer Windows kifejezetten, és azt általánosként van rögzítve, ha egy kiépítési hibák kap, mert az új virtuális gép fut-e az eredeti számítógép neve, felhasználónévvel és jelszóval. Ezenkívül az eredeti virtuális gép nem áll használható, mert meg van jelölve a speciális.

**Felbontás**

Mindkét a hibák elhárításához, az aktuális lemezkép törlése a portálról, és [vegye fel újra a jelenlegi VHD-ből](../windows/create-vm-specialized.md) ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára.

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probléma: Egyéni/katalógus/Piactéri lemezképhez; foglalási hibák
Ez a hiba helyzetekben akkor keletkezik, ha az új virtuális gép kérelem rögzítve van egy fürtöt, amely nem támogatja a kért Virtuálisgép-méretet, vagy nem rendelkezik a rendelkezésre álló szabad területet a kérelem befogadásához.

**1. ok:** A fürt nem támogatja a kért Virtuálisgép-méretet.

**1. megoldás:**

* Próbálja megismételni a kérést kisebb Virtuálisgép-mérettel.
* Ha a kért virtuális gép mérete nem lehet módosítani:
  * Állítsa le a rendelkezésre állási csoport összes virtuális gépen.
    Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport*  >  **Virtuális gépek** > *a virtuális gép* > **leállítása**.
  * Után állítsa le a virtuális gépek, a új virtuális gép létrehozása a kívánt méretet.
  * Először indítsa el az új virtuális Gépet, majd válassza ki a leállított virtuális gépek mindegyike és kattintson **Start**.

**2. ok:** A fürt nem rendelkezik ingyenes erőforrások.

**2. megoldás:**

* Próbálja megismételni a kérést később.
* Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet.
  * Hozzon létre egy új virtuális Gépet egy másik rendelkezésre állási csoportot (ugyanabban a régióban).
  * Adja hozzá az új virtuális gép ugyanazon a virtuális hálózaton.

## <a name="next-steps"></a>További lépések
Ha Windows leállított virtuális gép elindítása vagy egy meglévő Windows Azure-beli virtuális gép átméretezése során problémákat tapasztal, tekintse meg [újraindításával vagy átméretezésével, meglévő Windows virtuális gépként az Azure-ban üzembe helyezés hibáinak elhárítása Resource Manager](restart-resize-error-troubleshooting.md).


