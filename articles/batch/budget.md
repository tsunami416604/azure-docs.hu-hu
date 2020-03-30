---
title: Költségelemzés és költségkeret – Azure Batch
description: Megtudhatja, hogyan kaphat költségelemzést, és hogyan állíthat be költségvetést a Batch-számítási feladatok futtatásához használt számítási erőforrásokhoz és szoftverlicencekhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022715"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Költségelemzés és költségkeret az Azure Batch-hez

Az Azure Batch nem számít fel díjat, csak az alapul szolgáló számítási erőforrások és szoftverlicencek futtatásához használt Batch számítási feladatok. Magas szinten a költségek merülnek fel a virtuális gépek (Virtuális gépek) egy készletben, adatátvitel a virtuális gépről, vagy a felhőben tárolt bemeneti vagy kimeneti adatok. Vessünk egy pillantást a Batch néhány kulcsfontosságú összetevőjére, hogy megértsük, honnan származnak a költségek, hogyan állíthat be egy készletvagy fiók költségvetését, és milyen technikákat lehet a Batch-munkaterhelések költséghatékonyabbá tételére.

## <a name="batch-resources"></a>Kötegelt erőforrások

A virtuális gépek a kötegelt feldolgozáshoz használt legjelentősebb erőforrások. A virtuális gépek kötegelt használatának költségét a típus, a mennyiség és a használat időtartama alapján számítja ki a rendszer. A virtuális gép számlázási lehetőségei közé tartozik [a csak annyit, hogy az önrendelkezésre álló](https://azure.microsoft.com/offers/ms-azr-0003p/) fizetés vagy a [foglalás](../cost-management-billing/reservations/save-compute-costs-reservations.md) (előre fizetendő). Mindkét fizetési lehetőség a számítási munkaterheléstől függően különböző előnyökkel jár, és mindkét fizetési modell eltérő hatással lesz a számlára.

Amikor alkalmazások at telepítenek a Batch-csomópontok (VM-ek) [alkalmazáscsomagok](batch-application-packages.md)használatával, az Azure Storage-erőforrások, amelyek az alkalmazáscsomagok felhasználása. A rendszer díjat is számláz a bemeneti vagy kimeneti fájlok, például az erőforrásfájlok és egyéb naplóadatok tárolásáért. A Batch-hez társított tárolási adatok költsége általában sokkal alacsonyabb, mint a számítási erőforrások költsége. A **VirtualMachineConfiguration** szolgáltatással létrehozott készlet minden egyes virtuális gépe rendelkezik egy Azure által felügyelt lemezeket használó operációs rendszerrel. Az Azure által felügyelt lemezek további költségekkel járnak, és más lemezteljesítményszintek nek is eltérő költségei vannak.

A kötegkészletek hálózati erőforrásokat használnak. Különösen a **VirtualMachineConfiguration** készletek szabványos terheléselosztók, amelyek statikus IP-címeket igényelnek. A Batch által használt terheléselosztók láthatók a **Felhasználói előfizetési** fiókoknál, de a **Batch Service-fiókoknál** nem láthatók. A standard terheléselosztók díjat számítanak fel a batch készlet virtuális gépeinek átadott összes adatért; válassza ki azokat a batch API-kat, amelyek adatokat kérnek le a készletcsomópontokból (például A Feladat/csomópont fájl beolvasása), a feladatalkalmazás-csomagokból, az erőforrás-/kimeneti fájlokból és a tárolóképekből.

### <a name="additional-services"></a>További szolgáltatások

Virtuális gépeket és a tárolást nem magában foglaló szolgáltatások is figyelembe vehetik a Batch-fiók költségét.

A Batch szolgáltatással általánosan használt egyéb szolgáltatások a következők lehetnek:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Grafikus alkalmazásokkal rendelkező virtuális gépek

Attól függően, hogy milyen szolgáltatásokat használ a Batch-megoldással, további díjakmerülhetnek fel. Az egyes további szolgáltatások költségét a [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) ban találja.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Költségelemzés és költségkeret egy készlethez

Az Azure Portalon keresztül költségkereteket és költési riasztásokat hozhat létre a Batch-készlet(ek) vagy a Batch-fiók számára. A költségvetések és a riasztások hasznosak, ha az érdekelt feleket értesíti a túlköltekezés kockázatáról. Lehetséges, hogy a kiadási riasztások késnek, és kissé meghaladják a költségvetést. Ebben a példában egy adott kötegkészlet költségelemzését tekintjük meg.

1. Az Azure Portalon válassza **a Költségkezelés + Számlázás** lehetőséget a bal oldali navigációs sávon.
1. Válassza ki az előfizetést a **Saját előfizetések** szakaszból
1. Lépjen **a Költségelemzés** re a bal oldali navigációs sáv **Költségkezelés** szakaszában, amely az ehhez hasonló nézetet jeleníti meg:
1. Válassza **a Szűrő hozzáadása**lehetőséget. Az első legördülő menüben válassza az **Erőforrás** ![válassza az Erőforrás szűrő kiválasztása lehetőséget.](./media/batch-budget/resource-filter.png)
1. A második legördülő menüben válassza ki a Kötegkészletet. A készlet kiválasztásakor a költségelemzés a következő elemzéshez hasonlóan fog kinézni.
    ![Egy készlet költségelemzése](./media/batch-budget/pool-cost-analysis.png)

Az eredményül kapott költségelemzés a készlet költségét, valamint a költséghez hozzájáruló erőforrásokat mutatja. Ebben a példában a készletben használt virtuális gépek a legköltségesebb erőforrás.

Ha költségvetést szeretne létrehozni a készlethez, válassza a **Költségvetés: nincs**lehetőséget, majd válassza **az Új költségvetés létrehozása >** lehetőséget. Most az ablak segítségével konfigurálja a költségvetés kifejezetten a készlet.

A költségvetés konfigurálásáról az [Azure-költségvetések létrehozása és kezelése](../cost-management-billing/costs/tutorial-acm-create-budgets.md)című témakörben talál további információt.

> [!NOTE]
> Az Azure Batch az Azure Cloud Services és az Azure Virtual Machines technológiára épül. Ha a Cloud Services Configuration lehetőséget **választja,** a Felhőszolgáltatások díjszabási struktúrája alapján számítunk fel díjat. Ha a **Virtuálisgép-konfiguráció lehetőséget választja,** a virtuális gépek díjszabási struktúrája alapján kell fizetnie. A lap példái a **Virtuálisgép-konfigurációt (Virtuálisgép-konfiguráció)** használja.

## <a name="minimize-cost"></a>A költségek minimalizálása

Több virtuális gép és Az Azure-szolgáltatások használata hosszabb ideig költséges lehet. Szerencsére vannak olyan szolgáltatások, amelyek segítenek csökkenteni a kiadásokat, valamint stratégiák a munkaterhelés hatékonyságának maximalizálására.

### <a name="low-priority-virtual-machines"></a>Alacsony prioritású virtuális gépek

Az alacsony prioritású virtuális gépek csökkentik a Batch-számítási feladatok költségeit azáltal, hogy kihasználják az Azure-ban a többlet számítási kapacitást. Ha alacsony prioritású virtuális gépeket ad meg a készletekben, a Batch ezt a többletet használja a számítási feladatok futtatásához. Jelentős költségmegtakarítás érhető el az alacsony prioritású virtuális gépek használatával a dedikált virtuális gépek helyett.

További információ arról, hogyan állíthat be alacsony prioritású virtuális gépeket a számítási feladatokhoz [az Alacsony prioritású virtuális gépek használata a Batch-el.](batch-low-pri-vms.md)

### <a name="virtual-machine-os-disk-type"></a>Virtuálisgép operációsrendszer-lemeztípusa

Több [Virtuálisgép-operációs rendszer lemeztípusa van.](../virtual-machines/windows/disks-types.md) A legtöbb VM-sorozat mérete támogatja a prémium és a standard szintű tárolást. Ha egy "s" virtuális gép mérete van kiválasztva egy készlethez, a Batch prémium szintű SSD operációs rendszer lemezek konfigurálása. Ha a "nem s" virtuális gép mérete van kiválasztva, majd az olcsóbb, szabványos HDD-lemez típusát használja. Prémium szintű SSD operációs rendszerlemezeket `Standard_D2s_v3` használnak például, és szabványos `Standard_D2_v3`HDD operációs rendszerlemezeket használnak a számára.

A prémium szintű SSD-operációs rendszer lemezek drágábbak, de nagyobb teljesítményűek, és a prémium szintű lemezekkel rendelkező virtuális gépek valamivel gyorsabban indulnak el, mint a szabványos HDD operációs rendszerlemezeket tartalmazó virtuális gépek. A Batch, az operációs rendszer lemeze gyakran nem használják sokat, mint az alkalmazások és a feladat fájlok találhatók a virtuális gépek ideiglenes SSD-lemez. Ezért sok esetben nincs szükség a prémium szintű SSD megnövekedett költségének megfizetésére, amely az "s" virtuális gép méretének megadása esetén van kiépítve.

### <a name="reserved-virtual-machine-instances"></a>Fenntartott virtuálisgép-példányok

Ha hosszú ideig kívánja használni a Batch-et, megtakaríthatja a virtuális gépek költségét az [Azure-foglalások](../cost-management-billing/reservations/save-compute-costs-reservations.md) használatával a számítási feladatokhoz. A foglalási arány lényegesen alacsonyabb, mint a felosztó-kiosztó díj. A foglalás nélkül használt virtuálisgép-példányok díja használatalapú díjfizetéssel jár. Ha foglalást vásárol, a foglalási kedvezmény tanusíthatja, és a továbbiakban nem számítunk fel díjat a felosztó-kirovó árakon.

### <a name="automatic-scaling"></a>Automatikus skálázás

[Az automatikus méretezés](batch-automatic-scaling.md) dinamikusan méretezi a kötegkészletben lévő virtuális gépek számát az aktuális feladat igényei alapján. A készlet egy feladat élettartama alapján történő méretezésével az automatikus skálázás biztosítja, hogy a virtuális gépek csak akkor legyenek felskálázva, és csak akkor használhatók, ha van egy feladat, amelyet végre kell hajtani. Ha a feladat befejeződött, vagy nincsenek feladatok, a virtuális gépek automatikusan leskálázódik a számítási erőforrások mentése érdekében. A skálázás lehetővé teszi a Batch-megoldás teljes költségének csökkentését, ha csak a szükséges erőforrásokat használja.

Az automatikus skálázásról további információt az [Azure Batch-készlet számítási csomópontjainak automatikus méretezése](batch-automatic-scaling.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

- További információ a [Batch API-król és](batch-apis-tools.md) a Batch-megoldások létrehozásához és figyeléséhez rendelkezésre álló eszközökről.  

- Ismerje meg az [alacsony prioritású virtuális gépek et a Batch.](batch-low-pri-vms.md)
