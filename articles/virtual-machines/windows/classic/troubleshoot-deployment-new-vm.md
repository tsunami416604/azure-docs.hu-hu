---
title: "Windows virtuális gép telepítési klasszikus hibaelhárítása |} Microsoft Docs"
description: "Klasszikus telepítési problémák elhárításához, amikor egy új Windows virtuális gép létrehozása az Azure-ban"
services: virtual-machines-windows
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 9f01d237-ba39-4c32-b72d-18f5f505d43a
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 2fe0990e05758b7e538097915ffe82d9a3ec9d05
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Egy új Windows virtuális gép létrehozása az Azure klasszikus üzembe helyezési problémáinak elhárítása
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk erőforrás-kezelő verziója: [Itt](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>A gyűjtés auditnaplókat
A hibaelhárítás indítása gyűjteni a naplókat a probléma társított hiba azonosításához.

Az Azure portálon kattintson **Tallózás** > **virtuális gépek** > *a Windows rendszerű virtuális gép* > **beállítások** > **naplók**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y** Ha az operációs rendszer általánosított, Windows és feltöltött, illetve a általánosított beállítással rögzített, akkor nem lesz ki a hibákat. Hasonlóképpen ha az operációs rendszer Windows speciális, feltöltött, illetve a speciális beállítás rögzített, majd hibák nem lesz.

**Töltse fel a hibák:**

**N<sup>1</sup>:** Ha az operációs rendszer általánosított Windows, mint feltöltött speciális, elérhetővé válik egy üzembe helyezési időtúllépési hiba a a virtuális Géphez a OOBE képernyő állapotnál Beragadt.

**N<sup>2</sup>:** Ha az operációs rendszer a Windows speciális, és töltheti fel, mivel általánosítva van, egy üzembe helyezési hiba kap a virtuális gép Beragadt a OOBE képernyő, mert az új virtuális gép fut az eredeti számítógépnévvel rendelkező felhasználónév és jelszó.

**Megoldás:**

Mindkét hibák elhárításához, az eredeti, érhető el a helyszínen, ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) a virtuális merevlemez feltöltéséhez. Mivel általánosítva feltölteni, ne felejtse el először futtassa a Sysprep parancsot. Lásd: [az Azure-ba, a Windows Server VHD létrehozása és feltöltése](createupload-vhd.md) további információt.

**Rögzítés a hibák:**

**N<sup>3</sup>:** Ha az operációs rendszer általánosított Windows, mint a rögzített speciális, elérhetővé válik egy üzembe helyezési időtúllépési hiba, mert az eredeti virtuális gép már nem használható, mert meg van jelölve, mivel általánosítva van.

**N<sup>4</sup>:** Windows kifejezetten, és mivel általánosítva rögzítése az operációs rendszer esetén elérhetővé válik egy üzembe helyezési hiba, mert az új virtuális gép fut az eredeti számítógép neve, felhasználónévvel és jelszóval. Az eredeti virtuális gép is nem használható, mert meg van jelölve a speciális.

**Megoldás:**

Mindkét hibák elhárításához, az aktuális lemezkép törlése a portálról, és [vegye fel újra az aktuális merevlemezekről](capture-image.md) ugyanazokat a beállításokat, mint az operációs rendszer (általánosítva/speciális) számára.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probléma: Egyéni / gyűjtemény / Piactéri lemezképhez; foglalási hiba
Ez a hiba helyzetekben akkor keletkezik, ha az új virtuális gép kérelmet küld egy fürt, amely nem rendelkezik a kérelem olyan rendelkezésre álló szabad területet, vagy nem támogatja a kért Virtuálisgép-méretet. Már nem lehet azonos a felhőszolgáltatásban található virtuális gépek különböző sorozata. Ezért ha szeretne létrehozni egy új virtuális gép mérete eltér a felhőalapú szolgáltatás támogathatja a, a számítási kérelem sikertelen lesz.

Attól függően, hogy a felhőalapú szolgáltatás, az új virtuális gép létrehozásához használt megkötések miatt léphetnek fel két esetben által okozott hiba.

**1. ok:** adott fürt rögzítve a felhőalapú szolgáltatás, vagy affinitáscsoport csatolva, és ezért rögzítve adott fürt úgy lett kialakítva. Ezért új számítási erőforrás abban a affinitáscsoport próbálja meg ugyanabban a fürtben, a meglévő erőforrásokat a rendszer hol tárolja a kérelmek. Azonban az ugyanabban a fürtben előfordulhat, hogy nem támogatja a kért Virtuálisgép-méretet vagy nincs elegendő szabad terület, ami azt eredményezi, hogy foglalási hiba van. Ez érvényét veszti, hogy az új erőforrások jönnek létre egy új felhőalapú szolgáltatás vagy egy meglévő felhőszolgáltatáshoz keresztül.

**1. megoldás:**

* Hozzon létre egy új felhőalapú szolgáltatás, és társíthatja egy régiót vagy a régió-alapú virtuális hálózat.
* Hozzon létre egy új virtuális Gépet az új felhőalapú szolgáltatás.
  Ha hibaüzenetet kap egy új felhőalapú szolgáltatás létrehozása közben, próbálja meg újra később, vagy módosítsa a terület a felhőalapú szolgáltatáshoz.

> [!IMPORTANT]
> Ha próbál hozzon létre egy új virtuális gép egy meglévő felhőalapú szolgáltatást, de nem sikerült, és hozzon létre egy új felhőalapú szolgáltatás az új virtuális gép kellett, dönthet úgy, vonják össze a virtuális gépeinek az ugyanazon a felhőalapú szolgáltatás. Ehhez törölje a virtuális gépek a meglévő felhőalapú szolgáltatást, és ismét rögzíti őket az új felhőalapú szolgáltatás a lemezekről. Fontos azonban, ne feledje, hogy az új felhőszolgáltatás lesz egy új nevet és a VIP, így ezeket a függőségeket, amelyek jelenleg használják ezeket az információkat a meglévő felhőszolgáltatás frissíteni kell.
> 
> 

**2. ok:** a felhőalapú szolgáltatás társítva egy virtuális hálózathoz, amely csatolva van egy affinitáscsoport, így rögzítve van az adott fürt úgy lett kialakítva. Minden új számítási erőforrás abban a affinitáscsoport ezért próbálja meg ugyanabban a fürtben, a meglévő erőforrásokat a rendszer hol tárolja a kérelmek. Azonban az ugyanabban a fürtben előfordulhat, hogy nem támogatja a kért Virtuálisgép-méretet vagy nincs elegendő szabad terület, ami azt eredményezi, hogy foglalási hiba van. Ez érvényét veszti, hogy az új erőforrások jönnek létre egy új felhőalapú szolgáltatás vagy egy meglévő felhőszolgáltatáshoz keresztül.

**2. megoldás:**

* Hozzon létre egy új regionális virtuális hálózatot.
* Az új virtuális gép létrehozása az új virtuális hálózat.
* [A meglévő virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) az új virtuális hálózathoz. További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Alternatív megoldásként, [a kapcsolat-csoport-alapú virtuális hálózat regionális virtuális hálózat áttelepítése](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), majd hozza létre az új virtuális Gépet.

## <a name="next-steps"></a>Következő lépések
Ha problémát tapasztal a leállított Windows virtuális gépek elindításakor vagy egy meglévő Windows Azure-ban átméretezése, lásd: [újraindításával és átméretezésével egy meglévő Windows rendszerű virtuális gép az Azure klasszikus üzembe helyezési problémáinak elhárítása](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md).

