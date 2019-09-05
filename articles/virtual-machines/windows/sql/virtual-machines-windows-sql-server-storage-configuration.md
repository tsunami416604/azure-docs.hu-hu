---
title: SQL Server virtuális gépek tárolási konfigurációja | Microsoft Docs
description: Ez a témakör azt ismerteti, hogy az Azure hogyan konfigurálja a tárolót SQL Server virtuális gépek számára a kiépítés során (Resource Manager-alapú üzemi modell). Azt is ismerteti, hogyan konfigurálhatja a tárhelyet a meglévő SQL Server virtuális gépekhez.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: 2705b42849922ce7e3650162b8f1ff78723685c2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309242"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server virtuális gépek tárolási konfigurációja

Ha SQL Server virtuálisgép-rendszerképet konfigurál az Azure-ban, a portál segít automatizálni a tárolási konfigurációját. Ide tartozik a tároló csatlakoztatása a virtuális géphez, így a tárterület elérhetővé válik a SQL Server számára, és beállítható úgy, hogy optimalizálja az adott teljesítményre vonatkozó követelményeket.

Ez a témakör azt ismerteti, hogyan konfigurálja az Azure a SQL Server virtuális gépek tárterületét a kiépítés és a meglévő virtuális gépek esetében. Ez a konfiguráció a SQL Server rendszert futtató Azure-beli virtuális gépek [teljesítményének ajánlott eljárásain](virtual-machines-windows-sql-performance.md) alapul.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek

Az automatikus tárolási konfigurációs beállítások használatához a virtuális gépnek a következő jellemzőkkel kell rendelkeznie:

* Kiépítve [SQL Server Gallery-képpel](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* A [Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md)-alapú üzemi modellt használja.
* [Prémium SSD](../disks-types.md)-ket használ.

## <a name="new-vms"></a>Új virtuális gépek

A következő szakaszok azt ismertetik, hogyan konfigurálható a tároló az új SQL Server virtuális gépekhez.

### <a name="azure-portal"></a>Azure Portal

Ha egy Azure-beli virtuális gépet kiépít egy SQL Server Gallery-rendszerkép használatával, beállíthatja, hogy automatikusan konfigurálja a tárolót az új virtuális géphez. Megadhatja a tárterület méretét, a teljesítmény korlátait és a munkaterhelés típusát. Az alábbi képernyőfelvételen az SQL-alapú virtuális gépek üzembe helyezése során használt tárolási konfiguráció panel látható.

![SQL Server VM tárolási konfiguráció a kiépítés során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

A lehetőségek alapján az Azure a következő tárolási konfigurációs feladatokat hajtja végre a virtuális gép létrehozása után:

* A prémium SSD-k létrehozása és csatolása a virtuális géphez.
* Az adatlemezek SQL Server számára elérhetővé való hozzáférhetőségének beállítása.
* A megadott méret és teljesítmény (IOPS és átviteli sebesség) követelményei alapján konfigurálja az adatlemezeket a tárolási készletekbe.
* Társítja a tárolót egy új meghajtóval a virtuális gépen.
* Optimalizálja ezt az új meghajtót a megadott munkaterhelés-típus (adattárház, tranzakciós feldolgozás vagy általános) alapján.

További információ arról, hogy az Azure hogyan konfigurálja a tárolási beállításokat: [tárolási konfiguráció szakasz](#storage-configuration). A Azure Portal SQL Server VM létrehozásával kapcsolatos teljes útmutatóért tekintse meg [az üzembe helyezési oktatóanyagot](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Erőforrás-sablonok kezelése

Ha a következő Resource Manager-sablonokat használja, a rendszer alapértelmezés szerint két prémium szintű adatlemezt csatol, és nem rendelkezik tároló-konfigurációval. Ezeket a sablonokat azonban testreszabhatja úgy, hogy megváltoztassa a virtuális géphez csatolt prémium szintű adatlemezek számát.

* [Automatikus biztonsági mentéssel rendelkező virtuális gép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Automatikus javítással rendelkező virtuális gép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Virtuális gép létrehozása AKV-integrációval](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A meglévő SQL Server virtuális gépek esetében a Azure Portal egyes tárolási beállításait is módosíthatja. Nyissa meg az [SQL Virtual Machines-erőforrást](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), és válassza az **Áttekintés**lehetőséget. A SQL Server áttekintő oldal a virtuális gép aktuális tárterület-használatát jeleníti meg. Ebben a diagramban a virtuális gépen található összes meghajtó megjelenik. Az egyes meghajtók esetében a tárolóhely négy szakaszban jelenik meg:

* SQL-adatok
* SQL-napló
* Egyéb (nem SQL-alapú tárolás)
* Elérhető

A tárolási beállítások módosításához kattintson a **Konfigurálás** elemre a **Beállítások**területen. 

![Meglévő SQL Server VM tárterületének konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

A megjelenő konfigurációs beállítások attól függően változnak, hogy korábban használta-e ezt a szolgáltatást. Ha első alkalommal használja az-t, megadhatja az új meghajtók tárolási követelményeit. Ha korábban ezt a szolgáltatást használta a meghajtó létrehozásához, kiterjesztheti a meghajtó tárterületét.

### <a name="use-for-the-first-time"></a>Első alkalommal történő használat

Ha első alkalommal használja ezt a funkciót, megadhatja a tárterület méretét és a teljesítményre vonatkozó korlátokat egy új meghajtóra vonatkozóan. Ez a felület hasonló ahhoz, amit a kiépítési időszakban látni fog. A fő különbség az, hogy nem lehet megadnia a munkaterhelés típusát. Ez a korlátozás megakadályozza a virtuális gép meglévő SQL Server konfigurációinak megszakadását.

Az Azure új meghajtót hoz létre a specifikációk alapján. Ebben az esetben az Azure a következő tárolási konfigurációs feladatokat végzi el:

* Létrehozza és csatolja a Premium Storage-adatlemezeket a virtuális géphez.
* Az adatlemezek SQL Server számára elérhetővé való hozzáférhetőségének beállítása.
* A megadott méret és teljesítmény (IOPS és átviteli sebesség) követelményei alapján konfigurálja az adatlemezeket a tárolási készletekbe.
* Társítja a tárolót egy új meghajtóval a virtuális gépen.

További információ arról, hogy az Azure hogyan konfigurálja a tárolási beállításokat: [tárolási konfiguráció szakasz](#storage-configuration).

### <a name="add-a-new-drive"></a>Új meghajtó hozzáadása

Ha már konfigurálta a tárolót a SQL Server VMon, a tárterület bővítése két új lehetőséget biztosít. Az első lehetőség egy új meghajtó hozzáadása, amely növelheti a virtuális gép teljesítményének szintjét.

A meghajtó hozzáadása után azonban néhány további manuális konfigurációt is végre kell hajtania a teljesítmény növelése érdekében.

### <a name="extend-the-drive"></a>A meghajtó kiterjesztése

A tárterület bővítésének másik lehetősége a meglévő meghajtó kiterjesztése. Ez a beállítás növeli a meghajtó rendelkezésre álló tárhelyét, de nem növeli a teljesítményt. A tárolási készletek esetében az oszlopok száma nem módosítható a készlet létrehozása után. Az oszlopok száma határozza meg a párhuzamos írások számát, amelyek az adatlemezek között sávok lehetnek. Ezért a hozzáadott adatlemezek nem növelhetik a teljesítményt. Csak a megírt adattárolók számára biztosítanak további tárhelyet. Ez a korlátozás azt is jelenti, hogy a meghajtó kibővítésekor az oszlopok száma határozza meg a felvehető adatlemezek minimális számát. Tehát ha négy adatlemezzel rendelkező tárolási készletet hoz létre, az oszlopok száma is négy. Minden alkalommal, amikor bővíti a tárterületet, legalább négy adatlemezt kell felvennie.

![Meghajtó kiterjesztése egy SQL virtuális géphez](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Tároló konfigurálása

Ez a szakasz a tárolási konfiguráció változásairól nyújt hivatkozást, amelyeket az Azure automatikusan végrehajt az SQL virtuális gépek üzembe helyezése vagy konfigurálása során a Azure Portal.

* Az Azure a virtuális gépről kiválasztott tárolóból konfigurálja a tárolási készletet. A témakör következő szakasza részletesen ismerteti a Storage-készlet konfigurációját.
* Az automatikus tárolási konfiguráció mindig [prémium SSD](../disks-types.md) -P30 adatlemezeket használ. Ennek következtében a kiválasztott számú terabájt és a virtuális géphez csatolt adatlemezek száma között van egy 1:1 leképezés.

A díjszabással kapcsolatos információkért tekintse meg a [Storage díjszabását](https://azure.microsoft.com/pricing/details/storage) ismertető oldalt a **Disk Storage** lapon.

### <a name="creation-of-the-storage-pool"></a>A Storage-készlet létrehozása

Az Azure a következő beállítások használatával hozza létre a tárolót SQL Server virtuális gépeken.

| Beállítás | Value |
| --- | --- |
| Sáv mérete |256 KB (adattárház); 64 KB (tranzakciós) |
| Lemezméretek |1 TB |
| Gyorsítótár |Olvasás |
| Foglalás mérete |64 KB NTFS-foglalási egység mérete |
| Azonnali fájl inicializálása |Enabled |
| Lapok zárolása a memóriában |Enabled |
| Helyreállítás |Egyszerű helyreállítás (nincs rugalmasság) |
| Oszlopok száma |Adatlemezek száma<sup>1</sup> |
| TempDB helye |Adatlemezeken tárolva<sup>2</sup> |

<sup>1</sup> a Storage-készlet létrehozása után nem módosítható a tárolóban lévő oszlopok száma.

<sup>2</sup> ez a beállítás csak a tárolási konfigurációs szolgáltatással létrehozott első meghajtóra vonatkozik.

## <a name="workload-optimization-settings"></a>Munkaterhelés-optimalizálási beállítások

Az alábbi táblázat az elérhető három munkaterhelés-típust és a hozzájuk tartozó optimalizálási lehetőségeket ismerteti:

| Számítási feladat típusa | Leírás | Optimalizálás |
| --- | --- | --- |
| **Általános** |Alapértelmezett beállítás, amely támogatja a legtöbb munkaterhelést |Nincsenek |
| **Tranzakciós feldolgozás** |Optimalizálja a tárolót a hagyományos adatbázis-OLTP számítási feladatokhoz |Nyomkövetési jelző 1117<br/>Nyomkövetési jelző 1118 |
| **Adatraktározás** |Optimalizálja a tárolót analitikai és jelentéskészítési számítási feladatokhoz |Nyomkövetési jelző 610<br/>Nyomkövetési jelző 1117 |

> [!NOTE]
> A számítási feladatok típusát csak akkor adhatja meg, ha kijelöli az SQL-alapú virtuális gépet a tárolási konfiguráció lépésben.

## <a name="next-steps"></a>További lépések

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
