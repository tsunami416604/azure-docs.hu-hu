---
title: Azure virtuális hálózatokhoz való hozzáférés az Azure Logic Apps integrációs service Environment-környezetekkel (ISEs)
description: Ez az Áttekintés ismerteti, hogyan segíti az integrációs service-környezetek (ISEs) a logikai alkalmazások az Azure virtuális hálózatok eléréséhez
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: f21af23cf0b7b121441b1433f382db60ef7f13fe
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408659"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Azure virtuális hálózati erőforrásokhoz való hozzáférés Azure Logic Apps integrációs service-környezetek (ISEs) használatával

> [!NOTE]
> Ez a funkció akkor a *privát előzetes verzió*. Hozzáférés igényléséhez [itt csatlakozni a kérelem létrehozása](https://aka.ms/iseprivatepreview).

Egyes esetekben a logic apps és az integrációs fiókok hozzáférhetnek a biztonságos erőforrások, például virtuális gépek (VM) és a más rendszerekkel vagy belüli szolgáltatások egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md). A hozzáférés beállításával kapcsolatban is [hozzon létre egy *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , amelyet használhat a hely, a logic apps és az integrációs fiókok. 

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Az ISE létrehozása egy privát és elkülönített Logic Apps-példányt az Azure-beli virtuális hálózatban helyez üzembe. A privát példány dedikált erőforrások, például a storage használ, és a nyilvános "globális" Logic Apps szolgáltatás külön-külön futtatja. Ez a fajta elkülönítés is segít csökkenteni a hatást, amelyet más Azure-bérlőt előfordulhat, hogy az alkalmazások teljesítményét, vagy a ["zajos szomszédok" hatás](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Ez az Áttekintés ismerteti, hogyan létrehozása az ISE segít a logic apps és az integrációs fiókok tud közvetlenül hozzáférni az erőforrásokhoz az Azure virtuális hálózaton belül, és összehasonlítja az ISE-ben és a globális Logic Apps szolgáltatás közötti különbségeket.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolált, és a globális

Integrált service-környezet (ISE) az Azure-ban való létrehozásakor kiválaszthatja egy Azure virtuális hálózatban, mint egy *társ* környezete számára. Azure üzembe helyezte a virtuális hálózatban, ahol Ön hozhat létre és futtathat a logic apps a dedikált erőforrások egy elkülönített környezet eredményez a Logic Apps szolgáltatás a privát példány. Amikor létrehoz egy logikai alkalmazást, az alkalmazás földrajzi hely is a logic app közvetlen hozzáférést ad a virtuális hálózatban lévő erőforrásokra is válassza ki a ebben a környezetben.  

A Logic apps egy ISE-ben a globális Logic Apps szolgáltatás az ugyanazon felhasználói élmények és a hasonló funkciókat biztosítanak. Nem csak használható built-ins és az összekötők a Logic Apps globális szolgáltatás által biztosított, de az összekötők által biztosított az ISE-verziók közül választhat. Például Íme néhány standard szintű összekötők, amelyek az ISE-ben futtatott verziókat kínálnak:
 
* Az Azure Blob Storage, File Storage és Table Storage
* Azure Queues
* Azure Service Bus
* FTP
* SSH FTP (SFTP)
* SQL Server
* X 12 és EDIFACT, AS2

ISE-ben, és nem ISE csatlakozók közötti különbség a helyeken, ahol az eseményindítók és műveletek futtatása:

* Beépített triggereket és műveleteket, például a HTTP használata az ISE-ben, ha ezek triggereket és műveleteket mindig fusson, a logikai alkalmazás a azonos ISE-ben. 

* Összekötők két verziója kínáló: egy verziója fut. az ISE-ben, míg a más verziót futtatja, a globális Logic Apps szolgáltatásban.  

  Az összekötők, amelyek rendelkeznek a **ISE** címke mindig a azonos ISE-ben, a logikai alkalmazás futtatási. Összekötők nélkül a **ISE** címke futtassa a globális Logic Apps szolgáltatásban. 

  ![Válassza ki az ISE-összekötők](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Az ISE-ben konfigurált összekötők is elérhetők a globális Logic Apps szolgáltatásban való használatra. 

> [!IMPORTANT]
> A Logic apps beépített műveleteket és az ISE-ben futó összekötők használja egy másik díjszabási csomagot, nem a fogyasztás alapú díjszabással. További információkért lásd: [Logic Apps díjszabási](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Virtuális hálózati hozzáférési engedélyek

Egy integrációs service-környezet (ISE) létrehozásakor kiválaszthatja egy Azure virtuális hálózatban, mint egy *társ* környezete számára. Azonban továbbra is *csak* létrehozni a kapcsolatot, vagy *társviszony-létesítés*, amikor hoz létre az ISE-ben. Ezt a kapcsolatot az ISE hozzáférhet a virtuális hálózat, amely lehetővé teszi a logic apps, hogy ISE közvetlenül csatlakozhat a virtuális hálózatban lévő erőforrásokra erőforrásokhoz. A helyszíni rendszerek egy virtuális hálózatban, amely kapcsolódik az ISE-ben a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

* ISE-ben, hogy a rendszer, például az SQL Server-összekötő

* HTTP-művelet 

* Egyéni összekötő

Helyszíni rendszerekhez, amely nem a virtuális hálózat, vagy nem rendelkezik az ISE-összekötők, továbbra is a kapcsolódás után, [beállítása és használata a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md).

Azure-beli virtuális hálózathoz, társ számára a környezet kiválasztása, előtt szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel kell állítania a virtuális hálózat az Azure Logic Apps szolgáltatás. A feladathoz szükséges, hogy hozzárendelje a **hálózati közreműködő** és **klasszikus közreműködői** szerepkörök az Azure Logic Apps szolgáltatásba. A társviszony-létesítéshez szükséges szerepkör engedélyeivel kapcsolatos további információkért lásd: a [engedélyek létrehozása, módosítása, témakör vagy egy virtuális hálózati társviszony törlése](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok az ISE-ben

A logic apps-integráció service-környezet (ISE) futtató integrációs fiókok is használhatja, de ezeket az integrációs fiókok is használnia kell a *azonos ISE* a társított logikai alkalmazásokat. A Logic apps egy ISE-ben csak azok integrációs a fiókok, amelyek ugyanabban az ISE-ben is lehet hivatkozni. Integrációs fiók létrehozásakor kiválaszthatja az ISE helye az integrációs fiók.

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="http://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [elszigetelt logikai alkalmazásokból az Azure virtuális hálózatok csatlakoztatása](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
