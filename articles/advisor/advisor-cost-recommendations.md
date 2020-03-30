---
title: A szolgáltatási költségek csökkentése az Azure Advisor használatával
description: Az Azure Advisor használatával optimalizálhatja az Azure-telepítések költségeit.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259694"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>A szolgáltatási költségek csökkentése az Azure Advisor használatával

Az Advisor segít optimalizálni és csökkenteni a teljes Azure-kiadásokat az kihasználatlan és kihasználatlan erőforrások azonosításával.Költségjavaslatokat az Advisor **irányítópultköltség** lapján kaphat.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizálja virtuálisgép-költségeit az alacsony kihasználtságú példányok átméretezésével vagy leállításával 

Bár bizonyos alkalmazási forgatókönyvek alacsony kihasználtságot eredményezhetnek, gyakran pénzt takaríthat meg a virtuális gépek méretének és számának kezelésével. A Tanácsadó speciális kiértékelési modelljei virtuális gépeket vesznek figyelembe a leállításhoz, ha a CPU-használat maximális értékének P95-ös része kevesebb, mint 3%, a hálózati kihasználtság pedig kevesebb, mint 2% egy 7 napos időszak alatt. Virtuális gépek akkor tekinthető a megfelelő méretű, ha lehetséges, hogy illeszkedjen az aktuális terhelésegy kisebb Termékváltozat (ugyanazon termékváltozat családon belül), vagy egy kisebb számú # példány, így az aktuális terhelés nem megy át 80%-os kihasználtság, ha nem a felhasználó előtt munkaterhelések és nem haladja meg a 40%, ha a felhasználó felé néző számítási feladatok. Itt a számítási feladatok típusát a számítási feladat CPU-kihasználtsági jellemzőinek elemzésével határozzuk meg.

Az ajánlott műveletek leállítása vagy átméretezése, amely az erőforrás ajánlott. Az Advisor megmutatja az ajánlott műveletek – átméretezés vagy leállítás – becsült költségmegtakarítását. Az ajánlott művelet átméretezéséhez az Advisor aktuális és cél termékváltozat-információkat is biztosít. 

Ha azt szeretné, hogy agresszívebb azonosítására kihasználatlan virtuális gépek, beállíthatja a CPU-kihasználtsági szabály egy előfizetési alapon.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Csökkentse a költségeket a ki nem épített ExpressRoute-áramkörök eltávolításával

Az Advisor azonosítja azokat az ExpressRoute-áramköröket, amelyek több mint egy hónapja *nincs kiépítve,* és javasolja az áramkör törlését, ha nem tervezi az áramkör kiépítését a kapcsolatszolgáltatóval.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Költségek csökkentése az inaktív virtuális hálózati átjárók törlésével vagy újrakonfigurálásával

Az Advisor azonosítja azokat a virtuális hálózati átjárókat, amelyek több mint 90 napja tétlenek. Mivel ezek az átjárók számlázása óránként van számlázva, érdemes újrakonfigurálni vagy törlésüket, ha már nem kívánja használni őket. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Vásároljon fenntartott virtuálisgép-példányokat a használatalapú fizetéshez képest elért pénzmegtakarítás érdekében

Az Advisor az elmúlt 30 napban áttekinti a virtuális gép használatát, és meghatározza, hogy megtakaríthat-e pénzt egy Azure-foglalás megvásárlásával. Az Advisor megmutatja azokat a régiókat és méreteket, ahol potenciálisan a legtöbb megtakarítást tudja elérni, és megmutatja a foglalások megvásárlásából származó becsült megtakarításokat. Az Azure-foglalások segítségével előre megvásárolhatja a virtuális gépek alapköltségeit. A kedvezmények automatikusan vonatkoznak az új vagy meglévő virtuális gépekre, amelyek mérete és régiója megegyezik a foglalásokkal. [További információ az Azure által fenntartott virtuálisgép-példányokról.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Az Advisor a következő 30 napban lejáró fenntartott példányokról is értesíti Önt. Azt fogja javasolni, hogy vásároljon új fenntartott példányokat, hogy elkerülje a felosztó-ki-ki-díj szabásfizetést.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>A nem társított nyilvános IP-címek törlése, hogy pénzt takarítson meg

Az Advisor azonosítja azokat a nyilvános IP-címeket, amelyek jelenleg nincsenek azure-erőforrásokhoz társítva, például a terheléselosztókhoz vagy a virtuális gépekhez. Ezek a nyilvános IP-címek névleges díjval érkeznek. Ha nem tervezi használni őket, a törlésük költségmegtakarítást eredményezhet.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Meghiúsult Azure Data Factory-folyamatok törlése

Az Azure Advisor észleli az Azure Data Factory-folyamatok, amelyek ismételten sikertelen, és azt javasolja, hogy oldja meg a problémákat, vagy törölje a hibás folyamatokat, ha már nincs rájuk szükség. Ezeket a folyamatokat akkor is ki számlázunk, ha nem szolgálják ki Önt, amíg sikertelenek. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Felügyelt lemezek szabványos pillanatképeinek használata
A 60%-os megtakarítás érdekében javasoljuk, hogy a pillanatképeket a Standard szintű Storage-ban tárolja, a szülőlemez tárolási típusától függetlenül. Ez a beállítás az alapértelmezett beállítás a felügyelt lemezek pillanatképeihez. Az Azure Advisor azonosítja a prémium szintű storage-ban tárolt pillanatképeket, és javasolja a pillanatkép prémium verzióról standard tárhelyre való áttelepítését. [További információ a felügyelt lemez díjszabásáról](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Életciklus-felügyelet használata
Az Azure Advisor az Azure blob storage-objektumok számával, a teljes mérettel és a tranzakciókkal kapcsolatos intelligenciát használja annak észlelésére, hogy egy vagy több tárfiók alkalmas-e arra, hogy lehetővé tegye az életciklus-kezelés a rétegadatokhoz. A rendszer kérni fogja, hogy hozzon létre életciklus-kezelési szabályokat, hogy automatikusan rétegezze az adatokat a ritka elérésű vagy az archiválási szolgáltatásba, hogy optimalizálja a tárolási költségeket, miközben megőrzi az adatokat az Azure blob storage-ban az alkalmazások kompatibilitása érdekében.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>A költségjavaslatok elérése az Azure Advisorban

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Keressen rá és válassza ki az [**Advisort**](https://aka.ms/azureadvisordashboard) bármelyik oldalról.

1. Az **Advisor** irányítópulton válassza a **Költség** lapot.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Advisor-ajánlásokról, olvassa el a következő témakört:
* [Bevezetés a tanácsadó](advisor-overview.md)
* [Induláshoz](advisor-get-started.md)
* [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
* [Tanácsadó magas rendelkezésre állásra vonatkozó ajánlásai](advisor-high-availability-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
* [Az Advisor Operational Excellence ajánlásai](advisor-operational-excellence-recommendations.md)
