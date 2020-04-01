---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419570"
---
* A több-bérlős rendszerek, amelyek támogatják a teljes körű díjszabási tervek, kivéve elszigetelt.
* Az App Service-környezet, amely telepíti a virtuális hálózatba, és támogatja az elkülönített díjszabási terv alkalmazások.

A Virtuálishálózat-integráció szolgáltatás több-bérlős alkalmazásokban használatos. Ha az alkalmazás az [App Service-környezetben][ASEintro]van, akkor már egy virtuális hálózatban van, és nem igényel a Virtuálishálózat-integráció szolgáltatás használatát az azonos virtuális hálózat erőforrásainak eléréséhez. Az összes hálózati funkcióról az [App Service hálózati szolgáltatásai][Networkingfeatures]című témakörben talál további információt.

A Virtuálishálózat-integráció hozzáférést biztosít az alkalmazásnak a virtuális hálózat erőforrásaihoz, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatról. A privát webhely-hozzáférés azt a következőket teszi, hogy egy alkalmazás csak magánhálózatról, például egy Azure virtuális hálózaton keresztül érhető el. A VNet-integráció csak az alkalmazásból a virtuális hálózatba érkező kimenő hívások hozására szolgál. A Virtuálishálózat-integráció szolgáltatás eltérően viselkedik, ha az ugyanabban a régióban és más régiókban lévő virtuális hálózatokkal használják. A Virtuálishálózat-integráció szolgáltatás két változatban:

* **Regionális virtuális hálózat integrációja:** Ha ugyanabban a régióban csatlakozik az Azure Resource Manager virtuális hálózataihoz, rendelkeznie kell egy dedikált alhálózattal abban a virtuális hálózatban, amelyet integrál.
* **Átjáró által igényelt virtuális hálózat integrációja:** Ha más régiókban lévő virtuális hálózatokhoz vagy ugyanabban a régióban lévő klasszikus virtuális hálózathoz csatlakozik, a célvirtuális hálózatban kiépített Azure virtuális hálózati átjáróra van szükség.

A VNet-integráció funkciói:

* Standard, Premium, PremiumV2 vagy Elastic Premium díjszabási csomag megkövetelése.
* Támogatja a TCP és UDP.
* Az Azure App Service-alkalmazások és a függvényalkalmazások együttműködnek.

Vannak olyan dolgok, amelyeket a virtuális hálózat integrációja nem támogat, például:

* Meghajtó felszerelése.
* Active Directory-integráció.
* Netbios.

Az átjáró által igényelt virtuális hálózat integráció csak a cél virtuális hálózatban vagy a cél virtuális hálózathoz társviszony-létesítéssel vagy VPN-kapcsolattal rendelkező hálózatokban biztosít hozzáférést az erőforrásokhoz. Az átjáróáltal igényelt virtuális hálózat integrációja nem teszi lehetővé az Azure ExpressRoute-kapcsolatokban elérhető erőforrásokhoz való hozzáférést, vagy szolgáltatásvégekkel működik.

Függetlenül attól, hogy a használt verzió, vnet-integráció hozzáférést biztosít az alkalmazás a virtuális hálózat erőforrásaihoz, de nem ad bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatról. A privát webhely-hozzáférés azt a számítógépet illeti, hogy az alkalmazást csak magánhálózatról, például egy Azure virtuális hálózaton keresztül teszi elérhetővé. A virtuális hálózat integrációja csak az alkalmazásból a virtuális hálózatba érkező kimenő hívásokhoz.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
