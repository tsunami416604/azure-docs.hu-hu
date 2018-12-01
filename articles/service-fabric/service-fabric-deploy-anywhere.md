---
title: Service Fabric-fürtök létrehozása a Windows Server és Linux rendszeren |} A Microsoft Docs
description: Service Fabric-fürtök a Windows Server és Linux rendszerű, mely azt jelenti, hogy lesz üzembe helyezéséhez és gazdagép Service Fabric-alkalmazások bárhol futtatható Windows Server vagy Linux.
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
ms.date: 11/28/2018
ms.author: dekapur
ms.openlocfilehash: e4540076b29cf3cd51f03239a1868e18a41781d9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726525"
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Service Fabric-fürtök létrehozása Windows Serveren vagy Linuxon
Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürt csomópontja nevezzük. Fürtök méretezheti több ezer csomóponton. Ha új csomópontot ad hozzá a fürtöt, akkor a Service Fabric csomópontok megnövekedett számú rebalances a szolgáltatás partíció replikák és példányok. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés memória való hozzáféréshez. Ha a fürt csomópontjainak hatékonyan nincsenek használatban, csökkentheti a fürtben található csomópontok számát. A Service Fabric rebalances a partíciók replikáit és példányainak újra a csomópontokat, hogy a hardver jobban kihasználja a csomópontokon csökkent számú.

A Service Fabric lehetővé teszi, hogy minden olyan virtuális gépeket vagy a Windows Server vagy Linux rendszerű számítógépeken a Service Fabric-fürtök létrehozásához. Ez azt jelenti, hogy tudja, telepítéséhez és a Service Fabric-alkalmazások futtatása minden környezetben, összekapcsolt Windows Server vagy Linux rendszerű számítógépek esetében, legyen az a helyszínen, a Microsoft Azure vagy bármely más szolgáltatónál.

## <a name="create-service-fabric-clusters-on-azure"></a>Service Fabric-fürtök létrehozása az Azure-ban
Fürt létrehozása az Azure-ban történik, akár egy erőforrás-modellje sablont vagy a [az Azure portal](https://portal.azure.com). Olvasási [Service Fabric-fürt létrehozása a Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md) vagy [Service Fabric-fürt létrehozása az Azure Portalról](service-fabric-cluster-creation-via-portal.md) további információt.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Támogatott operációs rendszereket a fürtök az Azure-ban
Ön létrehozhat fürtöket említett operációs rendszerektől eltérő rendszert futtató virtuális gépeken:

* Windows Server 2012 R2
* Windows Server 2016 
* A Windows Server 1709-es
* A Windows Server 1803-as verzióban
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (előzetes verzió támogatása)

> [!NOTE]
> Ha úgy dönt, hogy üzembe helyezése a Service Fabric Windows Server 1709-es, vegye figyelembe, hogy (1), ne legyen olyan hosszú távú karbantartási ág, ezért előfordulhat, hogy a jövőben áthelyezése verziók, és (2) Ha a tárolók üzembe helyezése, a Windows Server 2016-ra épülő tárolók nem működnek a Windows Server  1709-es, és ez fordítva is igaz (kell őket az üzembe helyezés helyszíne újraépítése).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>A Service Fabric önálló helyszíni fürtök létrehozása vagy bármely más szolgáltatónál
A Service Fabric biztosít egy telepítési csomagot hozhat létre önálló Service Fabric fürtök a helyszíni vagy bármely más szolgáltatónál.

Önálló Service Fabric beállításával kapcsolatos további információkat a Windows Server-fürtök, olvassa el a [Windows Serverhez készült Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Önálló fürtök jelenleg nem támogatottak a Linux rendszerre. Linuxos beépített fejlesztéséhez és Azure Linux többgépes fürtök esetében támogatott.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>A felhőalapú telepítések és a helyszíni üzemelő példányok összehasonlítása
A folyamat a Service Fabric-fürt létrehozása a helyszíni hasonlít a virtuális gépek olyan tetszőleges a felhőben egy fürt létrehozásának folyamatán. A kezdeti lépéseket annak a virtuális gépek kiépítése a felhőszolgáltató vagy a helyszíni környezetben használt vonatkoznak rájuk. Ha engedélyezve van azok között hálózati kapcsolattal rendelkező virtuális gépek csoportját, majd állítsa be a Service Fabric-csomag, a lépéseket a fürt beállítások szerkesztése, és futtassa a fürt létrehozása és felügyeleti parancsfájlokat azonosak. Ez biztosítja, hogy tudását és üzemeltetése és kezelése a Service Fabric-fürtök élménye átruházható Ha új üzemeltetési környezetek lehetőséget választja.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Önálló Service Fabric-fürtök létrehozásának előnyeit
* Ön választhatja a felhőszolgáltatók a fürt üzemeltetéséhez.
* Service Fabric-alkalmazásokat, egyszer írt, környezetben is futtatható a több üzemeltetési környezetek minimális való módosítása nélkül.
* Ismerete segíti a Service Fabric-alkalmazások végrehajtott módosítások megjelennek üzemeltetési egyik környezetből a másikba.
* Működési élmény futtatását és kezelését a Service Fabric-fürtön végzi keresztül az egyik környezetből a másikba.
* Széles körű ügyfélkörét a korlátlan streameken működő üzemeltetési környezet megkötések.
* Megbízhatóság és a széles körű leállások elleni védelem egy további réteget létezik, mert áthelyezheti a szolgáltatások egy másik központi telepítési környezet egy kieső adatszolgáltató központot vagy a felhő-e.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Támogatott operációs rendszerek önálló fürtök
Ön létre tudja hozni a fürtök virtuális gépek vagy ezek (Linux még nem támogatott) operációs rendszert futtató számítógépeken:

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Helyszínen létrehozott Service Fabric-fürtök az Azure-on keresztül önálló Service Fabric-fürtök előnyei
Azure-ban futó Service Fabric-fürtöket biztosít a helyszíni előnyöket beállítás, így ha nem rendelkezik konkrét igényeinek megfelelő, amelyiken futtatja a fürtökhöz, a, akkor javasoljuk, hogy az Azure-ban futtatja. Az Azure-ban a integráció más Azure-funkciók és szolgáltatások, ami lehetővé teszi a műveletek és a fürt felügyeletének egyszerűbb és megbízhatóbb biztosítunk.

* **Az Azure portal:** Azure Portalon egyszerűen hozhat létre és kezelhet fürtöket.
* **Az Azure Resource Manager:** Azure Resource Manager használata lehetővé teszi az egységet a fürt által használt összes erőforrás egyszerű kezelését, és egyszerűbbé teszi a költségek nyomon követését és a számlázás.
* **Service Fabric-fürt az Azure-erőforrásként** A Service Fabric-fürt egy Azure-erőforrás, mint amikor más az Azure-erőforrások modellezheti, így.
* **Integráció az Azure-infrastruktúra** Service Fabric koordinálja az operációs rendszer, hálózati és egyéb frissítések a rendelkezésre állási és az alkalmazások megbízhatóságának javítása érdekében az alapul szolgáló Azure-infrastruktúra.  
* **Diagnosztika:** az Azure-ban, hogy integráció biztosítása az Azure-diagnosztika és a Log Analytics.
* **Automatikus méretezés:** fürtök az Azure-ban, a beépített automatikus méretezési funkcióval miatt a virtuálisgép-méretezési biztosítunk. A helyszíni és egyéb felhőkörnyezetekben fel kell a saját automatikus méretezési funkció vagy segítségével manuálisan az API-k által elérhetővé tett a Service Fabric fürtök méretezése méretezési csoport létrehozása.

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [Windows Serverhez készült Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

