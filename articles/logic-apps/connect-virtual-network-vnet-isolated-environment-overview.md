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
ms.date: 12/03/2018
ms.openlocfilehash: 5046b1012e0074e9548cad050c16eef25c00cee0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845195"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Azure virtuális hálózati erőforrásokhoz való hozzáférés Azure Logic Apps integrációs service-környezetek (ISEs) használatával

> [!NOTE]
> Ez a funkció akkor a *privát előzetes verzió*. Hozzáférés igényléséhez [itt csatlakozni a kérelem létrehozása](https://aka.ms/iseprivatepreview).

Egyes esetekben a logic apps és az integrációs fiókok hozzáférhetnek a védett erőforrások, például a virtuális gépek (VM) és más rendszerek vagy a szolgáltatások, az egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). A hozzáférés beállításával kapcsolatban is [hozzon létre egy *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) a logic apps és az integrációs fiókok futtatásához. 

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Az ISE létrehozása egy privát és elkülönített Logic Apps-példányt az Azure-beli virtuális hálózatban helyez üzembe. A privát példány dedikált erőforrások, például a storage használ, és a nyilvános "globális" Logic Apps szolgáltatás külön-külön futtatja. Ez a fajta elkülönítés is segít csökkenteni a hatást, amelyet más Azure-bérlőt előfordulhat, hogy az alkalmazások teljesítményét, vagy a ["zajos szomszédok" hatás](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Ez az Áttekintés ismerteti egy ISE lehetővé teszi a logic apps és integrációs fiókok közvetlen elérése az Azure virtuális hálózat, és összehasonlítja az ISE-ben és a globális Logic Apps szolgáltatás közötti különbségeket.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolált, és a globális

Amikor integrált service-környezet (ISE) hoz létre az Azure-ban, ki kell választania egy Azure virtuális hálózat, a *beszúrása* környezetben. Azure üzembe helyezte a virtuális hálózatban a Logic Apps szolgáltatás a privát példány. Ez a művelet létrehoz egy izolált környezethez, ahol Ön hozhat létre és futtathat a logic apps a dedikált erőforrásokat. Amikor létrehoz egy logikai alkalmazást, az alkalmazás helyeként, amelyek a logikai alkalmazás közvetlen hozzáférést biztosít a virtuális hálózatban lévő erőforrásokra irányuló kiválasztása ebben a környezetben. 

A Logic apps egy ISE-ben a globális Logic Apps szolgáltatás az ugyanazon felhasználói élmények és a hasonló funkciókat biztosítanak. Nem csak akkor használhatja az azonos beépített műveletek és az összekötők a globális Logic Apps szolgáltatásban, de ISE-specifikus összekötők is használhatja. Például Íme néhány standard szintű összekötők, amelyek az ISE-ben futtatott verziókat kínálnak:
 
* Az Azure Blob Storage, File Storage és Table Storage
* Az Azure-üzenetsorok, az Azure Service Bus, az Azure Event Hubs és IBM MQ-val
* Az FTP és SFTP-SSH
* Az SQL Server, az SQL Data Warehouse, Azure Cosmos DB használatával
* X 12 és EDIFACT, AS2

ISE-ben, és nem ISE csatlakozók közötti különbség a helyeken, ahol az eseményindítók és műveletek futtatása:

* Az ISE-ben beépített triggereket és műveleteket, például HTTP mindig futtatja, a logikai alkalmazás a azonos ISE-ben. 

* Két verziója kínáló összekötők egy verziója fut. az ISE-ben, míg a más verziót futtatja, a globális Logic Apps szolgáltatásban.  

  Az összekötők, amelyek rendelkeznek a **ISE** címke mindig a azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban. 

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Összekötők, amely egy ISE-ben is elérhetők a globális Logic Apps szolgáltatásban. 

> [!IMPORTANT]
> A Logic apps beépített műveleteket és az ISE-ben futó összekötők használja egy másik díjszabási csomagot, nem a fogyasztás alapú díjszabással. További információkért lásd: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Virtuális hálózati hozzáférési engedélyek

Amikor létrehoz egy integrációs service-környezet (ISE), hol válassza ki Azure-beli virtuális hálózathoz, *beszúrása* a környezetben. Injektálási üzembe helyezi a virtuális hálózatban a Logic Apps szolgáltatás a privát példány. Ez a művelet egy izolált környezethez, ahol Ön hozhat létre és futtathat a logic apps a dedikált erőforrások eredményez. Létrehozásakor a logikai alkalmazások az alkalmazások helyeként válassza ki az ISE-ben. Ezek a logic apps közvetlenül ezután a virtuális hálózat eléréséhez és a hálózaton lévő erőforrások eléréséhez. 

A virtuális hálózathoz csatlakozó rendszerek esetében is szúr be egy ISE-ben a virtuális hálózatban, a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

* ISE-ben, hogy a rendszer, például az SQL Server-összekötő

* HTTP-művelet 

* Egyéni összekötő

A helyszíni rendszerek, amelyek nem csatlakoznak egy virtuális hálózathoz, vagy nem rendelkezik az ISE-összekötők, kapcsolódhat ezekhez a rendszerekhez által [beállításával és a helyszíni adatátjáró használatával](../logic-apps/logic-apps-gateway-install.md).

A környezet betöltése az Azure virtuális hálózat kiválasztása előtt szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel kell állítania a virtuális hálózat az Azure Logic Apps szolgáltatás. A feladathoz szükséges, hogy hozzárendelje a **hálózati közreműködő** és **klasszikus közreműködői** szerepkörök az Azure Logic Apps szolgáltatásba.
Ezek az engedélyek beállításával kapcsolatban lásd: [csatlakozhat az Azure virtuális hálózatok a logikai alkalmazásokból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

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
