---
title: Azure virtuális hálózatokhoz való hozzáférés az Azure Logic Apps integrációs service Environment-környezetekkel (ISEs)
description: Ez az Áttekintés ismerteti, hogyan segíti az integrációs service-környezetek (ISEs) a logikai alkalmazások az Azure virtuális hálózatok (Vnetek) eléréséhez
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 1ef8c8eec3865f2a6e363e7da1dbda9504b81c05
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546432"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Azure virtuális hálózati erőforrásokhoz való hozzáférés Azure Logic Apps integrációs service-környezetek (ISEs) használatával

Egyes esetekben a logic apps és az integrációs fiókok hozzáférhetnek a védett erőforrások, például a virtuális gépek (VM) és más rendszerek vagy a szolgáltatások, belüli egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). A hozzáférés beállításával kapcsolatban is [hozzon létre egy *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , futtassa a logic apps és hozhat létre az integrációs fiókok.

Amikor létrehoz egy ISE-ben, az Azure egy magán- és elkülönített példányát a Logic Apps szolgáltatás az Azure-beli virtuális hálózatban helyez üzembe. A privát példány dedikált erőforrások, például a storage használ, és a nyilvános "globális" Logic Apps szolgáltatás külön-külön futtatja. Az elkülönített, privát példány és a nyilvános globális példány szétválasztása is segít csökkenteni a hatást, amelyet más Azure-bérlőt előfordulhat, hogy az alkalmazások teljesítményéről, amely néven is ismert a ["zajos szomszédok" hatás](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Miután létrehozta az ISE-ben, amikor hozhatók létre a logikai alkalmazás vagy az integrációs fiók választhat az ISE-ben a logikai alkalmazás vagy az integrációs fiók helyével:

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A logikai alkalmazás most már közvetlenül elérheti a rendszerek, belül vagy a virtuális hálózathoz csatlakozik, ezek az elemek egyikének használatával:

* Egy **ISE**-címkével, hogy a rendszer, például az SQL Server-összekötő
* A **Core**-címkével ellátott beépített eseményindítót vagy műveletet, például a HTTP-trigger vagy művelet
* Egyéni összekötő

Ez az Áttekintés részletesebben ismerteti hogyan egy ISE lehetővé teszi a logic apps és az integrációs fiókok az Azure virtuális hálózat közvetlen hozzáférést, és összehasonlítja az ISE-ben és a globális Logic Apps szolgáltatás között.

> [!NOTE]
> A Logic apps, beépített eseményindítók, beépített műveleteket és összekötők, amely az ISE-ben használt árképzési csomag eltér a fogyasztás alapú díjszabással. További információkért lásd: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolált, és a globális

Integrált service-környezet (ISE) az Azure-ban való létrehozásakor kiválaszthatja az Azure virtuális hálózat, ahol szeretné *beszúrása* az ISE-ben. Az Azure majd kódtárba, vagy üzembe helyezte, a virtuális hálózatban a Logic Apps szolgáltatás a privát példány. Ez a művelet létrehoz egy izolált környezethez, ahol Ön hozhat létre és futtathat a logic apps a dedikált erőforrásokat. Amikor a logikai alkalmazást fog létrehozni, válassza ki az ISE-ben az alkalmazás helyeként, amelyek a logikai alkalmazás közvetlen hozzáférést ad a virtuális hálózat és a hálózathoz tartozó erőforrások.

A Logic apps egy ISE-ben a globális Logic Apps szolgáltatás az ugyanazon felhasználói élmények és a hasonló funkciókat biztosítanak. Nem csak is használhatja az azonos beépített eseményindítók, beépített műveleteket és összekötők a Logic Apps globális szolgáltatás, de ISE-specifikus összekötők is használhatja. Például Íme néhány standard szintű összekötők, amelyek az ISE-ben futtatott verziókat kínálnak:

* Azure Blob Storage, File Storage, and Table Storage
* Az Azure-üzenetsorok, az Azure Service Bus, az Azure Event Hubs és IBM MQ-val
* Az FTP és SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* X 12 és EDIFACT, AS2

ISE-ben, és nem ISE csatlakozók közötti különbség a helyeken, ahol az eseményindítók és műveletek futtatása:

* Az ISE-ben beépített triggereket és műveleteket, például a HTTP-n mindig futtatása az azonos ISE-ben, a logikai alkalmazást, és a megjelenített a **Core** címkét.

  ![Válassza ki a "Fő" beépített eseményindítók és műveletek](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Az ISE-ben futó összekötők nyilvánosan rendelkezik tárolt verzió érhető el a globális Logic Apps szolgáltatásban. Összekötők két verziója, az összekötők által a **ISE** címke mindig a azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

### <a name="access-to-on-premises-data-sources"></a>A helyszíni adatforrásokhoz való hozzáférés

A helyszíni rendszerek Azure-beli virtuális hálózathoz csatlakozó behelyezése egy ISE-ben, e hálózat, a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával:

* ISE-verziót, hogy a rendszer, például az SQL Server-összekötő
  
* HTTP-művelet
  
* Egyéni összekötő

  * Ha egyéni összekötők a helyszíni adatátjáró igénylő, és létrehozott egy ISE kívül összekötőket, a logic apps egy ISE-ben is használhatja ezeket az összekötőket.
  
  * Az ISE-ben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Azonban ezeket az összekötőket közvetlenül hozzáférhet az ISE-ben üzemeltető virtuális hálózathoz csatlakozó helyszíni adatforrásokhoz. Tehát az ISE-ben a logic apps valószínűleg nem szükséges az átjáró azokhoz az erőforrásokhoz való kommunikáció során.

A helyszíni rendszerek, amelyek nem csatlakoznak egy virtuális hálózathoz, vagy nem rendelkezik az ISE-verzió összekötők, előbb futtatnia kell [beállítása a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md) előtt a logic apps kapcsolatot létesíthessen rendszereket.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok az ISE-ben

Integrációs fiókok is használhatja a logic apps-integráció service-környezet (ISE) belül. Azonban ezeket az integrációs fiókok kell használnia a *azonos ISE* a társított logikai alkalmazásokat. A Logic apps egy ISE-ben csak azok integrációs a fiókok, amelyek ugyanabban az ISE-ben is lehet hivatkozni. Integrációs fiók létrehozásakor kiválaszthatja az ISE helye az integrációs fiók.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [elszigetelt logikai alkalmazásokból az Azure virtuális hálózatok csatlakoztatása](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
