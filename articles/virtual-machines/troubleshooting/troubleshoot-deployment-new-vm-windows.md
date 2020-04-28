---
title: A Windowsos virtuális gépek Azure-beli üzembe helyezésének hibája Microsoft Docs
description: A Resource Manager üzembe helyezésével kapcsolatos hibák elhárítása új Windowsos virtuális gép létrehozásakor az Azure-ban
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc363b87a9f5b2f013c0bae75a07d79a3a7a830
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981393"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Új Windowsos virtuális gép Azure-beli létrehozásakor felmerülő üzembe helyezési problémák elhárítása
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Más virtuálisgép-telepítési problémák és kérdések esetén lásd: [a Windows rendszerű virtuális gépekkel kapcsolatos hibák elhárítása az Azure-ban](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Tevékenységnaplók összegyűjtése
A hibaelhárítás megkezdéséhez Gyűjtse össze a tevékenység naplóit a problémához kapcsolódó hiba azonosításához. A következő hivatkozások részletes információkat tartalmaznak a követendő folyamatról.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Ha az operációs rendszer általánosított, és az általánosított beállítással van feltöltve és/vagy rögzítve, akkor nem lesz hiba. Hasonlóképpen, ha az operációs rendszer Windows rendszerű, és a speciális beállítással van feltöltve és/vagy rögzítve, akkor nem lesz hiba.

**Feltöltési hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer általánosítva van, és speciálisként van feltöltve, akkor időtúllépési hiba jelenik meg a virtuális géppel az OOBE képernyőn.

**N<sup>2</sup>:** Ha az operációs rendszer a Windows specializált, és a rendszer általánosított fájlként töltődik fel, akkor a virtuális gép az OOBE képernyőn megakad, mert az új virtuális gép az eredeti számítógépnévvel, felhasználónévvel és jelszóval fut.

**Resolution** (Osztás)

A hibák elhárításához az [Add-AzVhd használatával töltse fel az eredeti VHD](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)-t, amely a helyszínen elérhető, ugyanazzal a beállítással, mint az operációs rendszer (általánosított/specializált). Az általánosított feltöltéshez ne felejtse el először futtatni a Sysprep programot.

**Rögzítési hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosítva van, és speciálisként van rögzítve, akkor időtúllépési hiba történik, mert az eredeti virtuális gép nem használható, mert általánosítva van megjelölve.

**N<sup>4</sup>:** Ha az operációs rendszer a Windows specializált, és az általánosként van rögzítve, a rendszer kiépítési hibát jelez, mert az új virtuális gép az eredeti számítógépnévvel, felhasználónévvel és jelszóval fut. Emellett az eredeti virtuális gép nem használható, mert speciálisként van megjelölve.

**Resolution** (Osztás)

A hibák elhárításához törölje az aktuális rendszerképet a portálról, és a [jelenlegi VHD](../windows/create-vm-specialized.md) -fájlból állítsa be újra az operációs rendszer (általánosított/specializált) beállítással megegyező beállítással.

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probléma: egyéni/Képtár/Piactéri rendszerkép; foglalási hiba
Ez a hiba olyan helyzetekben fordul elő, amikor az új virtuálisgép-kérelem olyan fürthöz van rögzítve, amely nem támogatja a kért virtuálisgép-méretet, vagy nem rendelkezik szabad területtel a kérelem elfogadásához.

**1. ok:** A fürt nem támogatja a kért VM-méretet.

**1. megoldás:**

* Próbálja megismételni a kérést kisebb virtuálisgép-méret használatával.
* Ha a kért virtuális gép mérete nem módosítható:
  * Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból.
    Kattintson **Az erőforráscsoportok** > *az erőforráscsoport* > **erőforrásai** > *a rendelkezésre állási* > csoport**Virtual Machines** > *a virtuális gép* > **leállítása**elemre.
  * Az összes virtuális gép leállítása után hozza létre az új virtuális gépet a kívánt méretben.
  * Először indítsa el az új virtuális gépet, majd válassza ki a leállított virtuális gépeket, és kattintson az **Indítás**gombra.

**2. ok:** A fürtnek nincs szabad erőforrása.

**2. megoldás:**

* Próbálja megismételni a kérelmet egy későbbi időpontban.
* Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet
  * Hozzon létre egy új virtuális gépet egy másik rendelkezésre állási készletben (ugyanabban a régióban).
  * Adja hozzá az új virtuális gépet ugyanahhoz a virtuális hálózathoz.

## <a name="next-steps"></a>További lépések
Ha problémákba ütközik egy leállított Windowsos virtuális gép indításakor vagy egy meglévő Windows rendszerű virtuális gép átméretezése az Azure-ban, tekintse meg a [Resource Manager üzembe helyezésével kapcsolatos hibák elhárítása az Azure-ban meglévő Windows rendszerű virtuális gépek újraindítása vagy átméretezés](restart-resize-error-troubleshooting.md)


