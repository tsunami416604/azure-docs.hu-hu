---
title: A szolgáltatási költségek csökkentése Azure Advisor használatával
description: A Azure Advisor használatával optimalizálhatja az Azure-beli üzemelő példányok költségeit.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 99dfec669d8981a557b2e8a8d8979292af74616f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658561"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>A szolgáltatási költségek csökkentése Azure Advisor használatával

Az Advisor az inaktív és a kihasználatlan erőforrások azonosításával segít optimalizálni és csökkenteni az Azure-t teljes egészében.A Cost javaslatait az Advisor irányítópultjának **Cost (Cost** ) lapján érheti el.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizálja virtuálisgép-költségeit az alacsony kihasználtságú példányok átméretezésével vagy leállításával 

Bár bizonyos alkalmazási forgatókönyvek alacsony kihasználtságot eredményezhetnek a tervezéssel, gyakran pénzt takaríthat meg a virtuális gépek méretének és számának kezelésével. Az Advisor Advanced értékelési modelljei úgy tekintik meg a virtuális gépeket, hogy a processzor kihasználtságának maximális értéke legfeljebb 3% legyen, és a hálózati kihasználtság kevesebb, mint 2% a 7 napos időszakra P95th. A virtuális gépek megfelelő méretűek, ha az aktuális terhelést egy kisebb SKU-ban (ugyanabban az SKU-családban) vagy egy kisebb számú példányon helyezi el, hogy az aktuális terhelés ne haladja meg a 80%-os kihasználtságot, ha a nem felhasználó felé irányuló munkaterhelések, és nem haladják meg a 40%-ot a felhasználó felé irányuló munkaterhelés Itt a számítási feladatok típusát a számítási feladatok CPU-kihasználtsági jellemzőinek elemzésével határozzuk meg.

Az ajánlott műveletek leállnak, és a rendszer a számára ajánlott erőforrásra vonatkozik. Az Advisor a javasolt műveletek becsült költségmegtakarítását jeleníti meg – átméretezheti vagy leállíthatja. Emellett a javasolt művelet átméretezéséhez az Advisor az aktuális és a célként megadott SKU-információkat is biztosítja. 

Ha agresszíven szeretne lenni a kihasználatlan virtuális gépek azonosításához, a CPU-kihasználtsági szabályt előfizetések alapján is módosíthatja.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>A MariaDB, a MySQL és a PostgreSQL-kiszolgálók esetében a megfelelő méretezéssel optimalizálható a kiadások 
Az Advisor elemzi a használatot, és azt javasolja, hogy a MariaDB/MySQL/PostgreSQL adatbázis-kiszolgáló erőforrásainak kihasználása az elmúlt 7 napban hosszabb ideig megtörténjen. Az alacsony erőforrás-használat nem kívánt kiadásokat eredményez, amelyek elkerülhetők úgy, hogy az ne befolyásolja jelentősen a teljesítményt. A költségek csökkentése és az erőforrások hatékony kezelése érdekében javasoljuk, hogy csökkentse a felére a számítási méretet (a virtuális magok számát).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>A költségek csökkentése a nem kiépített ExpressRoute-áramkörök eltávolításával

Az Advisor azokat az ExpressRoute-áramköröket azonosítja, amelyekben a szolgáltató állapota nem több mint egy hónapra van *kiépítve* , és az áramkör törlését javasolja, ha nem tervezi az áramkör kiépítését a kapcsolati szolgáltatóval.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Költségek csökkentése az inaktív virtuális hálózati átjárók törlésével vagy újrakonfigurálásával

Az Advisor olyan virtuális hálózati átjárókat azonosít, amelyek több mint 90 napig inaktívak. Mivel ezeket az átjárókat óránként számlázzák, érdemes lehet újrakonfigurálni vagy törölni őket, ha már nem kívánja használni őket. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Vásároljon fenntartott virtuálisgép-példányokat a használatalapú fizetéshez képest elért pénzmegtakarítás érdekében

Az Advisor áttekinti a virtuális gépek használatát az elmúlt 30 napban, és megállapítja, hogy pénzt takaríthat meg egy Azure-foglalás megvásárlásával. Az Advisor megmutatja azokat a régiókat és méreteket, ahol a legtöbb megtakarítás megtakarítást eredményez, és a megvásárolt foglalások becsült megtakarítását fogja megjeleníteni. Az Azure-foglalások segítségével előre megvásárolhatja a virtuális gépek alapdíjait. A kedvezmények automatikusan érvényesek az új vagy meglévő virtuális gépekre, amelyek mérete és régiója megegyezik a foglalásokkal. [További információ a Azure Reserved VM Instancesról.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Az Advisor emellett értesíti Önt a fenntartott példányokról, amelyek a következő 30 napban lejárnak. Javasoljuk, hogy az utólagos elszámolású díjszabás kifizetésének elkerüléséhez új fenntartott példányokat vásároljon.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Fenntartott példányok vásárlása több erőforrástípus számára az utólagos elszámolású költségek megtakarítása érdekében

Elemezzük a használati mintát az alábbi erőforrások listáján, az elmúlt 30 napban, és a fenntartott kapacitás megvásárlását ajánljuk, amely maximalizálja a megtakarítást. 
### <a name="cosmos-db-reserved-capacity"></a>Fenntartott Cosmos DB-kapacitás
Az Ön elmúlt 30 napos Cosmos DB-használati mintájának elemzése alapján fenntartott kapacitások vásárlását ajánljuk Önnek a lehető legnagyobb megtakarítás érdekében. Fenntartott kapacitással előre vásárolhat óránkénti használati jogot a Cosmos DB-adatbázishoz, így a használatalapú fizetést elkerülve pénzt takaríthat meg. A fenntartott kapacitás egy számlázási kedvezmény, amely az új vagy meglévő üzemelő példányokra automatikusan vonatkozik. A megtakarításra vonatkozó becsléseinket 3 éves fenntartott kapacitási díjszabással, az elmúlt 30 napban megfigyelt használati minta kiterjesztésével számítjuk ki, és egyéni előfizetésekre vonatkoznak. A fenntartott kapacitás megvásárlásával elérhetők megosztott hatókörre vonatkozó javaslatok, amelyek további megtakarítást tesznek lehetővé.

### <a name="sql-paas-reserved-capacity"></a>SQL Péter lefoglalt kapacitása
Az SQL PaaS rugalmas készletek és a felügyelt példányok elmúlt 30 napban megfigyelt használati mintájának elemzése alapján fenntartott kapacitások vásárlását ajánljuk Önnek a lehető legnagyobb megtakarítás érdekében. Fenntartott kapacitással előre vásárolhat óránkénti használati jogot az SQL DB-adatbázishoz, így megtakaríthatja az SQL-számítási költségeket. Az SQL-licenc számlázása külön történik, így a fenntartott kapacitás vásárlásához nem jár kedvezményes licenc. A fenntartott kapacitás egy számlázási kedvezmény, amely az új vagy meglévő üzemelő példányokra automatikusan vonatkozik. A megtakarításra vonatkozó becsléseinket 3 éves fenntartott kapacitási díjszabással, az elmúlt 30 napban megfigyelt használati minta kiterjesztésével számítjuk ki, és egyéni előfizetésekre vonatkoznak. A fenntartott kapacitás megvásárlásával elérhetők megosztott hatókörre vonatkozó javaslatok, amelyek további megtakarítást tesznek lehetővé.

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service-bélyegző díja fenntartott kapacitása
Elemezzük a App Service izolált környezet pecsétjét az elmúlt 30 nap során, és javaslatot tesz a fenntartott kapacitás megvásárlására, amely maximalizálja a megtakarítást. Fenntartott kapacitással előre vásárolhat óránkénti használati jogot az izolált környezeti bélyegdíjhoz, így a használatalapú fizetést elkerülve pénzt takaríthat meg. Vegye figyelembe, hogy a fenntartott kapacitás csak a bélyegdíjra vonatkozik, az App Service-példányokra nem. A fenntartott kapacitás egy számlázási kedvezmény, amely az új vagy meglévő üzemelő példányokra automatikusan vonatkozik. A megtakarításra vonatkozó becsléseinket 3 éves fenntartott kapacitási díjszabással, az elmúlt 30 nap használati mintája alapján számítjuk ki, és egyéni előfizetésekre vonatkoznak.

### <a name="blob-storage-reserved-capacity"></a>BLOB Storage fenntartott kapacitása
Az Ön elmúlt 30 napos Azure Blob és Data Lake Storage-használata alapján kiszámoltuk, hogy fenntartott kapacitások vásárlásával érhetné el a lehető legnagyobb megtakarítást. Fenntartott kapacitással előre vásárolhat óránkénti használati jogot, így pénzt takaríthat meg a jelenlegi, igény szerinti használathoz képest. A fenntartott Blob Storage-kapacitás csak az Azure Blobban (GPv2) és az Azure Data Lake Storage-ban (2. generáció) tárolt adatokra vonatkozik. A fenntartott kapacitás egy számlázási kedvezmény, amely az új vagy meglévő üzemelő példányokra automatikusan vonatkozik. A megtakarításra vonatkozó becsléseinket 3 éves fenntartott kapacitási díjszabással, az elmúlt 30 napban megfigyelt használati minta alapján számítjuk ki, és egyéni előfizetésekre vonatkoznak. A fenntartott kapacitás megvásárlásával elérhetők megosztott hatókörre vonatkozó javaslatok, amelyek további megtakarítást tesznek lehetővé.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>A MariaDB, a MySQL és a PostgreSQL fenntartott kapacitása
Elemezzük a Azure Database for MariaDB, a MySQL és a PostgreSQL használati mintáját az elmúlt 30 napban, és javaslatot tesz a fenntartott kapacitás megvásárlására, amely maximalizálja a megtakarítást. A fenntartott kapacitással előre megvásárolhatja a MariaDB, a MySQL és a PostgreSQL óradíjas használatát, és megtakaríthatja a költségeket. A fenntartott kapacitás egy számlázási kedvezmény, amely az új vagy meglévő üzemelő példányokra automatikusan vonatkozik. A megtakarításra vonatkozó becsléseinket 3 éves fenntartott kapacitási díjszabással, az elmúlt 30 nap használati mintája alapján számítjuk ki, és egyéni előfizetésekre vonatkoznak. A fenntartott kapacitás megvásárlásával elérhetők megosztott hatókörre vonatkozó javaslatok, amelyek további megtakarítást tesznek lehetővé.

### <a name="synapse-analytics-formerly-sql-dw-reserved-capacity"></a>A szinapszis Analytics (korábban SQL DW) fenntartott kapacitása
Az elmúlt 30 napban elemezzük az Azure szinapszis Analytics használati mintáját, és javaslatot teszünk a fenntartott kapacitás megvásárlására, amely maximalizálja a megtakarítást. Fenntartott kapacitással előre vásárolhat óránkénti használati jogot a Synapse Analyticshez, hogy pénzt takarítson meg az igény szerinti használathoz képest. A fenntartott kapacitás egy számlázási kedvezmény, amely az új vagy meglévő üzemelő példányokra automatikusan vonatkozik. A megtakarításra vonatkozó becsléseinket 3 éves fenntartott kapacitási díjszabással, az elmúlt 30 napban megfigyelt használati minta alapján számítjuk ki, és egyéni előfizetésekre vonatkoznak. A fenntartott kapacitás megvásárlásával elérhetők megosztott hatókörre vonatkozó javaslatok, amelyek további megtakarítást tesznek lehetővé.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>A nem társított nyilvános IP-címek törlése a pénz megtakarítása érdekében

Az Advisor olyan nyilvános IP-címeket azonosít, amelyek jelenleg nem tartoznak Azure-erőforrásokhoz, például Terheléselosztóokhoz vagy virtuális gépekhez. Ezek a nyilvános IP-címek névleges díjat számítanak fel. Ha nem tervezi használni őket, a törlésük költségmegtakarítást eredményezhet.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Meghiúsult Azure Data Factory-folyamatok törlése

A Azure Advisor felismeri Azure Data Factory folyamatokat, amelyek többször is sikertelenek, és azt javasoljuk, hogy hárítsa el a problémákat, vagy törölje a sikertelen folyamatokat, ha már nincs rájuk szükség. Ezeknek a folyamatoknak a számlázása akkor is történik, ha nem szolgálnak Önnek, amíg nem működnek. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Szabványos Pillanatképek használata Managed Diskshoz
A 60%-os megtakarítás érdekében javasoljuk, hogy a pillanatképeket a Standard szintű Storage-ban tárolja, a szülőlemez tárolási típusától függetlenül. Ez a beállítás az alapértelmezett beállítás Managed Disks Pillanatképek esetében. Azure Advisor azonosítja a Premium Storage tárolt pillanatképeket, és javasolja, hogy a pillanatképet a premiumról a standard Storage-ra kell áttelepíteni. [További információ a felügyelt lemez díjszabásáról](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Életciklus-felügyelet használata
A Azure Advisor az Azure Blob Storage-objektumok számával, a teljes mérettel és a tranzakciókkal kapcsolatos intelligenciát fogja használni annak észleléséhez, hogy egy vagy több Storage-fiók a legjobb megoldás-e az életciklus-felügyelethez a rétegek adatai számára. Arra fogja kérni, hogy életciklus-kezelési szabályokat hozzon létre, hogy a tárolási költségek optimalizálása érdekében automatikusan leszűkítse az adatait az Azure Blob Storage szolgáltatásban az alkalmazások kompatibilitása érdekében.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Javasolt a rövid élettartamú operációsrendszer-lemezek létrehozása
Az [elmúló operációsrendszer-lemezzel](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)az ügyfelek megkapják a következő előnyöket: az operációsrendszer-lemez tárolási díjainak mentése. Az operációsrendszer-lemez írási/olvasási késésének csökkentése. Az operációs rendszer (és az ideiglenes lemez) eredeti állapotába történő visszaállításával gyorsabb a virtuális gépek rendszerképének alaphelyzetbe állítása. A rövid élettartamú IaaS-alapú virtuális gépekhez és az állapot nélküli számítási feladatokhoz tartozó virtuális gépekhez inkább érdemes elmúló operációsrendszer-lemezt használni. Az Advisor olyan erőforrásokra vonatkozó javaslatot tartalmaz, amelyek az elmúló operációsrendszer-lemezzel kapcsolatos előnyöket is igénybe vehetnek. 


## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>A Cost javaslatokhoz való hozzáférés Azure Advisor

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg és válassza ki az [**Advisor**](https://aka.ms/azureadvisordashboard) elemet bármelyik oldalon.

1. Az **Advisor** irányítópulton válassza a **Cost (Cost** ) lapot.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:
* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor – magas rendelkezésre állási javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)

