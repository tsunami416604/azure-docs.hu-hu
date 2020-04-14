---
title: Automatikus skálázás immár a virtuálisgép-méretezési készletekkel
description: Automatikus skálázás immár a virtuálisgép-méretezési készletekkel kapcsolatos hibák elhárítása. Ismerje meg a felmerült tipikus problémákat és azok megoldásának módját.
author: mimckitt
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: mimckitt
ms.openlocfilehash: 4bc5e66f5b0759bdb5fe34276369161200bd5442
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273375"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Az automatikus méretezés hibaelhárítása a Virtual Machine Scale Sets használatával
**Probléma** – létrehozott egy automatikus skálázási infrastruktúrát az Azure Resource Manager ben a virtuálisgép-méretezési készletek használatával – például egy sablon üzembe helyezésével, mint ez: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – a méretezési szabályok definiálva vannak, és ez nagyszerűen működik, kivéve, hogy mennyi terhelést helyez el a virtuális gépeken, nem automatikus skálázással.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Néhány dolog, hogy fontolja meg a következők:

* Hány vCPU-k minden virtuális gép rendelkezik, és betölti az egyes vCPU-kat?
  Az előző Minta Azure-gyorsindítássablon egy do_work.php parancsfájllal rendelkezik, amely egyetlen vCPU-t tölt be. Ha egy virtuális gép nagyobb, mint egy egyvirtuálisos virtuális gép mérete, például Standard_A1 vagy D1, akkor többször kell futtatnia ezt a terhelést. A virtuális gépek virtuális gépei hez szükséges virtuális [gépek virtuális gépei azure-beli ellenőrzésének](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ellenőrzésével ellenőrizheti, hogy hány vCPU-t használ fel a virtuális gépekhez
* Hány virtuális gép a virtuális gép méretezési csoportban, mindegyiken dolgozik?
  
    A horizontális felskálázási esemény csak akkor történik meg, ha a méretezési készlet **összes** virtuális gépének átlagos processzora meghaladja a küszöbértéket az automatikus skálázási szabályokban meghatározott belső idő alatt.
* Lemaradtál a méretarányos eseményekről?
  
    Tekintse meg a naplózási naplók az Azure Portalon a méretezési események. Talán volt egy skála, és egy lefelé, hogy kimaradt. Szűrhet a "Méretezés" menüalatt.
  
    ![Naplók][audit]
* A horizontális felskálázási és horizontális felskálázási küszöbértékei eléggé eltérőek?
  
    Tegyük fel, hogy beállít egy szabályt, hogy horizontális felskálázás, ha az átlagos CPU nagyobb, mint 50%, öt perc alatt, és a méretezés, ha az átlagos CPU kevesebb, mint 50%. Ez a beállítás "csapkodó" problémát okozna, ha a CPU-használat közel van a küszöbértékhez, és a méretezési műveletek folyamatosan növelik és csökkentik a készlet méretét. Emiatt a beállítás miatt az automatikus skálázási szolgáltatás megpróbálja megakadályozni a "csapkodást", amely nem méretezésként nyilvánulhat meg. Ezért győződjön meg arról, hogy a horizontális felskálázási és horizontális felskálázási küszöbértékek eléggé eltérőek ahhoz, hogy a méretezések között némi helyet biztosítsanak.
* Megírtad a saját JSON sablonodat?
  
    Könnyű hibázni, ezért kezdje a fentihöz hasonló sablonnal, amely bizonyítottan működik, és kisebb növekményes változtatásokat hajtson végre. 
* Tud manuálisan skálázni, vagy ki?
  
    Próbálja meg újratelepíteni a virtuálisgép méretezési készlet erőforrás egy másik "kapacitás" beállítást a virtuális gépek manuális an- és hozzárendelése. Egy példa sablon https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing van itt: – előfordulhat, hogy a sablon szerkesztése, hogy megbizonyosodjon arról, hogy ugyanaz a gépmérete, mint a méretezési csoport használ. Ha sikeresen módosíthatja a virtuális gépek manuálisan, majd tudja, hogy a probléma automatikus skálázás izolált.
* Ellenőrizze a Microsoft.Compute/virtualMachineScaleSet és a Microsoft.Insights-erőforrásokat az [Azure Resource Explorerben](https://resources.azure.com/)
  
    Az Azure Resource Explorer egy nélkülözhetetlen hibaelhárító eszköz, amely megmutatja az Azure Resource Manager-erőforrások állapotát. Kattintson az előfizetésére, és tekintse meg a hibaelhárítással ellátott erőforráscsoportot. A számítási erőforrás-szolgáltató, tekintse meg a létrehozott virtuálisgép-méretezési készlet, és ellenőrizze a példánynézetben, amely megmutatja a központi telepítés állapotát. Ellenőrizze a virtuális gépek példánynézetét is a virtuális gép méretezési készletében. Ezután lépjen be a Microsoft.Insights erőforrás-szolgáltató, és ellenőrizze, hogy az automatikus skálázási szabályok jól néz ki.
* A diagnosztikai bővítmény működik, és teljesítményadatokat bocsát ki?
  
    **Frissítés:** Az Azure automatikus skálázása továbbfejlesztett egy gazdagép-alapú metrika-folyamat, amely már nem igényel diagnosztikai bővítményt kell telepíteni. A következő néhány bekezdés már nem alkalmazható, ha automatikus skálázási alkalmazást hoz létre az új folyamat használatával. A gazdafolyamat használatára konvertált Azure-sablonok raitt érhetők https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscaleel: . 
  
    Az automatikus skálázásgazda-alapú metrikák használata a következő okok miatt jobb:
  
  * Kevesebb mozgó alkatrészt kell telepíteni, mivel diagnosztikai bővítményeket nem kell telepíteni.
  * Egyszerűbb sablonok. Csak adja hozzá az elemzési adatok automatikus skálázási szabályokat egy meglévő méretezési csoport sablonhoz.
  * Megbízhatóbb jelentéskészítés és az új virtuális gépek gyorsabb elindítása.
    
    Az egyetlen ok, érdemes lehet továbbra is egy diagnosztikai bővítmény használata, ha szüksége van a memória diagnosztika jelentési/skálázási. A gazdagép-alapú mérőszámok nem jelentik a memóriát.
    
    Ezt szem előtt tartva csak kövesse a cikk többi részét, ha diagnosztikai bővítményeket használ az automatikus skálázáshoz.
    
    Automatikus skálázás az Azure Resource Manager működhet (de már nem kell) a diagnosztikai bővítmény nevű virtuálisgép-bővítmény segítségével. Teljesítményadatokat bocsát ki a sablonban megadott tárfiókba. Ezeket az adatokat ezután az Azure Monitor szolgáltatás összesíti.
    
    Ha az Insights szolgáltatás nem tudja olvasni az adatokat a virtuális gépekről, akkor e-mailt kell küldenie. Például kap egy e-mailt, ha a virtuális gépek nem. Ügyeljen arra, hogy ellenőrizze az e-mail, az e-mail címet megadott, amikor létrehozta az Azure-fiók.
    
    Az adatokat saját maga is megnézheti. Tekintse meg az Azure storage-fiók egy felhőkezelő használatával. Például a [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)használatával jelentkezzen be, és válassza ki a használt Azure-előfizetést. Ezután tekintse meg a diagnosztikai tárfiók neve hivatkozott a diagnosztikai bővítmény definíciója a központi telepítési sablonban.
    
    ![Cloud Explorer][explorer]
    
    Megjelenik egy csomó táblák, ahol az egyes virtuális gépek adatait tárolja. A Linux és a CPU-metrika példaként, tekintse meg a legutóbbi sorokat. A Visual Studio felhőkezelője támogatja a lekérdezési nyelvet, így futtathat lekérdezést. Például futtathat egy lekérdezést a "Timestamp gt datetime'2016-02-02T21:20:00Z"" lekérdezést, hogy biztosan megkapja a legfrissebb eseményeket. Az időzóna megfelel az UTC-nek. Az ott látható adatok megfelelnek a beállított méretezési szabályoknak? A következő példában a 20-as gép processzora az elmúlt öt percben 100%-ra nőtt.
    
    ![Tárolótáblák][tables]
    
    Ha az adatok nincsenek ott, ez azt jelenti, hogy a probléma a virtuális gépeken futó diagnosztikai bővítmény. Ha az adatok ott vannak, ez azt jelenti, hogy vagy probléma van a méretezési szabályok, vagy az Insights szolgáltatás. Ellenőrizze [az Azure állapotát](https://azure.microsoft.com/status/).
    
    Ha már áttekintette ezeket a lépéseket, ha továbbra is automatikus skálázási problémákkal küzd, próbálkozzon a következő erőforrásokkal: 
    * Olvassa el a fórumokat az [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), vagy [stack túlcsordulás](https://stackoverflow.com/questions/tagged/azure) 
    * Támogatási hívás naplózása. Készüljön fel a sablon és a teljesítményadatok nézetének megosztására.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
