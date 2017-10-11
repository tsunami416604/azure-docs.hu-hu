---
title: "Virtuálisgép-méretezési csoportok automatikusan skálázva hibaelhárítása |} Microsoft Docs"
description: "Hárítsa el a virtuálisgép-méretezési csoportok automatikusan skálázva. Ismerje meg észlelt jellemző problémákat és azok megoldását."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: guybo
ms.openlocfilehash: bd45a0fb99a77851aa7b91d23bd4b830b6f5cc7b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok automatikusan skálázva hibaelhárítása
**A probléma** – létrehozta az automatikus skálázás infrastruktúra az Azure Resource Manager használatával Virtuálisgép-méretezési készlet – például egy sablont ehhez hasonló telepítésével: https://github.com/Azure/azure-quickstart-templates/tree/master/201- a skálázási szabályok definiált vmss-bottle – automatikus skálázás – rendelkezik, és azt nagyszerű, azzal a különbséggel, hogy mekkora terhelés, akkor a virtuális gépek helyezni, függetlenül azok automatikus skálázás nem fog.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Szempontokat kell figyelembe venni a következők:

* Hány magok minden virtuális gép rendelkezik, és minden core betöltése?
  A fenti példa Azure gyors üzembe helyezési sablon do_work.php parancsfájl, amely betölti a egyetlen mag van. Ha nagyobb, mint például Standard_A1 vagy D1 Virtuálisgép-méretet egyetlen mag virtuális Gépet használunk, akkor ez a betöltés többször futtatnia kell. Ellenőrizze, hogy hány processzormag, a virtuális gépek megtekintésével [méretek a Windows virtuális gépek Azure-ban](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* A Virtuálisgép-méretezési csoportban lévő, hány virtuális gépek módon, az egyes munkaelem?
  
    A bővített kapacitású esemény csak lépnek helyezze el, ha között az átlagos CPU **összes** a virtuális gépek méretezési csoportban lévő meghaladja a küszöbértéket, az időbeli belső definiálva az automatikus skálázási szabályok.
* Nem hagyott ki méretezési eseményeket?
  
    Ellenőrizze, hogy a naplók az Azure-portálon méretezési események. Lehet, hogy terjedő skálán fel és le, amely egy méretezési kimaradt. Szűrhet "Méretezési"...
  
    ![Naplók][audit]
* Eltérőek a méretezési és a kibővített küszöbértékeket megfelelően?
  
    Tegyük fel, hogy úgy állítja be egy terjessze ki, ha az átlagos CPU 50 %-os több mint 5 perc és a skála 50 %-nál kisebb átlagos CPU esetén a szabályt. Emiatt egy "flapping" probléma esetén processzorhasználat megközelíti a küszöbérték skálázási műveletekhez folyamatosan növekvő, és a készlet méretének csökkentése. Emiatt az automatikus skálázás szolgáltatás megpróbálja tiltása "váltakozó állapotú", is jegyzékfájl nem skálázás, amely. Ezért győződjön meg arról, hogy a kibővített és skálázási a küszöbértékeket megfelelően különböző lemezterületet Between skálázás engedélyezéséhez.
* Volt-e írási saját JSON-sablon?
  
    Könnyen vétett, így start hasonlóan működnek, és kis növekményes módosításokat bizonyítottan egyet, amely fölött a sablon is. 
* Ön manuálisan méretezhető bejövő vagy kimenő?
  
    Próbálja meg újratelepíteni a Virtuálisgép-méretezési csoportban erőforrás manuálisan módosíthatja a virtuális gépek száma különböző "kapacitás" beállítással. Egy példa sablon ehhez a következő helyen: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – ellenőrizze, hogy a gép méretének, a méretezési készlet által használt sablon szerkesztése szeretne. Ha a virtuális gépek számát manuálisan sikeresen módosítja, akkor tudja, automatikus skálázási okozza a problémát.
* Ellenőrizze a Microsoft.Compute/virtualMachineScaleSet és Microsoft.Insights erőforrások a [Azure erőforrás-kezelővel](https://resources.azure.com/)
  
    Ez az egy elengedhetetlen hibaelhárítási eszköz, amely jelzi, hogy az Azure Resource Manager-erőforrások állapotát. Kattintson az előfizetését, és tekintse meg az erőforráscsoport hibaelhárítást. A számítási erőforrás-szolgáltató tekintse meg a Virtuálisgép-méretezési csoportban létrehozott, és ellenőrizze a példányait tartalmazó nézetet, amely jelzi, hogy egy központi telepítésének állapotát. A Virtuálisgép-méretezési csoportban lévő virtuális gépek példányait tartalmazó nézetet is ellenőrizheti. Ezután használja fel a Microsoft.Insights erőforrás-szolgáltató, és ellenőrizze, hogy az automatikus skálázási szabályok szépen.
* A diagnosztikai bővítmény működik és teljesítményadatokat kibocsátó?
  
    **Frissítés:** Azure automatikus skálázás használatához telepíteni kell egy diagnosztikai bővítmény már nem igénylő állomás alapú metrikák adatcsatorna bővült. Ez azt jelenti, hogy a következő néhány bekezdések már nem érvényes, ha létrehoz egy automatikus skálázás alkalmazást az új kimenetátirányításának segítségével. Azure-sablonok, amely használata a gazdagép-feldolgozási folyamat konvertált például: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Állomásalapú metrikákat használ az automatikus skálázási célszerűbb a következők lehetnek az okai:
  
  * Nincs diagnosztika kiterjesztéseket kevesebb mozgó elemeket kell telepíteni.
  * Egyszerűbb sablonok. Elemzések automatikus skálázási szabályok csak hozzáadása egy meglévő sablon méretezési beállítása.
  * További megbízható reporting, és új virtuális gépek gyorsabb indítása.
    
    A csak miatt előfordulhat, hogy szeretnék használni a diagnosztikai bővítmény lenne, ha szüksége memória diagnosztika reporting/méretezés. A gazdagép alapú metrikák nem jelentik a memória.
    
    Vele szem előtt csak ha kövesse az ebben a cikkben a többi diagnosztikai bővítmények továbbra is használja az automatikus skálázást.
    
    Az Azure Resource Manager automatikus skálázás együttműködhet (de már nem rendelkezik) segítségével a virtuális gépek bővítmény a diagnosztika bővítmény neve. Az megfelelően kibocsát egy tárfiókot, akkor meghatározhatja a sablonban teljesítményadatok. Ezeket az adatokat az Azure-figyelő szolgáltatás majd összesíti.
    
    Ha a Insights szolgáltatás nem tudja olvasni az adatokat a virtuális gépek, azt kellene küldjön egy e-mailt – például ha a virtuális gépeket le, így nézze meg leveleit, (egy az Azure-fiók létrehozásakor meghatározott).
    
    Is megnyithatja, és keresse meg az adatok. Tekintse meg az Azure storage-fiók egy cloud explorer használatával. Például a használatával a [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), jelentkezzen be, és mentse az Azure-előfizetés használata, és a diagnosztikai tárfiók neve a központi telepítési sablont diagnosztika bővítmény definíciójában hivatkozott...
    
    ![Cloud Explorer][explorer]
    
    Itt jelenik meg az egyes virtuális gépek adatainak tárolására táblák egy csoportját. Linux és a CPU-metrika tegyük fel, keresse meg a legutóbbi sorok. A Visual Studio cloud explorer lekérdezésnyelvet támogatja, így például a lekérdezés futtatása "időbélyeg gt dátum és idő" 2016-02-02T21:20:00Z "" a ügyeljen arra, hogy a megtartott legutóbbi események (feltételezik időpontja UTC szerint). Nem látható nem felel meg a skálázási szabályok állíthat be az adatokat? Az alábbi példában a CPU, a gép 20 elindítva, az elmúlt 5 percben 100 %-os növelését...
    
    ![Tárolási táblák][tables]
    
    Ha az adatok nem létezik, majd ez arra utalhat, a probléma van a virtuális gépeken futó diagnosztikai kiterjesztéssel. Ha az adatok ott, ez arra utalhat, vagy probléma a skála szabályokkal, vagy a Insights szolgáltatással. Ellenőrizze [Azure állapot](https://azure.microsoft.com/status/).
    
    Amennyiben azt, hogy a fenti lépéseket, ha továbbra is problémák automatikus skálázás próbálkozzon a fórumok [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp), vagy [veremtúlcsordulás](http://stackoverflow.com/questions/tagged/azure), vagy a támogatási hívás. Készüljön megosztani a sablont és a teljesítményadatokat nézetét.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
