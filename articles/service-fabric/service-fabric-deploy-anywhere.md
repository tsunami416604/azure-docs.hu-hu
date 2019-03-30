---
title: Áttekintés az Azure és a különálló Service Fabric-fürtök |} A Microsoft Docs
description: Service Fabric-fürtöket hozhat létre virtuális gépeket vagy a Windows Server vagy Linux rendszerű számítógépek. Ez azt jelenti, hogy tudja helyezhet üzembe és Service Fabric-alkalmazásokat futtathat bármilyen környezetben, ahol van egy készletet, Windows Server vagy Linux rendszerű számítógépek, amelyek egymással összekapcsolt – a helyszínen, a Microsoft Azure-t, vagy bármely más szolgáltatónál.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 6d5169d8ea4480e95e09228f9eb02bd78fdd0be8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661327"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>A Windows Server és Linux-fürtök az Azure és a különálló Service Fabric összehasonlítása
Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürt csomópontja nevezzük. Fürtök méretezheti több ezer csomóponton. Ha új csomópontot ad hozzá a fürtöt, akkor a Service Fabric csomópontok megnövekedett számú rebalances a szolgáltatás partíció replikák és példányok. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés memória való hozzáféréshez. Ha a fürt csomópontjainak hatékonyan nincsenek használatban, csökkentheti a fürtben található csomópontok számát. A Service Fabric rebalances a partíciók replikáit és példányainak újra a csomópontokat, hogy a hardver jobban kihasználja a csomópontokon csökkent számú.

A Service Fabric lehetővé teszi, hogy minden olyan virtuális gépeket vagy a Windows Server vagy Linux rendszerű számítógépeken a Service Fabric-fürtök létrehozásához. Ez azt jelenti, hogy tudja, helyezhet üzembe és a Service Fabric-alkalmazások futtatása minden környezetben, összekapcsolt Windows Server vagy Linux rendszerű számítógépek esetében, legyen az a helyszínen, a Microsoft Azure-t, vagy bármely más szolgáltatónál.

## <a name="benefits-of-clusters-on-azure"></a>Az Azure-ban fürtök előnyei
Az Azure-ban a integráció más Azure-funkciók és szolgáltatások, ami lehetővé teszi a műveletek és a fürt felügyeletének egyszerűbb és megbízhatóbb biztosítunk.

* **Az Azure Portalon:** Az Azure Portalon egyszerűen hozhat létre és kezelhet fürtöket.
* **Az Azure Resource Manager:** Azure Resource Manager használata lehetővé teszi az egységet a fürt által használt összes erőforrás egyszerű kezelését, és egyszerűbbé teszi a költségek nyomon követését és a számlázás.
* **Service Fabric-fürt az Azure-erőforrásként** A Service Fabric-fürt egy Azure-erőforrás, mint amikor más az Azure-erőforrások modellezheti, így.
* **Integráció az Azure-infrastruktúra** Service Fabric koordinálja az operációs rendszer, hálózati és egyéb frissítések a rendelkezésre állási és az alkalmazások megbízhatóságának javítása érdekében az alapul szolgáló Azure-infrastruktúra.  
* **Diagnosztika:** Az Azure-ban az Azure diagnostics-integráció kínálunk, és az Azure Monitor naplózza.
* **Automatikus skálázást:** A fürtök az Azure-ban beépített automatikus méretezési funkcióval miatt a virtuálisgép-méretezési biztosítunk. A helyszíni és egyéb felhőkörnyezetekben fel kell a saját automatikus méretezési funkció vagy segítségével manuálisan az API-k által elérhetővé tett a Service Fabric fürtök méretezése méretezési csoport létrehozása.

## <a name="benefits-of-standalone-clusters"></a>Önálló fürtök előnyei
* Ön választhatja a felhőszolgáltatók a fürt üzemeltetéséhez.
* Service Fabric-alkalmazásokat, egyszer írt, környezetben is futtatható a több üzemeltetési környezetek minimális való módosítása nélkül.
* Ismerete segíti a Service Fabric-alkalmazások végrehajtott módosítások megjelennek üzemeltetési egyik környezetből a másikba.
* Működési élmény futtatását és kezelését a Service Fabric-fürtön végzi keresztül az egyik környezetből a másikba.
* Széles körű ügyfélkörét a korlátlan streameken működő üzemeltetési környezet megkötések.
* Megbízhatóság és a széles körű leállások elleni védelem egy további réteget létezik, mert áthelyezheti a szolgáltatások egy másik központi telepítési környezet egy kieső adatszolgáltató központot vagy a felhő-e.

## <a name="next-steps"></a>További lépések

* Olvassa el a áttekintése [Service Fabric-fürtök az Azure-ban](service-fabric-azure-clusters-overview.md)
* Olvassa el a áttekintése [önálló Service Fabric-fürtök](service-fabric-standalone-clusters-overview.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése