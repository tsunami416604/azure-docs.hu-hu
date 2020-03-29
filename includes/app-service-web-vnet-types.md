---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671511"
---
* A több-bérlős rendszerek, amelyek támogatják a teljes körű árképzési tervek, kivéve elszigetelt
* Az App Service-környezet (ASE), amely telepíti a virtuális hálózatba, és támogatja az elkülönített díjszabási terv alkalmazások

Ez a dokumentum a virtuális hálózat integrációs szolgáltatás, amely a több-bérlős alkalmazások hoz használható. Ha az alkalmazás az [App Service-környezetben,][ASEintro]majd már egy virtuális hálózatban, és nem igényel a Virtuálishálózat-integráció szolgáltatás eléréséhez erőforrások ugyanabban a virtuális hálózaton. Az összes hálózati funkcióval kapcsolatos részletekért olvassa el az [App Service hálózati szolgáltatásait][Networkingfeatures]

A Virtuálishálózat-integráció hozzáférést biztosít az alkalmazásnak a virtuális hálózat erőforrásaihoz, de nem biztosít bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatról. A privát webhely-hozzáférés azt a következőket teszi, hogy az alkalmazás csak magánhálózatról, például egy Azure virtuális hálózatról érhető el. A virtuális hálózat integrációja csak az alkalmazásból a virtuális hálózatba érkező kimenő hívások hoz. A virtuális hálózat integrációs szolgáltatás másképp viselkedik, ha a virtuális hálózatok ugyanabban a régióban és más régiókban a virtuális hálózatok. A Virtuálishálózat-integráció szolgáltatás két változatban rendelkezik.

* Regionális virtuális hálózat integrációja – Ha ugyanabban a régióban csatlakozik az Erőforrás-kezelő virtuális hálózataihoz, rendelkeznie kell egy dedikált alhálózattal a virtuális hálózatban, amelyhez integrálja. 
* Átjáró szükséges virtuális hálózat integráció – Ha más régiókban lévő virtuális hálózatokhoz vagy ugyanabban a régióban egy klasszikus virtuális hálózathoz csatlakozik, a célvirtuális hálózatban kiépített virtuális hálózati átjáróra van szükség.

A VNet-integráció funkciói:

* standard, prémium, premiumv2 vagy elastic premium díjcsomag szükséges 
* TCP és UDP támogatása
* Az App Service-alkalmazások és a Function-alkalmazások

Vannak dolgok, amelyeket a virtuális hálózat-integráció nem támogat, többek között:

* meghajtó felszerelése
* AD-integráció 
* NetBios

Átjáró szükséges Virtuálishálózat-integráció csak hozzáférést biztosít az erőforrásokhoz a cél virtuális hálózat vagy a cél virtuális hálózathoz társviszony-létesítési vagy VPN-kapcsolattal csatlakoztatott hálózatok. Átjáró szükséges VNet-integráció nem teszi lehetővé a hozzáférést az ExpressRoute-kapcsolatok között elérhető erőforrásokhoz, vagy szolgáltatásvégpontokkal működik. 

Függetlenül attól, hogy a használt verzió, VNet integration ad az alkalmazás hozzáférést biztosít az erőforrásokhoz a virtuális hálózatban, de nem ad bejövő privát hozzáférést az alkalmazáshoz a virtuális hálózatról. A privát webhely-hozzáférés azt a következőket teszi, hogy az alkalmazás csak magánhálózatról, például egy Azure virtuális hálózatról érhető el. A virtuális hálózat integrációja csak az alkalmazásból a virtuális hálózatba érkező kimenő hívások hoz. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features