---
title: Tárolási konfiguráció SQL Server rendszerű virtuális gépekhez |} A Microsoft Docs
description: Ez a témakör ismerteti, hogy az Azure úgy konfigurálja storage az SQL Server virtuális gépek üzembe helyezésekor (Resource Manager üzembe helyezési modell). Azt is bemutatja, hogyan konfigurálhat tárolási a meglévő SQL Server rendszerű virtuális gépekhez.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: da850b8ff9174fa310c5247cd7e99af69db28a8b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328435"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Tárolási konfiguráció SQL Server rendszerű virtuális gépekhez

SQL Server virtuálisgép-lemezkép az Azure-ban való konfigurálásakor a portál segítségével automatizálhatja a tárolási konfiguráció. Ide tartoznak a tároló csatlakoztatása a virtuális géphez, így az SQL Server számára, hogy a tárolási és konfigurálni kell az adott igények szerint optimalizálása érdekében.

Ez a témakör azt ismerteti, hogyan az Azure úgy konfigurálja storage az SQL Server rendszerű virtuális gépek kiépítése során, és a meglévő virtuális gépek. Ez a konfiguráció alapján a [ajánlott eljárások teljesítményének javításához](virtual-machines-windows-sql-performance.md) az SQL Servert futtató Azure virtuális gépek.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek

Az automatikus tároló konfigurációs beállításokat használja, a virtuális gép megköveteli a következő jellemzőkkel:

* Az üzembe helyezett egy [SQL Server image z galerie](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Használja a [Resource Manager üzemi modell](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Használja a [prémium szintű SSD-k](../disks-types.md).

## <a name="new-vms"></a>Új virtuális gépek

A következő szakaszok ismertetik az új SQL Server virtuális gépek esetén a tárolás konfigurálása.

### <a name="azure-portal"></a>Azure Portal

Egy SQL Server a katalóguslemezt használó Azure virtuális gép kiépítésekor kiválaszthatja a storage automatikusan konfigurálja az új virtuális gép. Megadhatja, hogy a tároló mérete, a teljesítményszintek és a számításifeladat-típust. Az alábbi képernyőfelvételen a tárolás konfigurálása panel során SQL virtuális gép kiépítése.

![SQL Server virtuális gép tárolási konfiguráció üzembe helyezés során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

A választási lehetőségek alapján, az Azure hajtja végre a következő tárolókonfigurálás feladatait a virtuális gép létrehozása után:

* Hoz létre, és csatolja a prémium szintű SSD-k a virtuális géphez.
* Az adatlemezek elérhetők lesznek a SQL Server konfigurálása.
* Az adatlemezek konfigurálja azokat a tárolókészletet a megadott méretet és teljesítményt (IOPS és átviteli sebesség) követelmények alapján.
* A tárolókészlet társít egy új meghajtót a virtuális gépen.
* Optimalizálja az új meghajtó a megadott számításifeladat-típust (adatraktározás, tranzakciófeldolgozás vagy általános) alapján.

Hogyan Azure storage-beállítások konfigurálása, a további részletekért tekintse meg a [Storage konfigurációs szakasz](#storage-configuration). SQL Server virtuális gép létrehozása az Azure Portalon egy teljes leírását lásd: [az üzembe helyezési oktatóanyag](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Erőforrások kezelése sablonokkal

Ha használja a következő Resource Manager-sablonok, két premium adatlemezek csatolt nincs tárolókészlet konfigurációját, alapértelmezés szerint. Ezek a sablonok a virtuális géphez csatolt adatlemezek prémium számának módosításához azonban testre szabható.

* [Virtuális gép létrehozása az automatikus biztonsági mentés](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Virtuális gép létrehozása az automatikus javítás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Virtuális gép létrehozása az AKV-integráció](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Meglévő virtuális gépek

Meglévő SQL Server virtuális gépekhez módosíthatja az egyes tárolási beállításai az Azure Portalon. Válassza ki a virtuális Gépet, és nyissa meg a beállítások területen válassza ki az SQL Server Configuration. Az SQL Server-konfigurációs panelen jelenik meg a jelenlegi storage használata a virtuális gép. Minden meghajtó, amely létezik a virtuális gép ezen a diagramon jelennek meg. Minden meghajtó a ténylegesen felhasznált tárterület jeleníti meg a négy részből áll:

* SQL data
* SQL log
* Egyéb (az SQL tároló)
* Elérhető

![Tárolás konfigurálása a meglévő SQL Server virtuális gép](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Adjon hozzá egy új meghajtót, vagy egy meglévő meghajtó bővítése a tároló konfigurálását, kattintson a Szerkesztés hivatkozásra a diagram felett.

Konfigurációs beállításokat látja, hogy változik, attól függően, hogy ez a funkció előtt használt. Amikor első alkalommal használja, az új meghajtó tárolási követelményeit is megadhat. Ha korábban már használta ezt a szolgáltatást meghajtó létrehozása, ha szeretné, a meghajtó tárolás kiterjesztése.

### <a name="use-for-the-first-time"></a>Az első alkalommal használja

Ha most először a funkció használatát, megadhatja a tároló méretének és teljesítményének vonatkozó korlátozásokat új meghajtó. Ez a tapasztalat hasonlít a kiépítés ideje lenne megtekintéséhez. A fő különbség, hogy nincs engedélye határozza meg a számítási feladat típusát. Ez a korlátozás meggátolja, hogy a virtuális gépen a létező SQL Server-konfigurációkat megszakítása.

![Az SQL Server Storage csúszkák konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Az Azure létrehozza a specifikációknak új meghajtó. Ebben a forgatókönyvben az Azure storage a következő konfigurációs feladatok hajtja végre:

* Hoz létre, és csatolja a prémium szintű tárolólemezeket adatokat a virtuális géphez.
* Az adatlemezek elérhetők lesznek a SQL Server konfigurálása.
* Az adatlemezek konfigurálja azokat a tárolókészletet a megadott méretet és teljesítményt (IOPS és átviteli sebesség) követelmények alapján.
* A tárolókészlet társít egy új meghajtót a virtuális gépen.

Hogyan Azure storage-beállítások konfigurálása, a további részletekért tekintse meg a [Storage konfigurációs szakasz](#storage-configuration).

### <a name="add-a-new-drive"></a>Egy új meghajtó felvétele

Ha már konfigurálta az SQL Server virtuális gép tárolása, két új beállítás bővülő tárolási kimenetei. Az első lehetőség, hogy adjon hozzá egy új meghajtót, ami növelheti a virtuális gép teljesítményi szintjét.

![Adjon hozzá egy új meghajtót SQL virtuális gép](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Azonban a meghajtó a felvett, a teljesítmény növelése érdekében extra manuális konfigurálást kell elvégeznie.

### <a name="extend-the-drive"></a>A meghajtó bővítése

Bővített tárolási a másik lehetőség, hogy a meglévő meghajtó bővítése. Ez a beállítás a meghajtó számára elérhető tárterület is nő, de nem növeli a teljesítményt. A tárolókészletekkel az oszlopok száma nem módosítható, a tárolókészlet létrehozása után. Az oszlopok számát is szétteríti a adatlemezek párhuzamos írási számát határozza meg. Ezért a hozzáadott adatlemezt nem javítható a teljesítmény. Az éppen írt adatok további tárolási megoldás biztosítása csak akkor is. Ez a korlátozás is jelenti, hogy, ha a meghajtó bővítése, az oszlopok számát határozza meg minimális számát, amelyeket hozzáadhat adatlemezeket. Tehát ha négy adatlemezt egy tárolókészletet hoz létre, az oszlopok számát is négy. Kiterjeszti a tárhely, amikor hozzá kell adnia legalább négy adatlemezt.

![SQL virtuális gép egy meghajtó bővítése](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Tároló konfigurálása

Ez a témakör egy rá mutató hivatkozást, amely az SQL virtuális gép üzembe helyezési vagy az Azure Portalon konfigurációja közben automatikusan végez az Azure storage a módosítások.

* Ha a virtuális gép számára kiválasztott kevesebb mint két TB tárterület, az Azure nem hoz létre a tárolókészletet.
* Ha a virtuális gép legalább két TB tárterület be van jelölve, az Azure egy tárolókészlet konfigurálását. Ez a témakör következő szakaszában részletesen a tárolókészlet konfigurációját.
* Automatikus tárolási konfiguráció mindig használja [prémium szintű SSD-k](../disks-types.md) P30 adatlemezeket. Ebből következően nincs 1:1 leképezés a kiválasztott számú terabájt és a virtuális Géphez csatolt adatlemezek száma között.

Díjszabási információkért tekintse meg a [Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage) lapot a **Disk Storage** fülre.

### <a name="creation-of-the-storage-pool"></a>A tárolókészlet létrehozása

Azure a következő beállításokat használja a tárolókészlet létrehozásához az SQL Server virtuális gépek.

| Beállítás | Érték |
| --- | --- |
| STRIPE-mérete |256 KB-os (adatraktározás); 64 KB-os (tranzakciós) |
| Lemezméretek |1 TB |
| Gyorsítótár |Olvasás |
| Foglalási mérete |64 KB-os NTFS foglalási egységek mérete |
| Azonnali fájl inicializálása |Engedélyezve |
| Memórialapok zárolása a memóriában |Engedélyezve |
| Helyreállítás |A helyreállítási egyszerű (rugalmasság nélküli) |
| Oszlopok száma |Adatlemezek száma<sup>1</sup> |
| A TempDB helye |Az adatlemezek tárolása<sup>2</sup> |

<sup>1</sup> a tárolókészlet létrehozása után a tárolókészletben található oszlopok száma nem változtathatja meg.

<sup>2</sup> ezt a beállítást csak az első meghajtó hoz létre a tárolási konfiguráció szolgáltatással vonatkozik.

## <a name="workload-optimization-settings"></a>Számítási feladatok energiaoptimalizálási beállítások

A következő táblázat ismerteti az elérhető három számítási típus beállítások és a megfelelő optimalizálási lehetőségek:

| Számítási feladat típusa | Leírás | Optimalizálási lehetőségek |
| --- | --- | --- |
| **Általános** |Alapértelmezett beállítás, amely támogatja a legtöbb számítási feladatokhoz |None |
| **Tranzakciófeldolgozás** |Optimalizálja a tárolót az adatbázisok hagyományos OLTP számítási feladatokhoz |Követési jelző 1117<br/>Követési jelző 1118 |
| **Adatraktározás** |Optimalizálja a tárolási, elemzési és jelentéskészítési számítási feladatokhoz |Trace Flag 610<br/>Követési jelző 1117 |

> [!NOTE]
> A számításifeladat-típust csak adja meg, válassza ki a tároló konfigurációs lépés SQL virtuális gép üzembe helyezésekor.

## <a name="next-steps"></a>További lépések

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos témaköröket talál [SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).
