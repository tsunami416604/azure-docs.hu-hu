---
title: Virtuálisgép-méretezési csoportok automatikus skálázás hibáinak elhárítása |} A Microsoft Docs
description: Virtuálisgép-méretezési csoportok automatikus skálázás hibáinak elhárítása. Ismerje meg, tipikus jelentkező problémákat és azok megoldását.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 3308b22606e87853aad7e3d3a3995aab8d1b5401
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005311"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Automatikus méretezés hibaelhárítása a Virtual Machine Scale Sets
**A probléma** – létrehozta az automatikus skálázás infrastruktúrát az Azure Resource Manager használatával a virtual machine scale sets – például egy sablont ehhez hasonló telepítésével: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – a skálázási szabályok definiálva van, és nagyszerű, kivéve nem működik számít, hogy mennyi terhelik meg a virtuális gépeken, nem támogatja az automatikus méretezés.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Szempontokat kell figyelembe venni a következők:

* Hány vcpu-k minden virtuális gép rendelkezik, és minden egyes vCPU betöltése?
  Az előző példa Azure gyorsindítási sablon do_work.php parancsfájl, amely betölti egy egyetlen vCPU rendelkezik. Ha egyetlen vcpu-nkénti Virtuálisgép-méretét például standard_a1 neve vagy a D1-nál nagyobb méretű virtuális gép használja, kell futtatni a terhelés több alkalommal. Hány vcpu-k keresése a virtuális gépek áttekintésével [méretek a Windows virtuális gépek az Azure-ban](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hány virtuális gépet a virtuális gép méretezési sikeres, az egyes munkahelyi?
  
    A kibővített esemény kerül sor, amikor csak között az átlagos Processzorhasználat **összes** a méretezési csoportban lévő virtuális gépek meghaladja a küszöbértéket, belső idővel a megadott automatikus skálázási szabályok.
* Lemaradt a méretezési eseményeket?
  
    Ellenőrizze, hogy a naplók méretezési eseményeket az Azure Portalon. Talán volt egy méretezési csoport fel és le, amely egy méretezési csoportban nem teljesült. "Méretezés" szerint szűrheti.
  
    ![Naplók][audit]
* A horizontális le- és kibővített küszöbértékek tartoznak elég különböző?
  
    Tegyük fel, beállíthat egy szabályt, amely a horizontális felskálázás, amikor az átlagos Processzorhasználat értéke nagyobb, mint 50 %-kal több mint öt perc alatt, és a méretezési csoport, az átlagos Processzorhasználat esetén kevesebb mint 50 %. Ez a beállítás "flapping" probléma okozna, amikor a CPU-használat már közel a küszöbértéket, a skálázási műveletek folyamatosan növekvő, és a készlet méretét. Ezt a beállítást, mert az autoscale szolgáltatás megpróbálja, hogy a "váltakozó", is jegyzékfájl nem méretezés, amely. Ezért ügyeljen arra, hogy a horizontális felskálázás és horizontális leskálázási küszöbértékek megfelelően eltérő, hogy a skálázás között lemezterületet.
* Volt a saját JSON-sablon írásának?
  
    Győződjön meg arról, hibákkal, így kezdje egy sablonnal, például egyet, amely felett már bizonyított működik, és ellenőrizze a kis a növekményes változásokat, könnyebbé vált. 
* Is manuálisan méretezhető, hogy vagy?
  
    Próbálja ki az újbóli üzembe helyezés, a virtuálisgép-méretezési csoport egy másik "kapacitás" beállítás a virtuális gépek számát manuálisan módosíthatja az erőforrás állítsa be. Itt van egy példasablon: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – ellenőrizze, hogy rendelkezik a azonos mérete, a méretezési csoport használja a sablon szerkesztésével szüksége lehet. Ha sikeresen módosíthatja a virtuális gépek száma manuálisan, majd tudja, hogy a problémát el különítve, automatikus skálázást.
* Ellenőrizze a Microsoft.Compute/virtualMachineScaleSet, és a Microsoft.Insights erőforrás a [Azure erőforrás-kezelő](https://resources.azure.com/)
  
    Az Azure erőforrás-kezelő rendszer elengedhetetlen hibaelhárítási eszköz, amely bemutatja, az Azure Resource Manager-erőforrások állapotát. Kattintson az előfizetésre, és tekintse meg az erőforráscsoport a hibaelhárítást. A számítási erőforrás-szolgáltató alapján tekintse meg a virtuális gép méretezési csoportot hoztunk létre, és ellenőrizze a példányait tartalmazó nézetet, amely jelzi, hogy egy központi telepítésének állapotát. Emellett ellenőrizze a virtuálisgép-méretezési csoportban lévő virtuális gépek példányait tartalmazó nézetet. Ezután lépjen be a Microsoft.Insights erőforrás-szolgáltató, és ellenőrizze, hogy az automatikus skálázási szabályok megfelelően jelenik meg.
* A diagnosztikai bővítmény használata és teljesítményadatokat kibocsátó?
  
    **Frissítés:** Az Azure automatikus méretezési bővült a gazdagépalapú mérőszámok folyamatot, amely már nem szükséges telepíteni a diagnosztikai bővítmény használatához. A következő bekezdésekben már nem érvényesek, ha létrehoz egy automatikus méretezést végző alkalmazást az új adatcsatorna használatával. Azure-sablonok használata a gazdagép folyamat konvertált például a rendelkezésre álló itt: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Az automatikus skálázás gazdagépalapú mérőszámok használatával célszerűbb a következő okok miatt:
  
  * Kevesebb részek nem diagnosztikai bővítményként kell telepíteni.
  * Egyszerűbb sablonok. Egyszerűen adja hozzá insights automatikus méretezési szabályok egy már létező méretezésicsoport-sablon.
  * További megbízható reporting, és új virtuális gépek gyorsabb indítása.
    
    A csak okokból érdemes lehet, hogy tovább használhassa a diagnosztikai bővítmény, ha szüksége memória diagnosztikai reporting/méretezés. Gazdagépalapú mérőszámok memória nem jelentik.
    
    Vegye figyelembe, hogy az csak kövesse a cikk további részének használata diagnosztikai bővítmény a automatikus skálázáshoz.
    
    Automatikus skálázás az Azure Resource Manager dolgozhatnak (azonban már nem kell) segítségével egy virtuális gép bővítmény nevű a diagnosztikai bővítményt. Azt a teljesítményadatokat az a sablon meghatározott tárfiókhoz bocsát ki. Ezek az adatok ezután összesített értéket jelenít meg az Azure Monitor szolgáltatás által.
    
    Az Insights szolgáltatás nem tudja olvasni az adatokat a virtuális gépekről, ha ez feltételezhetően e-mail küldése. Például egy e-mailt kap, ha a virtuális gépek működnek. Mindenképpen ellenőrizze az e-mailben az Azure-fiók létrehozása során megadott e-mail címre.
    
    Kereshet is az adatok saját magának. Tekintse meg a cloud explorer használatával Azure storage-fiókban. Például a [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), jelentkezzen be, és válassza ki az Azure-előfizetést használ. Ezután tekintse meg a diagnosztikai tárfiók neve az a központi telepítési sablont a diagnosztikai bővítmény-definícióban hivatkozott.
    
    ![Cloud Explorer][explorer]
    
    Láthatja, hogy sok tábla minden egyes virtuális gépek adatainak tárolására. Linux és a CPU-metrika, például, tekintse meg a legutóbbi sorok. A Visual Studio cloud explorer egy lekérdezési nyelvet támogat, a lekérdezés futtatásához. Például futtathatja egy lekérdezést a "Timestamp gt dátum és idő" 2016-02-02T21:20:00Z'", győződjön meg arról, hogy biztosan hozzáférhessen a részleteiket megőrző legutóbbi események. Az időzóna UTC felel meg. Nem jelenik meg nem felel meg a skálázási szabályok beállítása az adatok? A következő példában a Processzor, a gép 20 elindult, növelje a 100 % az elmúlt öt percben.
    
    ![Storage-táblák][tables]
    
    Ha az adatok nem létezik, a probléma van a diagnosztikai bővítményt a virtuális gépeken futó utal. Ha az adatok van, ez arra utalhat, vagy probléma a skálázási szabályok, vagy az Insights szolgáltatással. Ellenőrizze [az Azure állapota](https://azure.microsoft.com/status/).
    
    Követően, hogy a fenti lépéseket, ha továbbra is az automatikus méretezés problémák merültek fel, próbálkozzon az alábbi forrásanyagokat: 
    * A fórumok olvasása a [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), vagy [Stack overflow-n](https://stackoverflow.com/questions/tagged/azure) 
    * A támogatási hívás naplózása. Lehet megosztani a sablont és a teljesítményadatokat nézetével előkészített.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
