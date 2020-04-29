---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312842"
---
* A több-bérlős rendszerek, amelyek támogatják az elkülönített díjszabási csomagok teljes skáláját.
* A App Service Environment, amely üzembe helyezi a VNet, és támogatja az elkülönített díjszabási csomagot.

A VNet-integrációs szolgáltatás a több-bérlős alkalmazásokban használatos. Ha az alkalmazás [app Service Environmentban][ASEintro]van, akkor már egy VNet van, és nem igényli a VNet integrációs funkciójának használatát az erőforrások eléréséhez ugyanabban a VNet. Az összes hálózati szolgáltatással kapcsolatos további információkért lásd: [app Service hálózatkezelési funkciók][Networkingfeatures].

A VNet integrációja lehetővé teszi az alkalmazás számára a VNet lévő erőforrásokhoz való hozzáférést, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a VNet. A privát helyhez való hozzáférés arra utal, hogy egy alkalmazás csak magánhálózaton keresztül érhető el, például egy Azure-beli virtuális hálózaton belülről. A VNet-integráció kizárólag az alkalmazásból érkező kimenő hívások VNet való elvégzésére szolgál. A VNet-integrációs funkció másképp viselkedik, ha a VNet-ban ugyanabban a régióban és más régiókban VNet van használatban. A VNet-integrációs szolgáltatás két változattal rendelkezik:

* **Regionális VNet-integráció**: Ha ugyanahhoz a régióhoz csatlakozik Azure Resource Manager virtuális hálózatokhoz, dedikált alhálózattal kell rendelkeznie azon a VNet, amelybe integrálni kívánja.
* **Átjáró által megkövetelt VNet-integráció**: Ha más régiókban található VNet vagy ugyanazon a régióban található klasszikus virtuális hálózatra csatlakozik, szüksége lesz egy Azure Virtual Network-átjáróra, amelyet a cél VNet kell kiépíteni.

A VNet integrációs funkciói:

* Standard, prémium, PremiumV2 vagy rugalmas prémium díjszabási csomag szükséges.
* A TCP és az UDP támogatása.
* Azure App Service-alkalmazások és-függvények használata.

Vannak olyan dolgok, amelyeket a VNet-integráció nem támogat, például:

* Meghajtó csatlakoztatása.
* Active Directory integráció.
* NetBIOS.

Az átjáróval megkövetelt VNet-integráció csak a célként megadott VNet, illetve a VNet és a virtuális magánhálózatok használatával csatlakozó hálózatokban biztosít hozzáférést az erőforrásokhoz. Az átjáróval megkövetelt VNet-integráció nem teszi lehetővé az Azure ExpressRoute-kapcsolatokon keresztül elérhető erőforrásokhoz való hozzáférést, vagy szolgáltatási végpontokkal működik.

A használt verziótól függetlenül a VNet integrációja lehetővé teszi az alkalmazás számára a VNet lévő erőforrásokhoz való hozzáférést, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a VNet. A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak a magánhálózaton keresztül érhető el, például egy Azure-VNet belülről. A VNet-integráció csak az alkalmazásból kimenő hívásokat tesz a VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
