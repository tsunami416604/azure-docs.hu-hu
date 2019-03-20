---
title: Az Azure Advisor használatával szolgáltatás költségek csökkentése |} A Microsoft Docs
description: Az Azure Advisor használata az Azure-környezetek költségeinek optimalizálása érdekében.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 140c8b2ab9b7985652a6474a1a9373e0d453b9e6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900726"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Az Azure Advisor használatával szolgáltatás költségek csökkentése

Az Advisor segít optimalizálni, és csökkentheti a teljes Azure felhőköltéseiket üresjárati és kihasználatlan erőforrások azonosítása. Akkor is költséget javaslatainak a **költség** az Advisor irányítópult lapon.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizálhatja a virtuális gép felhőköltéseiket kihasználatlan példányok leállítása és átméretezése 

Egyes alkalmazás-forgatókönyvek elvárt eredményezhetnek alacsony kihasználtságot, bár gyakran pénzt takaríthat, mivel kezeli az méretét és a virtuális gépek száma. Az Advisor 7 napig a virtuális gép használatát figyeli, és alacsony-kihasználtság virtuális gépek azonosítja. Virtuális gépek számítanak a kis-kihasználtsága a CPU-kihasználtság esetén 5 % vagy kevesebb és a hálózathasználat kevesebb mint 2 %, vagy ha a jelenlegi terhelés is elhelyezkedhetnek, a kisebb méretű virtuális gépet.

Az Advisor továbbra is a virtuális gép fut, így dönthet úgy, hogy állítsa le vagy méretezze át a becsült költségeit mutatja.

Ha szeretné a virtuális gépek kihasználtságát az azonosító agresszívebb legyen, módosíthatja a átlagos CPU kihasználtsága szabály minden előfizetés.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>A rendszer az ExpressRoute-Kapcsolatcsoportok kiküszöbölése révén csökkentheti a költségeket

Az Advisor azonosítja, amelyeket a szolgáltatói állapotú ExpressRoute-Kapcsolatcsoportok *nincs kiépítve* több mint egy hónapja, és javasolja a kapcsolatcsoport törlése, ha nem tervezi a kapcsolat üzembe helyezi a kapcsolatcsoportot szolgáltató.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Csökkentheti a költségeket törlése vagy újrakonfigurálása inaktív virtuális hálózati átjárók

Az Advisor azonosítja a virtuális hálózat kapuk, amely rendelkezik több mint 90 napja inaktív volt. Mivel az átjáró számlázása óránként, érdemes újrakonfigurálása vagy törölni, ha nem kívánja elszámolt. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Megtakarítást érhet el a használatalapú fizetéses költségekhez képest fenntartott virtuálisgép-példányok vásárlásával

Az Advisor fog tekintse át a virtuális gép használatát az elmúlt 30 napban, és határozza meg, ha pénzt menthető egy Azure foglalás megvásárlásával. Az Advisor bemutatja, a régiók és a méreteket, ahol potenciálisan rendelkezik a legtöbb megtakarítások és jelennek meg azt a becsült megtakarítások a foglalásokat vásárol. 

Az Azure-foglalásokat akkor is előre történő kifizetését a virtuális gépek számára. Új vagy meglévő virtuális gépek, amelyek az azonos méretű és régió szerint a foglalások automatikusan alkalmazza a kedvezményeket jelentenek. [További információ az Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Társítatlan nyilvános IP-címet törölni pénzt takaríthat meg

Az Advisor azonosítja a nyilvános IP-címek, amelyek nem jelenleg tartoznak, mint például a Terheléselosztókról és a virtuális gépek Azure-erőforrásokhoz. A nyilvános IP-cím egy névleges díj címek jár. Ha nem tervezi használni őket, költségmegtakarítást törléssel eredményezhet.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Az Azure Advisor díjakkal kapcsolatos ajánlások elérése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **költség** fülre.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatok kapcsolatos további információkért lásd:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Advisor-teljesítményajánlások](advisor-cost-recommendations.md)
* [Az Advisor magas rendelkezésre állás – javaslatok](advisor-cost-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-cost-recommendations.md)
