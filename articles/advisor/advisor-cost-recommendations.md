---
title: A szolgáltatási költségek csökkentése a Azure Advisor használatával | Microsoft Docs
description: A Azure Advisor használatával optimalizálhatja az Azure-beli üzemelő példányok költségeit.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 96e939f8e3da58a123d9a6733b71b74c2ff0ba87
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311915"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>A szolgáltatási költségek csökkentése Azure Advisor használatával

Az Advisor az inaktív és a kihasználatlan erőforrások azonosításával segít optimalizálni és csökkenteni az Azure-t teljes egészében. A Cost javaslatait az Advisor irányítópultjának **Cost (Cost** ) lapján érheti el.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>A virtuális gépek a kihasználatlan példányok átméretezésével vagy leállításával optimalizálható 

Bár bizonyos alkalmazási forgatókönyvek alacsony kihasználtságot eredményezhetnek a tervezéssel, gyakran pénzt takaríthat meg a virtuális gépek méretének és számának kezelésével. Az Advisor 7 napig figyeli a virtuális gépek használatát, majd azonosítja az alacsony kihasználtságú virtuális gépeket. A virtuális gépek alacsony kihasználtságnak számítanak, ha a processzor kihasználtsága 5% vagy kevesebb, és a hálózati kihasználtsága kevesebb, mint 2%, vagy ha a jelenlegi számítási feladatot kisebb virtuálisgép-mérettel lehet elfogadni.

Az Advisor megjeleníti a virtuális gép futtatásának becsült költségeit, így dönthet úgy, hogy leállítja vagy átméretezi.

Ha agresszíven szeretne lenni a kihasználatlan virtuális gépek azonosításához, a CPU-kihasználtsági szabályt előfizetések alapján is módosíthatja.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>A költségek csökkentése a nem kiépített ExpressRoute-áramkörök eltávolításával

Az Advisor azokat az ExpressRoute-áramköröket azonosítja, amelyekben a szolgáltató állapota *nem* több mint egy hónapra van kiépítve, és az áramkör törlését javasolja, ha nem tervezi az áramkör kiépítését a kapcsolati szolgáltatóval.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Csökkentse a költségeket a tétlen virtuális hálózati átjárók törlésével vagy újrakonfigurálásával

Az Advisor olyan virtuális hálózati átjárókat azonosít, amelyek több mint 90 napig inaktívak. Mivel ezeket az átjárókat óránként számlázzák, érdemes lehet újrakonfigurálni vagy törölni őket, ha már nem kívánja használni őket. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Fenntartott virtuálisgép-példányok vásárlása az utólagos elszámolású költségek megtakarítása érdekében

Az Advisor áttekinti a virtuális gépek használatát az elmúlt 30 napban, és megállapítja, hogy pénzt takaríthat meg egy Azure-foglalás megvásárlásával. Az Advisor megmutatja azokat a régiókat és méreteket, ahol a legtöbb megtakarítás megtakarítást eredményez, és a megvásárolt foglalások becsült megtakarítását fogja megjeleníteni. Az Azure-foglalások segítségével előre megvásárolhatja a virtuális gépek alapdíjait. A kedvezmények automatikusan érvényesek az új vagy meglévő virtuális gépekre, amelyek mérete és régiója megegyezik a foglalásokkal. [További információ a Azure Reserved VM Instancesról.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Az Advisor emellett értesíti Önt a fenntartott példányokról, amelyek a következő 30 napban lejárnak. Javasoljuk, hogy az utólagos elszámolású díjszabás kifizetésének elkerüléséhez új fenntartott példányokat vásároljon.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>A nem társított nyilvános IP-címek törlése a pénz megtakarítása érdekében

Az Advisor olyan nyilvános IP-címeket azonosít, amelyek jelenleg nem tartoznak Azure-erőforrásokhoz, például Terheléselosztóokhoz vagy virtuális gépekhez. Ezek a nyilvános IP-címek névleges díjat számítanak fel. Ha nem tervezi használni őket, a törlésük költségmegtakarítást eredményezhet.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Sikertelen Azure Data Factory folyamatok törlése

A Azure Advisor felismeri Azure Data Factory folyamatokat, amelyek többször is sikertelenek, és azt javasoljuk, hogy hárítsa el a problémákat, vagy törölje a sikertelen folyamatokat, ha már nincs rájuk szükség. Ezeknek a folyamatoknak a számlázása akkor is történik, ha nem szolgálnak Önnek, amíg nem működnek. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Szabványos Pillanatképek használata Managed Diskshoz
A 60%-os megtakarítás érdekében javasoljuk, hogy a pillanatképeket a normál tárolóban tárolja, a szülő lemez tárolási típusától függetlenül. Ez az alapértelmezett beállítás Managed Disks Pillanatképek esetében. Azure Advisor azonosítja a Premium Storage tárolt pillanatképeket, és javasolja, hogy a pillanatképet a premiumról a standard Storage-ra kell áttelepíteni. [További információ a felügyelt lemez díjszabásáról](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>A Cost javaslatokhoz való hozzáférés Azure Advisor

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

2.  Az Advisor Irányítópultján kattintson a **Cost (Cost** ) fülre.

## <a name="next-steps"></a>További lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-cost-recommendations.md)
* [Advisor – magas rendelkezésre állási javaslatok](advisor-cost-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-cost-recommendations.md)
