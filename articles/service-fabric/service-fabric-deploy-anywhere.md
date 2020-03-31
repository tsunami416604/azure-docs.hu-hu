---
title: Az Azure és az önálló Service Fabric-fürtök áttekintése
description: A Service Fabric-fürtök bármely virtuális gépen vagy Windows Server vagy Linux operációs rendszert futtató számítógépen létrehozható. Ez azt jelenti, hogy a Service Fabric-alkalmazásokat bármilyen környezetben telepítheti és futtathatja, ahol olyan Windows Server vagy Linux számítógépek vannak, amelyek a helyszínen, a Microsoft Azure-ban vagy bármely felhőszolgáltatóval összekapcsolódnak.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751146"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Az Azure és az önálló Service Fabric-fürtök összehasonlítása Windows Server és Linux rendszeren

A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. A fürt részét képezi gépet vagy virtuális gépet fürtcsomópontnak nevezzük. A fürtök több ezer csomópontra méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, a Service Fabric újraegyensúlyozza a szolgáltatáspartíció-replikákat és példányokat a megnövekedett számú csomópont között. Az alkalmazások általános teljesítménye javul, és csökken a memóriahoz való hozzáférés rekedése. Ha a fürt csomópontjait nem használja hatékonyan, csökkentheti a csomópontszámát a fürtben. A Service Fabric újra egyensúlyba hozza a partícióreplikákat és példányokat a csomópontok számának csökkenésével, hogy jobban kihasználja a hardvert az egyes csomópontokon.

A Service Fabric lehetővé teszi a Service Fabric-fürtök létrehozását bármely virtuális gépen vagy Windows Server vagy Linux operációs rendszert futtató számítógépen. Ez azt jelenti, hogy a Service Fabric-alkalmazásokat bármilyen környezetben telepítheti és futtathatja, ahol összekapcsolt Windows Server vagy Linux rendszerű számítógépekkel rendelkezik, legyen az a helyszíni, a Microsoft Azure vagy bármely felhőszolgáltató.

## <a name="benefits-of-clusters-on-azure"></a>A fürtök előnyei az Azure-ban

Az Azure-ban integrációt biztosítunk más Azure-funkciókkal és -szolgáltatásokkal, ami megkönnyíti és megbízhatóbbá teszi a fürt működését és felügyeletét.

* **Azure-portál:** Az Azure Portal megkönnyíti a fürtök létrehozását és kezelését.
* **Azure erőforrás-kezelő:** Az Azure Resource Manager használata lehetővé teszi a fürt által egységként használt összes erőforrás egyszerű kezelését, és leegyszerűsíti a költségkövetést és a számlázást.
* **Service Fabric Cluster azure-erőforrásként** A Service Fabric-fürt egy Azure-erőforrás, így modellezheti, mint az Azure-ban más erőforrásokat.
* **Integráció az Azure-infrastruktúrával** A Service Fabric koordinálja az operációs rendszer, a hálózat és az egyéb frissítések alapjául szolgáló Azure-infrastruktúrával az alkalmazások rendelkezésre állásának és megbízhatóságának javítása érdekében.  
* **Diagnosztika:** Az Azure-ban integrációt biztosítunk az Azure-diagnosztika és az Azure Monitor-naplók.
* **Automatikus méretezés:** Az Azure-beli fürtök esetében a Virtuálisgép méretezési csoportjainak köszönhetően beépített automatikus méretezési funkciókat biztosítunk. A helyszíni és más felhőbeli környezetekben saját automatikus skálázási szolgáltatást kell készítenie, vagy manuálisan kell skáláznia a Service Fabric által a fürtök méretezéséhez elérhető API-k használatával.

## <a name="benefits-of-standalone-clusters"></a>Az önálló fürtök előnyei

* Szabadon választhat bármely felhőszolgáltatót a fürt üzemeltetéséhez.
* Service Fabric-alkalmazások, egyszer írt, futtatható több üzemeltetési környezetben minimális módosítások nélkül.
* A Service Fabric-alkalmazások létrehozásának ismerete átviszi az egyik üzemeltetési környezetből a másikba.
* A Service Fabric-fürtök futtatásának és kezelésének működési tapasztalata egyik környezetből a másikba átviszi.
* A széles ügyfélelérést nem korlátozzák a környezet üzemeltetési korlátai.
* A megbízhatóság és a széles körű kimaradások elleni védelem egy további rétege létezik, mivel a szolgáltatásokat áthelyezheti egy másik központi telepítési környezetbe, ha egy adatközpont vagy felhőszolgáltató áramszünettel rendelkezik.

## <a name="next-steps"></a>További lépések

* Olvassa el a Service Fabric-fürtök áttekintését [az Azure-ban](service-fabric-azure-clusters-overview.md)
* Olvassa el a [Service Fabric önálló fürtjeit](service-fabric-standalone-clusters-overview.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése