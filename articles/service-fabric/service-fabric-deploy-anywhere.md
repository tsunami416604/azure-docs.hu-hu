---
title: Azure Service Fabric-fürtök létrehozása a Windows Server és Linux |} Microsoft Docs
description: Futtassa a Windows Server és Linux, ami azt jelenti, képes lesz központi telepítéséhez és a Service Fabric alkalmazások üzemeltetését bárhol Service Fabric-fürtök futtathatja a Windows Server vagy Linux rendszerű.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 3d427d99f6919991c29fc5947ebe0082670a1cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Service Fabric-fürtök létrehozása a Windows Server vagy Linux
Az Azure Service Fabric-fürt olyan hálózathoz csatlakozó virtuális vagy fizikai gépek, amelybe a mikroszolgáltatások telepíteni és felügyelni. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürtcsomópont neve. Fürtök méretezhető, több ezer. Ha új csomópont hozzáadása a fürthöz, a Service Fabric újra egyensúlyba hozza a szolgáltatás partíció replikákat és a példányt a növekvő számának a csomópontok között. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés a memória a hozzáférést. Ha a fürt a csomópontok nem használ a hatékony, csökkentheti a fürtben található csomópontok számát. A Service Fabric újra újra egyensúlyba hozza a partíció replikákat és a példány között jobb kihasználása érdekében a hardver minden egyes csomóponton csomópontok csökkent száma.

A Service Fabric lehetővé teszi, hogy a virtuális gépek vagy a Windows Server vagy Linux rendszerű számítógépeken a Service Fabric-fürtök létrehozásához. Ez azt jelenti, hogy tudja, telepítéséhez és a Service Fabric-alkalmazások futtatása bármely környezetben, összekapcsolt Windows Server vagy Linux rendszerű számítógépek esetében, a kell azt a helyszíni Microsoft Azure vagy bármely felhőalapú szolgáltatóhoz.

## <a name="create-service-fabric-clusters-on-azure"></a>Az Azure Service Fabric-fürtök létrehozása
Fürt létrehozása az Azure-on történik, vagy egy erőforrás-modellje sablon keresztül vagy a [Azure-portálon](https://portal.azure.com). Olvasási [a Service Fabric-fürt létrehozása a Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md) vagy [a Service Fabric-fürt létrehozása az Azure portálról](service-fabric-cluster-creation-via-portal.md) további információt.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Azure-fürtök támogatott operációs rendszerek
Megtörténik a fürtök létrehozását az ilyen operációs rendszert futtató virtuális gépeken:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Ha úgy dönt, hogy a Windows Server 1709 Service Fabric, akkor vegye figyelembe, hogy (1.) nincs a hosszú távú karbantartási ág, ezért lehet, hogy helyezze át a verziók a jövőben, és (2.) Ha a tároló üzembe helyezése, Windows Server 2016 épülő tárolók nem működik a Windows Server  1709, és ez fordítva is igaz (hogy kell építeni őket telepíteni őket).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>A Service Fabric önálló helyszíni fürtök létrehozása vagy bármely felhőalapú szolgáltatóhoz
Service Fabric biztosít az egy telepítési csomagot hozhat létre önálló Service Fabric fürt helyszíni vagy bármely felhőalapú szolgáltató.

A különálló Service Fabric beállításával kapcsolatos további információkat a Windows Server-fürtök, olvassa el a [Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Önálló fürtök jelenleg nem támogatott Linux. Linux egy beépített fejlesztési és többgépes Azure Linux-fürtök esetén támogatott.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>A felhő telepítések és a helyszíni központi telepítések összehasonlítása
A Service Fabric-fürtöt létrehozni a helyszíni virtuális gépek vannak beállítva az Ön által választott bármely felhő fürt létrehozásának folyamatán hasonlít. A kezdeti a virtuális gépek kiépítésének lépéseit a felhőbeli szolgáltató vagy a helyszíni környezetben használt vonatkozik. Miután közöttük engedélyezett hálózati kapcsolattal rendelkező virtuális gépek halmaza, majd állítsa be a Service Fabric-csomag a lépéseket a fürt beállítások szerkesztése, és futtassa a fürt létrehozása és parancsfájlok azonosak. Ez biztosítja, hogy a Tudásbázis és üzemeltetése és kezelése a Service Fabric-fürtök élménye átruházható céloz meg új üzemeltetési környezetekben kiválasztásakor.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Különálló Service Fabric-fürtök létrehozása előnyei
* Szabadon válassza ki azt a felhő szolgáltatót a fürt üzemeltetéséhez.
* A Service Fabric alkalmazásai, egyszer, is futtatható több üzemeltetési környezetekben, ahol minimális számára nincs változás.
* A Service Fabric-alkalmazások ismerete a üzemeltetési környezetek másik végzi.
* Működési élménye futtatásáért és felügyeletéért felelős a Service Fabric fürt végzi keresztül egyik környezetből a másikba.
* Széles körű felhasználói reach unbounded üzemeltetési környezet megkötések.
* Megbízhatóság és a széles körű kimaradások elleni védelem egy további réteget létezik, mert áthelyezheti a szolgáltatások egy másik központi telepítési környezet egy blackout adatszolgáltató center és a felhő-e.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Támogatott operációs rendszerek különálló fürtök
Megtörténik a fürtök létrehozását a virtuális gépek vagy ezek (Linux jelenleg nem támogatott) operációs rendszert futtató számítógépeken:

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Különálló Service Fabric-fürtök keresztül Azure Service Fabric-fürtök előnyei helyszíni létrehozása
A helyszíni előnyöket lehetőséget, így ha az adott funkciókra van szüksége a fürtöket futtató nincs Azure-on futó Service Fabric-fürtök biztosít, akkor javasoljuk, hogy az Azure-on futtatja. Az Azure a integráció más Azure-szolgáltatások és szolgáltatások, ami lehetővé teszi a műveletek és a fürt felügyeletének egyszerűbb és megbízhatóbb nyújtunk.

* **Azure-portálon:** Azure-portálon megkönnyíti, hogy létrehozásához és kezeléséhez a fürtöket.
* **Az Azure Resource Manager:** használja az Azure Resource Manager lehetővé teszi, hogy a egységet a fürt által használt erőforrások kezelésének és egyszerűbbé teszi a költségek nyomon követését és számlázási.
* **Service Fabric-fürt Azure erőforrásként** A Service Fabric-fürt egy Azure-erőforrás, mint amikor további erőforrások az Azure-ban modellezhető azt.
* **Integráció az Azure infrastruktúra** Service Fabric koordinálja az alapul szolgáló Azure infrastruktúra az operációs rendszer, hálózati és más frissítés rendelkezésre állásának és megbízhatóságának az alkalmazások javítása érdekében.  
* **Diagnosztika:** Azure, az Azure diagnostics és Naplóelemzési nyújtunk integráció.
* **Automatikus skálázással:** az Azure-fürtök esetén nyújtunk automatikus skálázás beépített funkcióval, virtuálisgép-méretezési csoportok-miatt. A helyszíni, vagy egyéb felhőalapú környezetben akkor hozhat létre a saját automatikus skálázás funkció vagy skálája a Service Fabric elérhetővé tévő API-k segítségével manuálisan fürtöket a méretezéshez.

## <a name="next-steps"></a>További lépések

* Hozzon létre egy fürtöt virtuális gépek vagy a Windows Server rendszerű számítógépek: [Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

