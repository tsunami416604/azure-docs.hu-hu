---
title: Hozzáférés az Azure virtual Network (VNET) az Azure Logic Apps
description: Ez az áttekintés bemutatja, hogyan elkülönített logic apps integrációs service-környezetek (ISEs) privát, dedikált erőforrásokat használó Azure-beli virtuális hálózatok (Vnetek) lehet csatlakoztatni
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b012f1ac9c5f08a7e74871ca215299904f6b1deb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958645"
---
# <a name="access-to-azure-virtual-network-vnet-resources-from-isolated-azure-logic-apps"></a>Elkülönített Azure Logic Apps az Azure Virtual Network (VNET) erőforrásokhoz való hozzáférés

> [!NOTE]
> Ez a funkció akkor a *privát előzetes verzió*. Hozzáférés igényléséhez [itt csatlakozni a kérelem létrehozása](https://aka.ms/iseprivatepreview).

Egyes esetekben a logic apps és az integrációs fiókok hozzáférhetnek a biztonságos erőforrások, például virtuális gépek (VM) és a más rendszerekkel vagy belüli szolgáltatások egy [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md). Ez a hozzáférés biztosításához is [hozzon létre egy *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) a logic apps és az integrációs fiókok létrehozására szolgáló helyként. 

![Válassza ki az integrációs service-környezet](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A magán- és elkülönített Logic Apps példány be a virtuális hálózat létrehozása az ISE-ben telepíti. A privát példány dedikált erőforrások, például a storage használ, és a nyilvános "globális" Logic Apps szolgáltatás külön-külön futtatja. Ez a fajta elkülönítés is segít csökkenteni a hatást, amelyet más Azure-bérlőt előfordulhat, hogy az alkalmazások teljesítményét, vagy a ["zajos szomszédok" hatás](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Ez az Áttekintés ismerteti, hogyan létrehozása az ISE segít a logic apps és az integrációs fiókok tud közvetlenül hozzáférni az erőforrásokhoz az Azure virtuális hálózaton belül, és összehasonlítja az ISE-ben és a globális Logic Apps szolgáltatás közötti különbségeket.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Izolált, és a globális

Integrált service-környezet (ISE) az Azure-ban való létrehozásakor választhat egy Azure virtuális Hálózatban, mint egy *társ* környezete számára. Azure üzembe helyezte a privát példány a Logic Apps szolgáltatás az a virtuális hálózat egy elkülönített környezet, amelyben a dedikált erőforrásokon futtatott – a logic apps, és hozzon létre eredményez. Amikor létrehoz egy logikai alkalmazást, az alkalmazás földrajzi hely is hozzáférést biztosít a logikai alkalmazás közvetlen a virtuális Hálózatban található erőforrások is válassza ki a ebben a környezetben.  

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

## <a name="permissions-for-vnet-access"></a>Virtuális hálózat-hozzáférési engedélyei

Amikor létrehoz egy integrációs service-környezet (ISE), egy Azure virtuális hálózaton (VNET) kiválaszthatja egy *társ* környezete számára. Azonban továbbra is *csak* létrehozni a kapcsolatot, vagy *társviszony-létesítés*, amikor hoz létre az ISE-ben. Ezt a kapcsolatot az ISE hozzáférhet a vnetben, amely lehetővé teszi a logic apps, hogy ISE közvetlenül csatlakozhat a virtuális Hálózatban található erőforrások erőforrásokhoz. A helyszíni rendszerek egy virtuális hálózatban, amely kapcsolódik az ISE-ben a logic apps közvetlenül hozzáférhetnek ezekhez a rendszerekhez ezek az elemek egyikének használatával: 

* ISE-ben, hogy a rendszer, például az SQL Server-összekötő

* HTTP-művelet 

* Egyéni összekötő

Helyszíni rendszerekhez, amely nem a virtuális hálózathoz, vagy nem rendelkezik az ISE-összekötők, továbbra is a kapcsolódás után, [beállítása és használata a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md).

Mielőtt választhat egy Azure virtuális hálózat legyen társ környezete számára, szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel kell állítania az Azure Logic Apps szolgáltatás az Azure virtuális hálózat. A feladathoz szükséges, hogy hozzárendelje a **hálózati közreműködő** és **klasszikus közreműködői** szerepkörök az Azure Logic Apps szolgáltatásba. A társviszony-létesítéshez szükséges szerepkör engedélyeivel kapcsolatos további információkért lásd: a [engedélyek létrehozása, módosítása, témakör vagy egy virtuális hálózati társviszony törlése](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok az ISE-ben

A logic apps-integráció service-környezet (ISE) futtató integrációs fiókok is használhatja, de ezeket az integrációs fiókok is használnia kell a *azonos ISE* a társított logikai alkalmazásokat. A Logic apps egy ISE-ben csak azok integrációs a fiókok, amelyek ugyanabban az ISE-ben is lehet hivatkozni. Integrációs fiók létrehozásakor kiválaszthatja az ISE helye az integrációs fiók.

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps fórumára</a>.
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a <a href="http://aka.ms/logicapps-wish" target="_blank">Logic Apps felhasználói visszajelzéseinek oldalára</a>.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [csatlakozhat a virtuális hálózatok (Vnetek) elszigetelt logikai alkalmazásokból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Tudjon meg többet [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg [virtuális hálózat integrációja Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md)
