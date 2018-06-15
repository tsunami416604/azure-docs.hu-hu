---
title: Virtuálisgép-méretezési csoportok automatikusan skálázva hibaelhárítása |} Microsoft Docs
description: Hárítsa el a virtuálisgép-méretezési csoportok automatikusan skálázva. Ismerje meg észlelt jellemző problémákat és azok megoldását.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: ea634ea8bcb4fed1ed63dc8d1e17d215a00758c6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161022"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok automatikusan skálázva hibaelhárítása
**A probléma** – létrehozta az automatikus skálázás infrastruktúra az Azure Resource Manager használatával virtuálisgép-méretezési csoportok – például egy sablont ehhez hasonló telepítésével: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – a skálázási szabályok definiálva van, és nagy, kivéve, hogy nem működik a virtuális gépeken mennyi terhelésváltozást számít, automatikus skálázás nem.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Szempontokat kell figyelembe venni a következők:

* Hány Vcpu minden virtuális gép rendelkezik, és minden vCPU betöltése?
  A fenti Azure gyors üzembe helyezési mintasablon do_work.php parancsfájl, amely betölti a egyetlen vCPU rendelkezik. Ha nagyobb, mint például Standard_A1 vagy D1 egy egyetlen-vCPU Virtuálisgép-méretet virtuális Gépet használunk, ez a betöltés többször is lefuthat kellene. Alapján ellenőrizheti a virtuális gépek hány Vcpu [méretek a Windows virtuális gépek Azure-ban](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* A virtuálisgép-méretezési csoportban lévő hány virtuális gépek módon, az egyes munkaelem?
  
    A kibővített esemény kerül sor, amikor csak között az átlagos CPU **összes** a virtuális gépek méretezési csoportban lévő meghaladja a küszöbértéket, az időbeli belső definiálva az automatikus skálázási szabályok.
* Nem hagyott ki méretezési eseményeket?
  
    Ellenőrizze, hogy a naplók az Azure-portálon méretezési események. Lehet, hogy terjedő skálán fel és le, amely egy méretezési kimaradt. "Méretezési" szerint szűrheti.
  
    ![Naplók][audit]
* Eltérőek a méretezési és a kibővített küszöbértékeket megfelelően?
  
    Tegyük fel, hogy úgy állítja be egy terjessze ki, ha az átlagos CPU 50 %-os több mint öt perc, és a skála 50 %-nál kisebb átlagos CPU esetén a szabályt. Ez a beállítás esetén a processzorhasználat megközelíti a küszöbérték skálázási műveletekhez folyamatosan növekvő, és a készlet méretének csökkentése miatt "flapping" probléma. Ezt a beállítást, mert az automatikus skálázás szolgáltatás megpróbálja tiltása "váltakozó állapotú", is jegyzékfájl nem skálázás, amely. Ezért kell arról, hogy a kibővített és a skála küszöbértékeket megfelelően különböző lemezterületet Between skálázás engedélyezéséhez.
* Volt-e írási saját JSON-sablon?
  
    Könnyen vétett, így start hasonlóan működnek, és kis növekményes módosításokat bizonyítottan egyet, amely fölött a sablon is. 
* Ön manuálisan méretezhető bejövő vagy kimenő?
  
    Próbálja meg újratelepíteni a virtuális gép méretezési erőforrás különböző "kapacitás" beállítást a virtuális gépek számát manuálisan módosíthatja. Egy példa-sablon a következő helyen: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – szükség lehet, szerkesztheti a sablont, hogy ellenőrizze, hogy a gép méretének, a méretezési használja. Ha a virtuális gépek számát manuálisan sikeresen módosíthatja, majd tudja, hogy a probléma elkülönített automatikus skálázást.
* Ellenőrizze a Microsoft.Compute/virtualMachineScaleSet és Microsoft.Insights erőforrások a [Azure erőforrás-kezelővel](https://resources.azure.com/)
  
    Az Azure erőforrás-kezelő rendszer elengedhetetlen hibaelhárítási eszköz bemutatja, hogy az Azure Resource Manager-erőforrások állapotát. Kattintson az előfizetését, és tekintse meg az erőforráscsoport hibaelhárítást. A számítási erőforrás-szolgáltató tekintse meg a virtuálisgép-méretezési csoport létrehozása, és ellenőrizze a példányait tartalmazó nézetet, amely jelzi, hogy egy központi telepítésének állapotát. Ellenőrizze a példányait tartalmazó nézetet a virtuálisgép-méretezési csoportban lévő virtuális gépek. Ezután nyissa meg a Microsoft.Insights erőforrás-szolgáltató be, és ellenőrizze, hogy az automatikus skálázási szabályok meg.
* A diagnosztikai bővítmény működik és teljesítményadatokat kibocsátó?
  
    **Frissítés:** egy állomásalapú metrikák folyamat már nem szükséges telepíteni kell egy diagnosztikai bővítmény használatához Azure automatikus skálázás bővült. A következő néhány bekezdések már nem érvényes, ha létrehoz egy automatikus skálázás alkalmazást az új kimenetátirányításának segítségével. Azure-sablonok használata a gazdagép-feldolgozási folyamat konvertált például érhető el itt: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Állomásalapú metrikákat használ az automatikus skálázási célszerűbb a következők lehetnek az okai:
  
  * Nincs diagnosztika kiterjesztéseket kevesebb mozgó elemeket kell telepíteni.
  * Egyszerűbb sablonok. Elemzések automatikus skálázási szabályok csak hozzáadása egy meglévő sablon méretezési beállítása.
  * További megbízható reporting, és új virtuális gépek gyorsabb indítása.
    
    A csak okok miatt érdemes továbbra is egy diagnosztikai bővítmény használatára, ha szüksége memória diagnosztika reporting/méretezés. Gazdagép-alapú metrikák nem jelentik a memória.
    
    Vele szem előtt csak kövesse a cikk többi használata diagnosztikai bővítmények számára az automatikus skálázást.
    
    Az Azure Resource Manager automatikus skálázás együttműködhet (de már nem rendelkezik) segítségével a virtuális gépek bővítmény a diagnosztika bővítmény neve. Az megfelelően kibocsát egy tárfiókot, akkor meghatározhatja a sablonban teljesítményadatok. Ezeket az adatokat az Azure-figyelő szolgáltatás majd összesíti.
    
    Ha a Insights szolgáltatás nem tudja olvasni az adatokat a virtuális gépek, azt kellene küldjön egy e-mailt. Például egy e-mailek lekérése, ha a virtuális gépek nem működnek. Feltétlenül nézze meg leveleit, az Azure-fiók létrehozásakor megadott e-mail címre.
    
    Is megtekintheti az adatokat saját maga. Tekintse meg az Azure storage-fiók egy cloud explorer használatával. Használata esetén például a [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), jelentkezzen be, majd válassza ki az Azure-előfizetés használata. Ezután tekintse meg a diagnosztikai tárfiók neve a központi telepítési sablonba diagnosztika-bővítmény definíciójának hivatkozik.
    
    ![Cloud Explorer][explorer]
    
   Megjelenik az egyes virtuális gépek adatainak tárolására táblák egy csoportját. Linux és a CPU-metrika tegyük fel, keresse meg a legutóbbi sorok. A Visual Studio cloud explorer lekérdezésnyelvet is támogatja, így a lekérdezés futtatása. Például futtathatja a lekérdezés "időbélyeg gt dátum és idő" 2016-02-02T21:20:00Z "" Győződjön meg arról, a legutóbbi esemény lekérdezésekor. Az időzóna UTC felel meg. Nem látható nem felel meg a skálázási szabályok állíthat be az adatokat? A következő példában a CPU, a gép 20 elindítva, az elmúlt öt percben 100 %-os növelését.
    
    ![Tárolási táblák][tables]
    
    Ha az adatok nem létezik, ez arra utalhat, a probléma van a virtuális gépeken futó diagnosztikai kiterjesztéssel. Ha az adatok ott, ez arra utalhat, vagy probléma a skála szabályokkal, vagy a Insights szolgáltatással. Ellenőrizze [Azure állapot](https://azure.microsoft.com/status/).
    
    Amennyiben azt, hogy a fenti lépéseket, ha továbbra is problémák automatikus skálázás, próbálja meg a következőket: 
    * Olvassa el a fórumok a [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), vagy [veremtúlcsordulás](http://stackoverflow.com/questions/tagged/azure) 
    * A támogatási hívás naplózása. Készüljön megosztani a sablont és a teljesítményadatokat nézetét.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
