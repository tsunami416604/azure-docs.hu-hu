---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c7fe0d6f8e03501cca7a8b98f95286b6a21c0476
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735724"
---
## <a name="multi-and-single-instance-vms"></a>Többszörös és egypéldányos virtuális gépek
Számos szolgáltatást használó ügyfeleink az Azure száma szerint, kritikus fontosságú, hogy azok ütemezhető a virtuális gépek tervezett karbantartás miatt az állásidő--rendelkezéseinknek körülbelül 15 perc –, amelyek akkor fordul elő, karbantartás során. Használhatja a rendelkezésre állási csoportok segítségével szabályozhatja, amikor az üzembe helyezett virtuális gépek megkapják a tervezett karbantartás.

Nincsenek Azure-on futó virtuális gépek két lehetséges konfigurációkat. Virtuális gépek vagy a többpéldányos vagy egypéldányos konfigurálni. Ha virtuális gépeket egy rendelkezésre állási csoportban, majd beállításuk többpéldányos. Vegye figyelembe, akár egyetlen virtuális gép is telepíthető egy rendelkezésre állási csoportot, hogy több példányt kezelje őket. Ha a virtuális gépek nem állnak rendelkezésre állási csoportban, majd beállításuk egypéldányos.  További információ a rendelkezésre állási csoportok: [, a Windows virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [kezelése a Linux rendszerű virtuális gépek rendelkezésre állásának](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Egypéldányos és többpéldányos virtuális gépek tervezett karbantartási frissítéseinek külön-külön történik. A virtuális gépek kell egypéldányos (Ha a többpéldányos) vagy (ha az Egypéldányos) többpéldányos újrakonfigurálásával szabályozhatja, ha a virtuális gépeiken kapni a tervezett karbantartás. Lásd: [Azure-beli Linuxos virtuális gépek tervezett karbantartásának](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [Azure Windows virtuális gépek tervezett karbantartásának](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Azure virtuális gépek tervezett karbantartása részleteiért.

## <a name="for-multi-instance-configuration"></a>A többpéldányos konfiguráció
Kiválaszthatja, hogy a tervezett karbantartás hatással van a virtuális gépek által üzembe helyezett egy rendelkezésre állási csoport konfigurációjában ezeket a virtuális gépeket távolíthat egy rendelkezésre állási csoportok idő.

1. E-mailt kap a virtuális gépek egy többpéldányos konfigurációban a tervezett karbantartás előtt, hétnapos naptárban. Az előfizetés azonosítókat és az érintett többpéldányos virtuális gépek nevét az e-mail törzsében szerepelnek.
2. E hét napban lehetősége van az üzemelő példányok frissítése a többpéldányos virtuális gépek az adott régióban eltávolítását a rendelkezésre állási idő. Ez a változtatás konfigurációs újraindítás, hatására a, a virtuális gép egy fizikai gazdagép karbantartás, egy másik fizikai gazdagépre, amely nem karbantartás céljából megcélzott van való áttérés.
3. A rendelkezésre állási az Azure Portalon a virtuális gép távolíthat el.

   1. A portálon válassza ki a virtuális gép eltávolítása a rendelkezésre állási csoportban.  

   2. A **beállítások**, kattintson a **rendelkezésre állási csoport**.

      ![Rendelkezésre állási csoport kiválasztása](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. A rendelkezésre állási csoportban legördülő menüre, válassza ki a "Nem része egy rendelkezésre állási csoportban."

      ![Távolítsa el a készletből](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. A lap tetején kattintson **mentése**. Kattintson a **Igen** gombra annak megerősítéséhez, hogy ez a művelet újraindítja a virtuális Gépet.

   >[!TIP]
   >A virtuális Gépet a többpéldányos később újrakonfigurálhatja a listán szereplő rendelkezésre állási csoportok kiválasztásával.

4. Virtuális gépek rendelkezésre állási csoportok távolítva egypéldányos gazdagépek kerül, és nem frissülnek a rendelkezésre állási beállítása konfigurációk a tervezett karbantartás során.
5. Rendelkezésre állási készletet használó virtuális gépek, a frissítés befejezése után (az eredeti e-mailben leírt ütemezésnek), adja a virtuális gépeket újra üzembe a rendelkezésre állási csoportok. Rendelkezésre állási csoport részévé újrakonfigurálja a virtuális gépek több példányt, és újraindítását eredményezi. Általában minden többpéldányos frissítések végezhető el a teljes Azure-környezetre, miután egy példányban – karbantartás követi.

Virtuális gép eltávolítása egy rendelkezésre állási csoportot is elérhető az Azure PowerShell használatával:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Az Egypéldányos konfigurációban
Kiválaszthatja, hogy az idő a tervezett karbantartás hatással van, virtuális gépek egypéldányos konfigurációban adja hozzá ezek a virtuális gépek rendelkezésre állási csoportokba.

Lépésről lépésre

1. E-mailt kap egy egypéldányos konfigurációban a virtuális gépek tervezett karbantartás előtt, hétnapos naptárban. Az előfizetés azonosítókat és az érintett egypéldányos virtuális gépek nevét az e-mail törzsében szerepelnek.
2. E hét nap alatt válassza ki a példány újraindul az Egypéldányos virtuális gépeket ad hozzá egy rendelkezésre állási csoportot, hogy ugyanabban a régióban az idő. Ez a változtatás konfigurációs újraindítás, hatására a, a virtuális gép egy fizikai gazdagép karbantartás, egy másik fizikai gazdagépre, amely nem karbantartás céljából megcélzott van való áttérés.
3. Kövesse az utasításokat követve adja hozzá a meglévő virtuális gépek rendelkezésre állási csoportokba, az Azure portal és az Azure PowerShell használatával. (Lásd az Azure PowerShell-mintát, kövesse az alábbi lépéseket.)
4. Ezek a virtuális gépek laborkörnyezetekben több példányt, ha ki vannak zárva a tervezett karbantartás az Egypéldányos virtuális gépekhez.
5. Az Egypéldányos virtuális gép frissítése (ütemezésnek az eredeti e-mailben) befejezése után a virtuális gépek eltávolítását a rendelkezésre állási csoportok egypéldányos térhet vissza a virtuális gépek.

Virtuális gép hozzáadása egy rendelkezésre állási csoport is elérhető az Azure PowerShell használatával:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
