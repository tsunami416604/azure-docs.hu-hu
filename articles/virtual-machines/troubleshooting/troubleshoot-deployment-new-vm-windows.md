---
title: A Windows virtuális gépek azure-beli telepítésének hibái – problémamegoldás | Microsoft dokumentumok
description: Erőforrás-kezelő telepítési problémáinak elhárítása új Windows virtuális gép létrehozásakor az Azure-ban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981393"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Az új Windows virtuális gép Azure-beli létrehozásakor felmerülő telepítési problémák elhárítása
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Legfontosabb problémák
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

A virtuális gépek telepítésével kapcsolatos egyéb problémákról és kérdésekről a [Windows virtuálisgép-problémák azure-beli telepítésének elhárítása című témakörben talál.](troubleshoot-deploy-vm-windows.md)

## <a name="collect-activity-logs"></a>Tevékenységnaplók gyűjtése
A hibaelhárítás megkezdéséhez gyűjtse össze a tevékenységnaplókat a problémával kapcsolatos hiba azonosításához. Az alábbi linkek részletes információkat tartalmaznak a követendő folyamatról.

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Ha az operációs rendszer a Windows általánosított, és feltöltött és /vagy rögzített az általánosított beállítás, akkor nem lesz hiba. Hasonlóképpen, ha az operációs rendszer a Windows speciális, és feltöltöttés / vagy elfogták a speciális beállítást, akkor nem lesz semmilyen hibát.

**Feltöltési hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer a Windows általánosított, és a feltöltött speciális, kapsz egy kiépítési időtúlírási hiba a virtuális gép beragadt a kezdőbetöltés képernyőn.

**N<sup>2</sup>:** Ha az operációs rendszer a Windows speciális, és általánosként van feltöltve, akkor egy kiépítési hiba hibaüzenetet kap a virtuális gép beragadt a kezdőélmény képernyőn, mert az új virtuális gép fut az eredeti számítógépnév, felhasználónév és jelszó.

**Resolution** (Osztás)

Mindkét hiba megoldásához használja [az Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)segítségével töltse fel az eredeti virtuális merevlemezt, amely a helyszínen érhető el, ugyanazzal a beállítással, mint az operációs rendszer (generalizált/specializált). Ha általánosként szeretne feltölteni, először futtassa a sysprep programot.

**Rögzítési hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer a Windows általános, és speciálisként rögzíti, kap egy kiépítési időtúlfoglalási hiba, mert az eredeti virtuális gép nem használható, mert általánosként van megjelölve.

**N<sup>4</sup>:** Ha az operációs rendszer a Windows speciális, és általánosként rögzíti, kap egy kiépítési hiba hiba, mert az új virtuális gép fut az eredeti számítógépnév, felhasználónév és jelszó. Emellett az eredeti virtuális gép nem használható, mert speciálisként van megjelölve.

**Resolution** (Osztás)

Mindkét hiba megoldásához törölje az aktuális lemezképet a portálról, és [rögzítse újra az aktuális virtuális gépekről](../windows/create-vm-specialized.md) ugyanazzal a beállítással, mint az operációs rendszer (generalizált/specializált).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probléma: Egyéni/galéria/piactéri lemezkép; foglalási hiba
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
Ha problémákat tapasztal, amikor elindít egy leállított Windows virtuális gépet, vagy átméretez egy meglévő Windows virtuális gépet az Azure-ban, olvassa el az [Erőforrás-kezelő telepítési problémáinak elhárítása az Azure-ban lévő meglévő Windows virtuális gépek újraindításával vagy újraméretezésével kapcsolatos problémák elhárítása című témakört.](restart-resize-error-troubleshooting.md)


