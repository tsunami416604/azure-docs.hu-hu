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
ms.date: 02/24/2019
ms.openlocfilehash: ed172db6aaa064cfed319a4190306d91aa846c48
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806827"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Azure virtuális hálózati erőforrásokhoz való hozzáférés Azure Logic Apps integrációs service-környezetek (ISEs) használatával

> [!NOTE]
> Ez a funkció akkor a *nyilvános előzetes verzióban*.

Egyes esetekben a logic apps és az integrációs fiókok hozzáférhetnek a védett erőforrások, például a virtuális gépek (VM) és más rendszerek vagy a szolgáltatások, az egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). A hozzáférés beállításával kapcsolatban is [hozzon létre egy *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) a logic apps és az integrációs fiókok futtatásához. Amikor létrehoz egy ISE-ben, az Azure egy magán- és elkülönített példányát a Logic Apps szolgáltatás az Azure-beli virtuális hálózatban helyez üzembe. A privát példány dedikált erőforrások, például a storage használ, és a nyilvános "globális" Logic Apps szolgáltatás külön-külön futtatja. Az elkülönített, privát példány és a nyilvános globális példány szétválasztása is segít csökkenteni a hatást, amelyet más Azure-bérlőt előfordulhat, hogy az alkalmazások teljesítményéről, amely néven is ismert a ["zajos szomszédok" hatás](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Miután létrehozta az ISE-ben, amikor hozhatók létre a logikai alkalmazás vagy az integrációs fiók választhat az ISE-ben a logikai alkalmazás vagy az integrációs fiók helyével:

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A logikai alkalmazás most már közvetlenül elérheti a rendszerek, belül vagy a virtuális hálózathoz csatlakozik, ezek az elemek egyikének használatával:

* Az ISE rendszerverzióval ellátott ezt a rendszert, például az SQL Server-összekötő
* Beépített eseményindítók vagy műveletek, például a HTTP-trigger vagy művelet
* Egyéni összekötő

Ez az Áttekintés részletesebben ismerteti hogyan egy ISE lehetővé teszi a logic apps és az integrációs fiókok az Azure virtuális hálózat közvetlen hozzáférést, és összehasonlítja az ISE-ben és a globális Logic Apps szolgáltatás között.
A helyszíni rendszerek, amelyek nem csatlakoznak egy virtuális hálózathoz, vagy nem rendelkezik az ISE-verzió összekötők, kapcsolódhat ezekhez a rendszerekhez által [beállításával és a helyszíni adatátjáró használatával](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> A Logic apps beépített műveleteket és az ISE-ben futó összekötők használja egy másik díjszabási csomagot, nem a fogyasztás alapú díjszabással. További információkért lásd: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolált, és a globális

Integrált service-környezet (ISE) az Azure-ban való létrehozásakor kiválaszthatja az Azure virtuális hálózat, ahol szeretné *beszúrása* az ISE-ben. Az Azure majd kódtárba, vagy üzembe helyezte, a virtuális hálózatban a Logic Apps szolgáltatás a privát példány. Ez a művelet létrehoz egy izolált környezethez, ahol Ön hozhat létre és futtathat a logic apps a dedikált erőforrásokat. Amikor a logikai alkalmazást fog létrehozni, válassza ki az ISE-ben az alkalmazás helyeként, amelyek a logikai alkalmazás közvetlen hozzáférést ad a virtuális hálózat és a hálózathoz tartozó erőforrások.

A Logic apps egy ISE-ben a globális Logic Apps szolgáltatás az ugyanazon felhasználói élmények és a hasonló funkciókat biztosítanak. Nem csak akkor használhatja az azonos beépített műveletek és az összekötők a globális Logic Apps szolgáltatásban, de ISE-specifikus összekötők is használhatja. Például Íme néhány standard szintű összekötők, amelyek az ISE-ben futtatott verziókat kínálnak:

* Azure Blob Storage, File Storage, and Table Storage
* Az Azure-üzenetsorok, az Azure Service Bus, az Azure Event Hubs és IBM MQ-val
* Az FTP és SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* X 12 és EDIFACT, AS2

ISE-ben, és nem ISE csatlakozók közötti különbség a helyeken, ahol az eseményindítók és műveletek futtatása:

* Az ISE-ben beépített triggereket és műveleteket, például HTTP mindig futtatja, a logikai alkalmazás a azonos ISE-ben.

* Két verziója kínáló összekötők egy verziója fut. az ISE-ben, míg a más verziót futtatja, a globális Logic Apps szolgáltatásban.  

  Az összekötők, amelyek rendelkeznek a **ISE** címke mindig a azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban.

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Összekötők, amely egy ISE-ben is elérhetők a globális Logic Apps szolgáltatásban.

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Virtuális hálózati hozzáférési engedélyek

A környezet betöltése az Azure virtuális hálózat kiválasztása előtt szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel kell állítania a virtuális hálózat az Azure Logic Apps szolgáltatás. A feladathoz szükséges, hogy hozzárendelje a **hálózati közreműködő** és **klasszikus közreműködői** szerepkörök az Azure Logic Apps szolgáltatásba.
Ezek az engedélyek beállításával kapcsolatban lásd: [csatlakozhat az Azure virtuális hálózatok a logikai alkalmazásokból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok az ISE-ben

Integrációs fiókok is használhatja a logic apps-integráció service-környezet (ISE) belül. Azonban ezeket az integrációs fiókok kell használnia a *azonos ISE* a társított logikai alkalmazásokat. A Logic apps egy ISE-ben csak azok integrációs a fiókok, amelyek ugyanabban az ISE-ben is lehet hivatkozni. Integrációs fiók létrehozásakor kiválaszthatja az ISE helye az integrációs fiók.

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [elszigetelt logikai alkalmazásokból az Azure virtuális hálózatok csatlakoztatása](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
