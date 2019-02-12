---
title: Az Azure Application ajánlat előfeltételei |} A Microsoft Docs
description: Az előfeltételek az Azure-alkalmazások közzététele az Azure Marketplace-en kínálnak.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: b3f978635127ef6aabb123d1c95b76ed06fccbbf
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097790"
---
# <a name="azure-application-prerequisites"></a>Az Azure application Előfeltételek

Ez a cikk egy kezelt alkalmazásra vonatkozó ajánlat az Azure piactér közzétételi technikai és üzleti előfeltételeit ismerteti.  Ha még nem tette meg, tekintse meg a videó [létrehozása Megoldássablonokkal, és a felügyelt alkalmazások az Azure Marketplace-en](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="technical-requirements"></a>Technikai követelmények

A technikai követelményekről a következő elemeket tartalmazza:

*   Az Azure Resource Manager-sablonokat, további információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Ez a cikk ismerteti az Azure Resource Manager-sablonok szerkezetének. Egy sablon és az elérhető tulajdonságok köre szakaszt az eltérő szakaszok tükrözze. A sablon JSON-t és kifejezések, amelyek segítségével kialakíthatja az üzemelő példány értékeit áll. 
* Az Azure gyorsindítási sablonok.<br> További információkért lásd:

  * [Az Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/). A közösségtől származó sablonok révén hatékonyabban helyezheti üzembe az Azure-erőforrásokat az Azure Resource Managerrel. Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.
  * [GitHub: Az Azure Resource Manager gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates). Ebben a tárházban az összes jelenleg elérhető Azure Resource Manager sablon a Közösség által biztosított tartalmazza. Egy sablon kereshető indexet kell tartani, https://azure.microsoft.com/en-us/documentation/templates/.
* UI-definíció létrehozása<br>
További információkért lásd: [létrehozása az Azure portal felhasználói felületet a felügyelt alkalmazás](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Ez a cikk az alapfogalmakat, a createUiDefinition.json fájl mutatja be. Az Azure Portalon ezt a fájlt használja egy felügyelt alkalmazás létrehozásához a felhasználói felület létrehozásához.


## <a name="business-requirements"></a>Üzleti feltételek

Az üzleti követelmények a következők a következő eljárási, és a jogi szerződéses kötelezettségek:

* Regisztrált Felhőbeli piactér kiadói kell lennie. Ha nem regisztrált, kövesse a cikkben legyen a Felhőbeli piactér kiadói.

>[!NOTE]
>A Cloud Partner portálra való bejelentkezéshez ugyanazt a Microsoft Developer Center regisztrációs fiókot kell használnia. Az Azure piactér-i ajánlatainak közzétételéhez egyetlen Microsoft-fiókkal kell rendelkeznie. Ez a fiók nem lehet adott egyes szolgáltatásokat vagy ajánlatokat.

* A vállalatnak (vagy az leányvállalatának) kell lennie egy értékesítési-a-ország az Azure piactér által támogatott. Ezekben az országokban aktuális listáját lásd: [a Microsoft Azure Marketplace részvételi szabályzata](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* A termék úgy, hogy az Azure piactér által támogatott számlázási modellek kompatibilis licenccel kell rendelkezniük. További információkért lásd: [számlázási lehetőségekkel](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) az Azure piactéren.
* Ön felelős az ügyfelek számára elérhetővé tétele az műszaki támogatást, olyan üzletileg ésszerű módon. Ez a támogatás ingyenes, fizetős, vagy közösségi megközelítések keresztül lehet.
* Ön felelős a szoftver- és külső függőségek licencelése.
* Meg kell adnia a tartalmat, amely megfelel az Azure Marketplace-en és az Azure Portalon való megjelentetéséhez ajánlat feltételeit.
* El kell fogadnia a Microsoft Azure Marketplace részvételi szabályzata és a kiadói szerződés feltételeit.
* Be kell tartania a Microsoft Azure webhelyhasználati feltételek, a Microsoft adatvédelmi nyilatkozat és a Microsoft Azure Certified Program szerződését.


## <a name="publishing-requirements"></a>Közzétételi követelményei

Egy új Azure-alkalmazás-ajánlat közzétételéhez a következő előfeltételeknek kell megfelelnie:

* Rendelkezik a metaadatok, készen áll a használatra. Az alábbi lista (nem teljes) ezeket a metaadatokat egy példát mutat be:
  * Cím
  * Egy leírást (HTML formátumban)
  * Emblémakép (PNG formátumban), valamint ezek a rögzített lemezkép mérete: 40 x 40 képpont, 90 x 90 képpont, 115 x 115 képpont és 255 x 115 képpont.
* A *használati feltételeket tartalmazó fájl* és a egy *adatvédelmi szabályzat*
* Dokumentáció
* Támogatási kapcsolattartók


## <a name="next-steps"></a>További lépések

Ha teljesítette a szükséges összes követelmény, készen áll a fogja [hozzon létre egy Azure-alkalmazás ajánlatot](./cpp-create-offer.md). 
 
