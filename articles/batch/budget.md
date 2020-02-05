---
title: Cost Analysis and Budget – Azure Batch
description: Ismerje meg, hogyan hozhatja létre a költségek elemzését, és hogyan állíthat be költségvetést a Batch számítási feladatainak futtatásához használt számítási erőforrások és szoftverlicenc számára.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022715"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Cost Analysis és költségvetések Azure Batch

Magáért a Azure Batchért nem számítunk fel díjat, csak a számítási erőforrások és a Batch-munkaterhelések futtatására szolgáló szoftverlicenc. Magas szinten a virtuális gépek (VM-EK) egy készleten, a virtuális gépről érkező adatátvitel, illetve a felhőben tárolt bemeneti vagy kimeneti adatok alapján merülnek fel. Vessünk egy pillantást a Batch egyes kulcsfontosságú összetevőire, hogy megértsük, a költségek honnan származnak, hogyan lehet költségvetést beállítani egy készlethez vagy fiókhoz, valamint néhány olyan technikát, amellyel a Batch számítási feladatok költséghatékonysága költséghatékony.

## <a name="batch-resources"></a>Batch-erőforrások

A virtuális gépek a legjelentősebb erőforrás a kötegelt feldolgozáshoz. A virtuális gépeknek a Batchhez való használatának költségeit a típus, a mennyiség és a használat időtartama alapján számítjuk ki. A virtuális gépek számlázási [lehetőségei közé tartozik az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású vagy a [foglalás](../cost-management-billing/reservations/save-compute-costs-reservations.md) (előre fizetve). A számítási feladattól függően mindkét fizetési lehetőség különböző előnyökkel jár, és mindkét fizetési modell eltérő hatással lesz a számlára.

Ha az alkalmazások batch-csomópontokra (VM) vannak telepítve [alkalmazáscsomag](batch-application-packages.md)használatával, akkor az alkalmazás csomagjai által felhasznált Azure Storage-erőforrásokért kell fizetnie. A rendszer a bemeneti és kimeneti fájlok, például az erőforrások fájljainak és egyéb naplófájljainak tárolását is kiszámlázza. Általánosságban elmondható, hogy a Batch szolgáltatáshoz kapcsolódó tárolási adatok ára sokkal alacsonyabb, mint a számítási erőforrások díja. A **VirtualMachineConfiguration** -mel létrehozott készletekben található minden virtuális gépnek van egy társított operációsrendszer-lemeze, amely az Azure által felügyelt lemezeket használja. Az Azure által felügyelt lemezek további költségekkel járnak, és a többi lemez teljesítmény-szintjei is eltérő költségekkel rendelkeznek.

A Batch-készletek hálózati erőforrásokat használnak. Különösen a **VirtualMachineConfiguration** -készletek standard Load Balancer használata szükséges, amelyek statikus IP-címeket igényelnek. A Batch által használt terheléselosztó a **felhasználói előfizetési** fiókok esetében látható, de a **Batch** -szolgáltatásfiókok esetében nem láthatók. A standard Load Balancer a Batch-készletben lévő virtuális gépekről átadott összes adatforrásra vonatkozó díjat számít fel. Válassza ki azokat a Batch API-kat, amelyek adatokat kérdeznek le a készlet csomópontjairól (például a feladat-és csomópont-fájlok lekérése), a feladathoz tartozó csomagok, az erőforrás/kimeneti fájlok és a tárolók képei

### <a name="additional-services"></a>Kiegészítő szolgáltatások

A virtuális gépeket és tárhelyet nem tartalmazó szolgáltatások a Batch-fiókjuk költségeit is figyelembe vehetik.

A Batch szolgáltatással gyakran használt egyéb szolgáltatások a következők lehetnek:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network (Virtuális hálózat)
- Grafikus alkalmazásokkal rendelkező virtuális gépek

Attól függően, hogy milyen szolgáltatásokat használ a Batch-megoldáshoz, további díjakat is igényelhet. Tekintse át a [díjszabási számológépet](https://azure.microsoft.com/pricing/calculator/) , és határozza meg az egyes szolgáltatások költségeit.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Készletre vonatkozó költségek elemzése és költségvetés

A Azure Portal segítségével költségvetéseket hozhat létre, és elköltheti a Batch-készlet (ek) vagy a Batch-fiókra vonatkozó riasztásokat. A költségvetések és a riasztások akkor hasznosak, ha az érintett feleket a túlhasználattal kapcsolatos kockázatokról értesítik. Előfordulhat, hogy a riasztások elköltése és a költségvetés valamivel nagyobb késése van. Ebben a példában egy különálló batch-készlet költséghatékonyságát fogjuk megtekinteni.

1. A Azure Portal válassza a **Cost Management + számlázás** lehetőséget a bal oldali navigációs sávon.
1. Előfizetés kiválasztása a **saját előfizetések** szakaszból
1. A bal oldali navigációs sáv **Cost Management** szakaszában kattintson a **Cost Analysis** elemre, amely a következőképpen jelenik meg:
1. Válassza a **szűrő hozzáadása**lehetőséget. Az első legördülő menüben válassza az **erőforrás** lehetőséget ![válassza ki az erőforrás-szűrőt](./media/batch-budget/resource-filter.png)
1. A második legördülő menüben válassza ki a Batch-készletet. A készlet kiválasztása után a Cost Analysis a következő elemzéshez hasonlóan fog kinézni.
    a készlet ![ának elemzése](./media/batch-budget/pool-cost-analysis.png)

Az eredményül kapott Cost Analysis megjeleníti a készlet költségeit, valamint az ehhez a díjakhoz hozzájáruló erőforrásokat. Ebben a példában a készletben használt virtuális gépek a legdrágább erőforrások.

Ha költségvetést szeretne létrehozni a készlethez, válassza a **költségvetés: nincs**lehetőséget, majd válassza az **új költségvetési > létrehozása**lehetőséget. Most az ablak használatával konfigurálhat egy költségvetést kifejezetten a készlethez.

A költségvetés konfigurálásával kapcsolatos további információkért lásd: [Azure-költségvetések létrehozása és kezelése](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch az Azure Cloud Servicesra és az Azure Virtual Machines technológiára épül. Ha **Cloud Services konfigurációt**választ, a Cloud Services díjszabási struktúra alapján kell fizetnie. A **virtuális gép konfigurációjának**kiválasztásakor a Virtual Machines díjszabási struktúra alapján kell fizetnie. Az oldalon található példa a **virtuális gép konfigurációját**használja.

## <a name="minimize-cost"></a>Csökkentse a költségeket

Több virtuális gép és Azure-szolgáltatás használata hosszabb ideig költséges lehet. Szerencsére vannak olyan szolgáltatások, amelyek segítenek csökkenteni a kiadásokat, valamint stratégiákat a számítási feladatok hatékonyságának maximalizálása érdekében.

### <a name="low-priority-virtual-machines"></a>Alacsony prioritású virtuális gépek

Az alacsony prioritású virtuális gépek csökkentik a Batch-munkaterhelések költségeit azáltal, hogy kihasználják a felesleges számítási kapacitást az Azure-ban. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, a Batch ezt a többletet használja a számítási feladatok futtatásához. Az alacsony prioritású virtuális gépek használata jelentős költségmegtakarítást biztosít a dedikált virtuális gépek helyett.

Ismerje meg, hogyan állíthat be alacsony prioritású virtuális gépeket a számítási feladathoz, ha [alacsony prioritású virtuális gépeket használ a Batch használatával](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Virtuális gép operációsrendszer-lemezének típusa

Több [virtuális gép operációsrendszer-lemeze](../virtual-machines/windows/disks-types.md)van. A legtöbb virtuálisgép-sorozat a prémium és a standard szintű tárolást egyaránt támogató méretekkel rendelkezik. Ha egy készlethez a virtuális gép mérete van kiválasztva, a Batch a prémium szintű SSD operációsrendszer-lemezeket konfigurálja. Ha a "nem s" virtuálisgép-méret van kiválasztva, akkor a rendszer az olcsóbb, standard szintű HDD-lemez típusát használja. A prémium szintű SSD operációsrendszer-lemezek például a `Standard_D2s_v3` és a standard HDD operációsrendszer-lemezekhez használatosak `Standard_D2_v3`hoz.

Prémium SSD operációsrendszer-lemezek drágábbak, de a nagyobb teljesítmény és a prémium szintű lemezekkel rendelkező virtuális gépek valamivel gyorsabban elkezdődhetnek, mint a standard HDD operációsrendszer-lemezekkel rendelkező virtuális gépek. A Batch használata esetén az operációsrendszer-lemez gyakran nincs használatban, mivel az alkalmazások és a feladatok fájljai a virtuális gépek ideiglenes SSD-lemezén találhatók. Ezért számos esetben nem kell fizetnie a prémium SSD számára, amelyet a virtuális gép méretének megadásakor kell kiépíteni.

### <a name="reserved-virtual-machine-instances"></a>Fenntartott virtuálisgép-példányok

Ha hosszú ideig kívánja használni a köteget, a munkaterhelések [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) használatával mentheti a virtuális gépek költségeit. A foglalások díja jelentősen alacsonyabb, mint az utólagos elszámolású díjszabás. A foglalás nélkül használt virtuálisgép-példányokat utólagos elszámolású díjszabással számoljuk el. Foglalás megvásárlása esetén a foglalási kedvezményt a rendszer az utólagos elszámolású díjszabás szerint számítja fel.

### <a name="automatic-scaling"></a>Automatikus skálázás

Az [automatikus skálázás](batch-automatic-scaling.md) dinamikusan méretezi a Batch-készletben lévő virtuális gépek számát az aktuális feladatokhoz tartozó igények alapján. Ha a készletet a feladatok élettartama alapján méretezi, az automatikus skálázás biztosítja, hogy a virtuális gépek méretezése és használata csak akkor történjen, amikor feladatot hajt végre. Ha a feladat befejeződött, vagy nincsenek feladatok, a virtuális gépek automatikusan le lesznek méretezve a számítási erőforrások mentéséhez. A skálázás lehetővé teszi, hogy csak a szükséges erőforrások használatával csökkentse a Batch-megoldás teljes költségeit.

További információ az automatikus skálázásról: [számítási csomópontok automatikus méretezése egy Azure batch készletben](batch-automatic-scaling.md).

## <a name="next-steps"></a>Következő lépések

- További információ a Batch-megoldások létrehozásához és figyeléséhez elérhető [Batch API-król és eszközökről](batch-apis-tools.md) .  

- Ismerkedjen meg az [alacsony prioritású virtuális gépekkel a Batch segítségével](batch-low-pri-vms.md).
