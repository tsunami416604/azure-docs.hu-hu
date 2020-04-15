---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312842"
---
* A több-bérlős rendszerek, amelyek támogatják a teljes körű díjszabási tervek, kivéve elszigetelt.
* Az App Service-környezet, amely telepíti a virtuális hálózatba, és támogatja az elkülönített díjszabási terv alkalmazások.

A Virtuálishálózat-integráció szolgáltatás több-bérlős alkalmazásokban használatos. Ha az alkalmazás az [App Service-környezetben,][ASEintro]majd már egy virtuális hálózatban, és nem igényel a Virtuálishálózat-integráció szolgáltatás eléréséhez erőforrások ugyanabban a virtuális hálózaton. Az összes hálózati funkcióról az [App Service hálózati szolgáltatásai][Networkingfeatures]című témakörben talál további információt.

Virtuális hálózat integrációja hozzáférést biztosít az alkalmazás a virtuális hálózat ban lévő erőforrásokhoz, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatról. A privát webhely-hozzáférés azt a következőket teszi, hogy egy alkalmazás csak magánhálózatról, például egy Azure virtuális hálózaton keresztül érhető el. A virtuális hálózat integrációja csak az alkalmazásból a virtuális hálózatba érkező kimenő hívások kezdeményezéséhez használatos. A Virtuálishálózat-integráció szolgáltatás eltérően viselkedik, ha a virtuális hálózat tal ugyanabban a régióban és más régiókban a virtuális hálózattal használják. A Virtuálishálózat-integráció szolgáltatás két változatban:

* **Regionális virtuális hálózat integrációja:** Ha ugyanabban a régióban csatlakozik az Azure Resource Manager virtuális hálózatokhoz, rendelkeznie kell egy dedikált alhálózattal a virtuális hálózatban, amelyhez integrálja.
* **Átjáró által igényelt virtuális hálózat integráció:** Ha más régiókban lévő virtuális hálózathoz vagy ugyanabban a régióban egy klasszikus virtuális hálózathoz csatlakozik, a célvirtuális hálózatban kiépített Azure virtuális hálózati átjáróra van szükség.

A VNet-integráció funkciói:

* Standard, Premium, PremiumV2 vagy Elastic Premium díjszabási csomag megkövetelése.
* Támogatja a TCP és UDP.
* Az Azure App Service-alkalmazások és a függvényalkalmazások együttműködnek.

Vannak olyan dolgok, amelyeket a virtuális hálózat integrációja nem támogat, például:

* Meghajtó felszerelése.
* Active Directory-integráció.
* Netbios.

Az átjáró által igényelt virtuális hálózat-integráció csak a cél virtuális hálózatban vagy a cél virtuális hálózathoz társviszony-létesítéssel vagy VPN-kapcsolattal csatlakoztatott hálózatokban biztosít hozzáférést az erőforrásokhoz. Az átjáróáltal igényelt virtuális hálózat integrációja nem teszi lehetővé az Azure ExpressRoute-kapcsolatokban elérhető erőforrásokhoz való hozzáférést, vagy szolgáltatásvégekkel működik.

Függetlenül attól, hogy a használt verzió, vnet-integráció hozzáférést biztosít az alkalmazás a virtuális hálózat ban lévő erőforrásokhoz, de nem ad bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatról. A privát webhely-hozzáférés azt a következőket teszi, hogy az alkalmazás csak magánhálózatról, például egy Azure virtuális hálózaton keresztül érhető el. A virtuális hálózat integrációja csak az alkalmazásból a virtuális hálózatba érkező kimenő hívások hoz.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
