---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671511"
---
* A több-bérlős rendszerek, amelyek támogatják az elkülönített árképzési csomagok teljes körét
* A VNet üzembe helyező és az elkülönített díjszabási csomagot támogató App Service Environment

Ez a dokumentum a több-bérlős alkalmazásokban használható VNet-integrációs szolgáltatáson keresztül érhető el. Ha az alkalmazás [app Service Environmentban][ASEintro]van, akkor már egy VNet van, és nem igényli a VNet integrációs funkciójának használatát az erőforrások eléréséhez ugyanabban a VNet. Az összes hálózati szolgáltatással kapcsolatos részletekért olvassa el [app Service hálózati szolgáltatások][Networkingfeatures]

A VNet integrációja lehetővé teszi az alkalmazás számára a virtuális hálózat erőforrásaihoz való hozzáférést, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a VNet. A privát hely elérése arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet. A VNet-integrációs szolgáltatás másképp viselkedik, ha a virtuális hálózatok ugyanabban a régióban és más régiókban virtuális hálózatok van használatban. A VNet-integrációs szolgáltatásnak két változata van.

* Regionális VNet-integráció – Ha ugyanahhoz a régióhoz kapcsolódik a Resource Manager-virtuális hálózatok, egy dedikált alhálózattal kell rendelkeznie abban a VNet, amelyhez az integrációt végzi. 
* Átjáró szükséges VNet-integráció – ha más régiókban lévő virtuális hálózatok vagy ugyanazon régióban lévő klasszikus VNet kapcsolódik, akkor szüksége van egy Virtual Network átjáróra, amelyet a cél VNet kiépít.

A VNet integrációs funkciói:

* Standard, prémium, PremiumV2 vagy rugalmas prémium díjszabási csomag megkövetelése 
* a TCP és az UDP támogatása
* App Service-alkalmazások és-függvények használata

Néhány dolog, amit a VNet-integráció nem támogat, beleértve a következőket:

* meghajtó csatlakoztatása
* AD-integráció 
* NetBios

Az átjáróhoz szükséges VNet-integráció csak a célként megadott VNet lévő erőforrásokhoz, illetve a VNet vagy a VPN-hez csatlakozó hálózatokhoz biztosít hozzáférést. Az átjáróhoz szükséges VNet-integráció nem teszi lehetővé a ExpressRoute-kapcsolatokon keresztül elérhető erőforrásokhoz való hozzáférést, vagy a szolgáltatási végpontokkal való munkát. 

A használt verziótól függetlenül a VNet integrációja lehetővé teszi az alkalmazás számára a virtuális hálózat erőforrásaihoz való hozzáférést, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatból. A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features