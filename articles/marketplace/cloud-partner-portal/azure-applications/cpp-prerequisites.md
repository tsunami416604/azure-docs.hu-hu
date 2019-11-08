---
title: Az Azure-alkalmazások ajánlatának előfeltételei | Azure piactér
description: Azure-alkalmazások Azure Marketplace-en való közzétételének előfeltételei.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826138"
---
# <a name="azure-application-prerequisites"></a>Azure-alkalmazások előfeltételei

Ez a cikk a felügyelt alkalmazások Azure Marketplace-en való közzétételének technikai és üzleti előfeltételeit ismerteti.  Ha még nem tette meg, tekintse át a következő információforrásokat:
- Az SKU típusától függően vagy [Azure-alkalmazások: megoldási sablon ajánlat közzétételi útmutató](../../marketplace-solution-templates.md) vagy [Azure-alkalmazások: felügyelt alkalmazás ajánlatának közzétételi útmutatója](../../marketplace-managed-apps.md)
- [Megoldási sablonok és felügyelt alkalmazások készítése az Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) videóhoz


## <a name="technical-requirements"></a>Technikai követelmények

A technikai követelmények a következő elemeket tartalmazzák:

*   További információ a sablonok Azure Resource Manageráról: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Ez a cikk egy Azure Resource Manager sablon szerkezetét ismerteti. Bemutatja a sablon különböző részeit és az ezekben a részekben elérhető tulajdonságokat. A sablon JSON-ből és kifejezésből áll, amelyek használatával értékeket lehet létrehozni a központi telepítéshez. 
* Azure Gyorsindítás sablonok.<br> További információkért lásd:

  * [Azure Gyorsindítás sablonok](https://azure.microsoft.com/documentation/templates/). A közösségtől származó sablonok révén hatékonyabban helyezheti üzembe az Azure-erőforrásokat az Azure Resource Managerrel. Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.
  * [GitHub: Azure Resource Manager gyorsindító-sablonok](https://github.com/azure/azure-quickstart-templates). Ez a tárház tartalmazza a Közösség által biztosított összes jelenleg elérhető Azure Resource Manager-sablont. A kereshető sablon indexe https://azure.microsoft.com/documentation/templates/on marad.
* Felhasználói felület definíciójának létrehozása<br>
További információkért lásd: [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Ez a cikk bemutatja az createUiDefinition. JSON fájl alapvető fogalmait. A Azure Portal ezzel a fájllal hozza létre a felügyelt alkalmazások létrehozásához használt felhasználói felületet.


## <a name="business-requirements"></a>Üzleti feltételek

Az üzleti követelmények a következő eljárási, szerződéses és jogi kötelezettségeket foglalják magukban:

* A felhőalapú piactéren regisztrált közzétevőnek kell lennie. Ha nincs regisztrálva, kövesse a cikk lépéseit a [Cloud Marketplace Publisherben](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>A Cloud Partner Portalba való bejelentkezéshez ugyanazt a Microsoft Developer Center regisztrációs fiókot kell használnia. Az Azure Marketplace-ajánlatokhoz csak egy Microsoft-fiók tartozhat. Ez a fiók nem feltétlenül egyedi szolgáltatásokra vagy ajánlatokra vonatkozik.

* A vállalatnak (vagy annak leányvállalatának) az Azure piactér által támogatott értékesítési ország/régió területén kell lennie. Ezen országok/régiók aktuális listája: [Microsoft Azure Marketplace részvételi szabályzatok](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* A terméknek licenccel kell rendelkeznie, amely kompatibilis az Azure piactér által támogatott számlázási modellekkel. További információ: [Számlázási lehetőségek](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) az Azure Marketplace-en.
* Ön felelős azért, hogy üzleti szempontból ésszerű módon biztosítson technikai támogatást az ügyfeleknek. Ez a támogatás ingyenes, fizetős vagy közösségi megközelítésekkel is elvégezhető.
* Ön felelős a szoftver és a harmadik féltől származó szoftverek függőségeinek licenceléséhez.
* Olyan tartalmat kell megadnia, amely megfelel az ajánlat feltételeinek, hogy megjelenjenek az Azure Marketplace-en és a Azure Portal.
* El kell fogadnia a Microsoft Azure Marketplace részvételi szabályzatok és a kiadói szerződés feltételeit.
* Be kell tartania a Microsoft Azure webhely használati feltételeit, a Microsoft adatvédelmi nyilatkozatát és a Microsoft Azure Certified program szerződését.


## <a name="publishing-requirements"></a>Közzétételi követelmények

Új Azure-alkalmazási ajánlat közzétételéhez a következő előfeltételeknek kell megfelelnie:

* A metaadatok használatra készen állnak. A következő lista (nem teljes) a metaadatok példáját mutatja be:
  * Egy cím
  * Leírás (HTML formátumban)
  * Egy embléma képe (PNG formátumban) és a rögzített képméretekben: 40 x 40 képpont, 90 x 90 képpont, 115 x 115 képpont és 255 x 115 képpont.
* Használati *feltételek* és *adatvédelmi szabályzatok* dokumentumai
* Alkalmazás dokumentációja
* Támogatási kapcsolattartók


## <a name="next-steps"></a>További lépések

Ha teljesíti az összes követelményt, készen áll [egy Azure-alkalmazási ajánlat létrehozására](./cpp-create-offer.md). 
 
