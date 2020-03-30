---
title: Linuxos virtuális gépek telepítésének elhárítása| Microsoft dokumentumok
description: Erőforrás-kezelő telepítési problémáinak elhárítása új Linux-virtuális gép létrehozásakor az Azure-ban
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981418"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>A Resource Manager üzembe helyezésének problémáinak elhárítása új Linux-virtuális gép azure-beli létrehozásával kapcsolatban
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Legfontosabb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

A virtuális gép üzembe helyezésére vonatkozó egyéb problémáival és kérdéseivel kapcsolatban tekintse meg a [Linux rendszerű virtuális gépek Azure-beli üzembe helyezése során előforduló problémák elhárítása](troubleshoot-deploy-vm-linux.md) című részt.

## <a name="collect-activity-logs"></a>Tevékenységnaplók gyűjtése
A hibaelhárítás megkezdéséhez gyűjtse össze a tevékenységnaplókat a problémával kapcsolatos hiba azonosításához. Az alábbi linkek részletes információkat tartalmaznak a követendő folyamatról.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Ha az operációs rendszer Linux általános, és feltöltött és / vagy rögzített az általános beállítás, akkor nem lesz semmilyen hibát. Hasonlóképpen, ha az operációs rendszer Linux-ra specializálódott, és feltöltésre és / vagy a speciális beállítással kerül rögzítésre, akkor nem lesz hiba.

**Feltöltési hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer linuxos általánosított, és speciálisként van feltöltve, egy kiépítési időtúllépési hibát kap, mert a virtuális gép a kiépítési szakaszban ragadt.

**N<sup>2</sup>:** Ha az operációs rendszer Linux-ra specializált, és általánosként van feltöltve, kiépítési hiba jelenik meg, mert az új virtuális gép az eredeti számítógépnévvel, felhasználónévvel és jelszóval fut.

**Felbontás:**

Mindkét hiba megoldásához töltse fel az eredeti virtuális merevlemezt, amely a helyszínen érhető el, ugyanazzal a beállítással, mint az operációs rendszer (generalizált/specializált). A feltöltés általános, ne feledje, hogy futtassa -deprovision először.

**Rögzítési hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer Linux általános, és speciálisként rögzíti, kap egy kiépítési időtúlfoglalási hiba, mert az eredeti virtuális gép nem használható, mivel általánosként van megjelölve.

**N<sup>4</sup>:** Ha az operációs rendszer Linux-ra specializált, és általánosként lesz rögzítve, kiépítési hiba jelenik meg, mert az új virtuális gép az eredeti számítógépnévvel, felhasználónévvel és jelszóval fut. Emellett az eredeti virtuális gép nem használható, mert speciálisként van megjelölve.

**Felbontás:**

Mindkét hiba megoldásához törölje az aktuális lemezképet a portálról, és [rögzítse újra az aktuális virtuális gépekről](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ugyanazzal a beállítással, mint az operációs rendszer (generalizált/specializált).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probléma: Egyéni/ galéria/ piactér imázsa; foglalási hiba
Ez a hiba akkor merül fel, amikor az új virtuálisgép-kérelem egy fürthöz van rögzítve, amely vagy nem támogatja a kért virtuálisgép-méretet, vagy nem rendelkezik szabad területtel a kérés befogadására.

**1. ok:** A fürt nem támogatja a kért virtuális gép méretét.

**1. megoldás:**

* Próbálkozzon újra a kérelem egy kisebb virtuális gép mérete használatával.
* Ha a kért virtuális gép mérete nem módosítható:
  * Állítsa le az összes virtuális gépet a rendelkezésre állási csoportban.
    Kattintson **az Erőforrás csoportok** > *az erőforráscsoport* > **Erőforrások** > *a rendelkezésre állási készlet* > **virtuális gépek** > *a virtuális gép* > **Stop**.
  * Miután az összes virtuális gép leáll, hozza létre az új virtuális gépet a kívánt méretben.
  * Először indítsa el az új virtuális gépet, majd jelölje ki az egyes leállított virtuális gépeket, és kattintson a **Start**gombra.

**2. ok:** A fürt nem rendelkezik szabad erőforrásokkal.

**2. megoldás:**

* Próbálkozzon újra a kéréssel egy későbbi időpontban.
* Ha az új virtuális gép egy másik rendelkezésre állási készlet része lehet
  * Hozzon létre egy új virtuális gép egy másik rendelkezésre állási csoport (ugyanabban a régióban).
  * Adja hozzá az új virtuális gépet ugyanahhoz a virtuális hálózathoz.

## <a name="next-steps"></a>További lépések
Ha problémákba ütközik, amikor egy leállított Linux virtuális gépet indít, vagy átméretez egy meglévő Linux virtuális gépet az Azure-ban, olvassa el [az Erőforrás-kezelő telepítési problémáinak elhárítása az Azure-ban egy meglévő Linux virtuális gép újraindításával vagy újraméretezésével kapcsolatos problémák elhárítása](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakört.

