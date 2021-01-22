---
title: Költségelemzés és költségvetések
description: Megtudhatja, hogyan hozhatja létre a költségek elemzését, hogyan állíthatja be a költségvetést, és csökkentheti a számítási erőforrások és a Batch-munkaterhelések futtatásához használt szoftverlicenc költségeit.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679313"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Cost Analysis és költségvetések Azure Batch

A Azure Batch használatára nem számítunk fel költségeket, bár a számítási erőforrások és a Batch-munkaterhelések futtatásához használt szoftverlicenc díja is felszámolható. A virtuális gépek (VM-EK) költségei felmerülhetnek a készletben, a virtuális gépről érkező adatátvitel vagy a felhőben tárolt bemeneti vagy kimeneti adatok miatt. Ez a témakör segít megismerni, hogy a költségek honnan származnak, hogyan állíthat be költségvetést egy batch-készlethez vagy-fiókhoz, és hogyan csökkentheti a Batch-munkaterhelések költségeit.

## <a name="batch-resources"></a>Batch-erőforrások

A virtuális gépek a legjelentősebb erőforrás a kötegelt feldolgozáshoz. A virtuális gépeknek a Batchhez való használatának költségeit a típus, a mennyiség és a használat időtartama alapján számítjuk ki. A virtuális gépek számlázási [lehetőségei közé tartozik az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású vagy a [foglalás](../cost-management-billing/reservations/save-compute-costs-reservations.md) (előre fizetve). A számítási feladattól függően mindkét fizetési lehetőség különböző előnyökkel jár, és a számla eltérő módon fog befolyásolni.

Ha az alkalmazások batch-csomópontokra (VM) vannak telepítve [alkalmazáscsomag](batch-application-packages.md)használatával, akkor az alkalmazás csomagjai által felhasznált Azure Storage-erőforrásokért kell fizetnie. A rendszer a bemeneti és kimeneti fájlok, például az erőforrások fájljainak és egyéb naplófájljainak tárolását is kiszámlázza. Általánosságban elmondható, hogy a Batch szolgáltatáshoz kapcsolódó tárolási adatok ára sokkal alacsonyabb, mint a számítási erőforrások díja. A [virtuális gépi konfigurációval](nodes-and-pools.md#virtual-machine-configuration) létrehozott készletekben található minden virtuális gépnek van egy társított operációsrendszer-lemeze, amely az Azure által felügyelt lemezeket használja. Az Azure által felügyelt lemezek további költségekkel járnak, és a többi lemez teljesítmény-szintjei is eltérő költségekkel rendelkeznek.

A Batch-készletek hálózati erőforrásokat használnak. A **VirtualMachineConfiguration** -készletek esetében a standard Load balancert használják, amelyek statikus IP-címeket igényelnek. A Batch által használt terheléselosztó a **felhasználói előfizetési** fiókok esetében látható, de a **Batch** -szolgáltatásfiókok esetében nem láthatók. A standard Load Balancer a Batch-készletben lévő virtuális gépekről átadott összes adatforrásra vonatkozó díjat számít fel. Válassza ki azokat a Batch API-kat, amelyek adatokat kérdeznek le a készlet csomópontjairól (például a feladat-és csomópont-fájlok lekérése), a feladathoz tartozó csomagok, az erőforrás/kimeneti fájlok és a tárolók képei

### <a name="additional-services"></a>További szolgáltatások

A virtuális gépeket és tárhelyet nem tartalmazó szolgáltatások a Batch-fiókjuk költségeit is figyelembe vehetik.

A Batch szolgáltatással gyakran használt egyéb szolgáltatások a következők lehetnek:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Grafikus alkalmazásokkal rendelkező virtuális gépek

Attól függően, hogy milyen szolgáltatásokat használ a Batch-megoldáshoz, további díjakat is igényelhet. Tekintse át a [díjszabási számológépet](https://azure.microsoft.com/pricing/calculator/) , és határozza meg az egyes szolgáltatások költségeit.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Készletre vonatkozó költségek elemzése és költségvetés

A Azure Portal költségvetéseket hozhat létre, és elköltheti a Batch-készletekhez vagy a Batch-fiókokhoz tartozó riasztásokat. A költségvetések és a riasztások akkor hasznosak, ha az érintett feleket a túllépések kockázatával kapcsolatban értesítik, bár lehetséges, hogy a riasztások elköltése késéssel jár, és a költségvetés némileg meghaladja a költségvetést.

Ebben a példában egy különálló batch-készlet költséghatékonyságát fogjuk megtekinteni.

1. A Azure Portal írja be a következőt: vagy válassza a **Cost Management + számlázás** lehetőséget.
1. Válassza ki az előfizetését a **Számlázási hatókörök** szakaszban.
1. A **Költségkezelés** területen válassza a **Költségelemzés** lehetőséget.
1. Válassza a **szűrő hozzáadása** lehetőséget. Az első legördülő menüben válassza az **erőforrás** elemet.
1. A második legördülő menüben válassza ki a Batch-készletet. Ha a készlet van kiválasztva, az itt látható példához hasonlóan megjelenik a készlethez tartozó Cost-elemzés.
    ![Képernyőfelvétel: a Azure Portal készletének Cost Analysis.](./media/batch-budget/pool-cost-analysis.png)

Az eredményül kapott Cost Analysis megjeleníti a készlet költségeit, valamint az ehhez a díjakhoz hozzájáruló erőforrásokat. Ebben a példában a készletben használt virtuális gépek a legdrágább erőforrások.

Ha költségvetést szeretne létrehozni a készlethez, válassza a **költségvetés: nincs** lehetőséget, majd válassza az **új költségvetési >létrehozása** lehetőséget. Most az ablak használatával [konfigurálhat egy költségvetést](../cost-management-billing/costs/tutorial-acm-create-budgets.md) kifejezetten a készlethez.

> [!NOTE]
> Azure Batch az Azure Cloud Servicesra és az Azure Virtual Machines technológiára épül. Ha **Cloud Services konfigurációt** választ, a Cloud Services díjszabási struktúra alapján kell fizetnie. A **virtuális gép konfigurációjának** kiválasztásakor a Virtual Machines díjszabási struktúra alapján kell fizetnie. Az oldalon található példa a virtuálisgép- **konfigurációt** használja, amelyet a legtöbb batch-készlet esetében ajánlott használni.

## <a name="minimize-cost"></a>Csökkentse a költségeket

Több virtuális gép és Azure-szolgáltatás használata hosszabb ideig költséges lehet. Érdemes lehet ezeket a stratégiákat használni a számítási feladatok hatékonyságának maximalizálása és a költségek csökkentése érdekében.

### <a name="low-priority-virtual-machines"></a>Alacsony prioritású virtuális gépek

Az [alacsony prioritású virtuális gépek](batch-low-pri-vms.md) csökkentik a Batch-munkaterhelések költségeit azáltal, hogy kihasználják a felesleges számítási kapacitást az Azure-ban. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, a Batch ezt a többletet használja a számítási feladatok futtatásához. A dedikált virtuális gépek helyett jelentős költségmegtakarítást érhet el, ha alacsony prioritású virtuális gépeket használ.

### <a name="virtual-machine-os-disk-type"></a>Virtuális gép operációsrendszer-lemezének típusa

Az Azure több [virtuális gép operációsrendszer-lemez típusát](../virtual-machines/disks-types.md)is biztosítja. A legtöbb virtuálisgép-sorozat a prémium és a standard szintű tárolást egyaránt támogató méretekkel rendelkezik. Ha egy készlethez a virtuális gép mérete van kiválasztva, a Batch a prémium szintű SSD operációsrendszer-lemezeket konfigurálja. Ha a "nem s" virtuálisgép-méret van kiválasztva, akkor a rendszer az olcsóbb, standard szintű HDD-lemez típusát használja. A prémium szintű SSD operációsrendszer-lemezeket például a `Standard_D2s_v3` és a standard HDD operációsrendszer-lemezek használják `Standard_D2_v3` .

Prémium SSD operációsrendszer-lemezek drágábbak, de nagyobb teljesítménnyel rendelkeznek. A prémium szintű lemezekkel rendelkező virtuális gépek valamivel gyorsabbak lehetnek, mint a standard HDD operációsrendszer-lemezekkel rendelkező virtuális gépek. A Batch esetében az operációsrendszer-lemez gyakran nem sokat használatos, mivel az alkalmazások és a feladatok fájljai a virtuális gép ideiglenes SSD-lemezén találhatók. Emiatt gyakran kiválaszthatja a "nem s" virtuálisgép-méretet, így nem kell megfizetnie a prémium SSD számára, amely a virtuális gép méretének megadásakor lett kiépítve.

### <a name="reserved-virtual-machine-instances"></a>Fenntartott virtuálisgép-példányok

Ha hosszú ideig kívánja használni a Batch-t, csökkentheti a virtuális gépek költségeit a számítási feladatok [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) használatával. A foglalások díja jelentősen alacsonyabb, mint az utólagos elszámolású díjszabás. A foglalások nélkül használt virtuálisgép-példányokat az utólagos elszámolású díjszabás szerint számoljuk el. Foglalás vásárlása esetén a foglalási kedvezmény érvényes.

### <a name="automatic-scaling"></a>Automatikus skálázás

Az [automatikus skálázás](batch-automatic-scaling.md) dinamikusan méretezi a Batch-készletben lévő virtuális gépek számát az aktuális feladatokhoz tartozó igények alapján. Ha a készletet a feladatok élettartama alapján méretezi, az automatikus skálázás biztosítja, hogy a virtuális gépek horizontális felskálázása és használata csak akkor történjen, amikor a feladatok elvégzésére van szükség. Ha a feladat befejeződött, vagy ha nincsenek feladatok, a virtuális gépek automatikusan le lesznek méretezve a számítási erőforrások mentéséhez. A skálázás lehetővé teszi, hogy csak a szükséges erőforrások használatával csökkentse a Batch-megoldás teljes költségeit.

## <a name="next-steps"></a>Következő lépések

- További információ a Batch-megoldások létrehozásához és figyeléséhez elérhető [Batch API-król és eszközökről](batch-apis-tools.md) .  
- Ismerje meg [, hogyan használhatja az alacsony prioritású virtuális gépeket a Batch használatával](batch-low-pri-vms.md).
