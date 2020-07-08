---
title: Linux rendszerű virtuális gépek üzembe helyezése – problémamegoldás | Microsoft Docs
description: A Resource Manager üzembe helyezésével kapcsolatos problémák elhárítása új Linux rendszerű virtuális gép létrehozásakor az Azure-ban
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: daberry
ms.openlocfilehash: f85389d8fc2269b346df22854bb7ddce08844a88
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83118226"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>A Resource Manager üzembe helyezésével kapcsolatos hibák elhárítása új Linux rendszerű virtuális gép létrehozásával az Azure-ban
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

A virtuális gép üzembe helyezésére vonatkozó egyéb problémáival és kérdéseivel kapcsolatban tekintse meg a [Linux rendszerű virtuális gépek Azure-beli üzembe helyezése során előforduló problémák elhárítása](troubleshoot-deploy-vm-linux.md) című részt.

## <a name="collect-activity-logs"></a>Tevékenységnaplók összegyűjtése
A hibaelhárítás megkezdéséhez Gyűjtse össze a tevékenység naplóit a problémához kapcsolódó hiba azonosításához. A következő hivatkozások részletes információkat tartalmaznak a követendő folyamatról.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Ha az operációs rendszer Linux általánosított, és az általánosított beállítással van feltöltve és/vagy rögzítve, akkor nem lesz hiba. Hasonlóképpen, ha az operációs rendszer Linux-alapú, és a speciális beállítással van feltöltve és/vagy rögzítve, akkor nem lesz hiba.

**Feltöltési hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer Linux általánosított, és speciálisként van feltöltve, a kiépítési időtúllépési hiba jelenik meg, mert a virtuális gép a kiépítési szakaszban megakad.

**N<sup>2</sup>:** Ha az operációs rendszer Linux-alapú, és az általánosított módon van feltöltve, a rendszer kiépítési hibát jelez, mert az új virtuális gép az eredeti számítógépnévvel, felhasználónévvel és jelszóval fut.

**Felbontás**

A hibák elhárításához töltse fel a helyszínen elérhető eredeti VHD-t ugyanazzal a beállítással, mint az operációs rendszer (általánosított/specializált). Ha általánosítva szeretné feltölteni a feltöltést, ne feledje, hogy először futtassa a-megszüntetést.

**Rögzítési hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer Linux általánosított, és speciálisként van rögzítve, akkor időtúllépési hiba történik, mert az eredeti virtuális gép nem használható, mert általánosítva van megjelölve.

**N<sup>4</sup>:** Ha az operációs rendszer Linux-alapú, és a rendszer általánosított rögzíti, a kiépítési hiba miatt hibaüzenetet kap, mert az új virtuális gép az eredeti számítógépnévvel, felhasználónévvel és jelszóval fut. Emellett az eredeti virtuális gép nem használható, mert speciálisként van megjelölve.

**Felbontás**

A hibák elhárításához törölje az aktuális rendszerképet a portálról, és a [jelenlegi VHD](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -fájlból állítsa be újra az operációs rendszer (általánosított/specializált) beállítással megegyező beállítással.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probléma: egyéni/Képtár/Piactéri rendszerkép; foglalási hiba
Ez a hiba olyan helyzetekben fordul elő, amikor az új virtuálisgép-kérelem olyan fürthöz van rögzítve, amely nem támogatja a kért virtuálisgép-méretet, vagy nem rendelkezik szabad területtel a kérelem elfogadásához.

**1. ok:** A fürt nem támogatja a kért VM-méretet.

**1. megoldás:**

* Próbálja megismételni a kérést kisebb virtuálisgép-méret használatával.
* Ha a kért virtuális gép mérete nem módosítható:
  * Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból.
    Kattintson az **erőforráscsoportok**  >  *az erőforráscsoport*  >  **erőforrásai**  >  *a rendelkezésre állási*csoport  >  **Virtual Machines**  >  *a virtuális gép*  >  **leállítása**elemre.
  * Az összes virtuális gép leállítása után hozza létre az új virtuális gépet a kívánt méretben.
  * Először indítsa el az új virtuális gépet, majd válassza ki a leállított virtuális gépeket, és kattintson az **Indítás**gombra.

**2. ok:** A fürtnek nincs szabad erőforrása.

**2. megoldás:**

* Próbálja megismételni a kérelmet egy későbbi időpontban.
* Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet
  * Hozzon létre egy új virtuális gépet egy másik rendelkezésre állási készletben (ugyanabban a régióban).
  * Adja hozzá az új virtuális gépet ugyanahhoz a virtuális hálózathoz.

## <a name="next-steps"></a>További lépések
Ha a leállított linuxos virtuális gép indításakor vagy egy meglévő linuxos virtuális gép Azure-ban való átméretezésével kapcsolatos problémák merülnek fel, tekintse meg a [Resource Manager-alapú üzembe helyezési problémák elhárítása az Azure-ban meglévő linuxos virtuális gépek újraindításával vagy átméretezésével](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

