---
title: Azure alkalmazás ajánlat előfeltételei | Azure Piactér
description: Az Azure-alkalmazások közzétételének előfeltételei az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281748"
---
# <a name="azure-application-prerequisites"></a>Az Azure alkalmazás előfeltételei

Ez a cikk ismerteti a felügyelt alkalmazásajánlat azure piactéren való közzétételének technikai és üzleti előfeltételeit.  Ha még nem tette meg, tekintse át az alábbi információforrásokat:
- A termékváltozat típusától függően vagy [Azure-alkalmazások: Megoldássablon-ajánlat közzétételi útmutató](../../marketplace-solution-templates.md) vagy [Azure-alkalmazások: Felügyelt alkalmazásajánlat közzétételi útmutatója](../../marketplace-managed-apps.md)
- [Megoldássablonok és felügyelt alkalmazások létrehozása az Azure Marketplace-hez](https://channel9.msdn.com/Events/Build/2018/BRK3603) – videó


## <a name="technical-requirements"></a>Technikai követelmények

A műszaki követelmények a következő elemeket foglalják magukban:

*   Az Azure Resource Manager-sablonok további információt [az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Ez a cikk egy Azure Resource Manager-sablon szerkezetét ismerteti. Bemutatja a sablon különböző szakaszait és az ezekben a szakaszokban elérhető tulajdonságokat. A sablon JSON-ból és kifejezésekből áll, amelyek segítségével értékeket hozhat létre a központi telepítéshez. 
* Azure gyorsindítási sablonok.<br> További információkért lásd:

  * [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/). Az Azure-erőforrások üzembe helyezése az Azure Resource Manageren keresztül közösségi hozzájárulással készült sablonokkal további műveletekhez. Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.
  * [GitHub: Az Azure Resource Manager rövid útmutató sablonjai](https://github.com/azure/azure-quickstart-templates). Ez a tártár tartalmazza a közösség által jelenleg elérhető Azure Resource Manager-sablonokat. A kereshető sablonindex https://azure.microsoft.com/documentation/templates/a helyen marad.
* Felhasználói felület definíciójának létrehozása<br>
További információ: [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz.](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview) Ez a cikk bemutatja a createUiDefinition.json fájl alapfogalmait. Az Azure Portal ezt a fájlt használja a felhasználói felület létrehozásához felügyelt alkalmazás létrehozásához.


## <a name="business-requirements"></a>Üzleti követelmények

Az üzleti követelmények a következő eljárási, szerződéses és jogi kötelezettségeket tartalmazzák:

* Regisztrált Cloud Marketplace Kiadónak kell lennie. Ha még nincs regisztrálva, kövesse a ["Legyen felhőbeli piactér-közzétevővé"](https://docs.microsoft.com/azure/marketplace/become-publisher
)című cikklépéseit.

>[!NOTE]
>Ugyanazzal a Microsoft Developer Center regisztrációs fiókkal kell bejelentkeznie a Cloud Partner Portalszolgáltatásba. Az Azure Marketplace-ajánlatokhoz csak egy Microsoft-fiókkal kell rendelkeznie. Ez a fiók nem lehet egyedi szolgáltatásokra vagy ajánlatokra jellemző.

* A vállalatnak (vagy leányvállalatának) az Azure Marketplace által támogatott országból/régióból kell származnia. Ezen országok/régiók aktuális listáját a [Microsoft Azure Piactér részvételi szabályzatai című témakörben tetszetős témakörben található.](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)
* A terméknek olyan módon kell licencelnie, amely kompatibilis az Azure Marketplace által támogatott számlázási modellekkel. További információ: [Számlázási lehetőségek](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) az Azure Marketplace-en.
* Ön felelős azért, hogy a technikai támogatást üzletileg ésszerű módon elérhetővé tegye az ügyfelek számára. Ez a támogatás lehet ingyenes, fizetett, vagy a közösségi megközelítések.
* Ön felelős a szoftver és a harmadik féltől származó szoftverfüggőségek licencelésért.
* Olyan tartalmat kell megadnia, amely megfelel az ajánlatnak az Azure Marketplace-en és az Azure Portalon való felsorolásfeltételeinek.
* El kell fogadnia a Microsoft Azure Piactér részvételi szabályzatának és kiadói szerződésének feltételeit.
* Be kell tartania a Microsoft Azure webhelyhasználati feltételeit, a Microsoft adatvédelmi nyilatkozatát és a Microsoft Azure certified programszerződését.


## <a name="publishing-requirements"></a>Közzétételi követelmények

Új Azure-alkalmazásajánlat közzétételéhez meg kell felelnie az alábbi előfeltételeknek:

* Készítse elő a metaadatok használatát. Az alábbi lista (nem teljes) egy példát mutat be erre a metaadatra:
  * A cím
  * Leírás (HTML formátumban)
  * Emblémakép (PNG formátumban) és ezekben a rögzített képméretekben: 40 x 40 képpont, 90 x 90 képpont, 115 x 115 képpont és 255 x 115 képpont.
* Használati *feltételek* és *adatvédelmi irányelvek*
* Alkalmazási dokumentáció
* Támogatási kapcsolattartók


## <a name="next-steps"></a>További lépések

Miután teljesítette az összes követelményt, készen áll egy [Azure-alkalmazásajánlat létrehozására.](./cpp-create-offer.md) 
 
