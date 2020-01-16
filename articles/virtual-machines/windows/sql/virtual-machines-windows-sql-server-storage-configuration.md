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
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 9d8fce0772f13c6e009b2441ecd85779a7622c5c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981742"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server virtuális gépek tárolási konfigurációja

Ha SQL Server virtuálisgép-rendszerképet konfigurál az Azure-ban, a portál segít automatizálni a tárolási konfigurációját. Ide tartozik a tároló csatlakoztatása a virtuális géphez, így a tárterület elérhetővé válik a SQL Server számára, és beállítható úgy, hogy optimalizálja az adott teljesítményre vonatkozó követelményeket.

Ez a témakör azt ismerteti, hogyan konfigurálja az Azure a SQL Server virtuális gépek tárterületét a kiépítés és a meglévő virtuális gépek esetében. Ez a konfiguráció a SQL Server rendszert futtató Azure-beli virtuális gépek [teljesítményének ajánlott eljárásain](virtual-machines-windows-sql-performance.md) alapul.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek

Az automatikus tárolási konfigurációs beállítások használatához a virtuális gépnek a következő jellemzőkkel kell rendelkeznie:

* Kiépítve [SQL Server Gallery-képpel](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* A [Resource Manager](../../../azure-resource-manager/management/deployment-models.md)-alapú üzemi modellt használja.
* [Prémium SSD](../disks-types.md)-ket használ.

## <a name="new-vms"></a>Új virtuális gépek

A következő szakaszok azt ismertetik, hogyan konfigurálható a tároló az új SQL Server virtuális gépekhez.

### <a name="azure-portal"></a>Azure portál

Ha egy Azure-beli virtuális gépet kiépít egy SQL Server Gallery-rendszerkép használatával, válassza a **konfiguráció módosítása** lehetőséget a **SQL Server beállítások** lapon a teljesítményre optimalizált tárolási konfiguráció lap megnyitásához. Meghagyhatja az alapértelmezett értékeket, vagy módosíthatja az igényeinek leginkább megfelelő lemez-konfigurációt a munkaterhelés alapján. 

![SQL Server VM tárolási konfiguráció a kiépítés során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Válassza ki, hogy milyen típusú számítási feladatot kíván üzembe helyezni a SQL Server a **tárolási optimalizálás**alatt. Az **általános** optimalizálási beállítással alapértelmezés szerint egy 5000-os maximális IOPS rendelkező adatlemez lesz, és ugyanazt a meghajtót fogja használni az adataihoz, a tranzakciónaplóhoz és a tempdb-tárolóhoz. A **tranzakciós feldolgozás** (OLTP) vagy **az adattárházak** kiválasztása külön lemezt hoz létre az adattároláshoz, egy külön lemezt a tranzakciónaplóhoz, és a helyi SSD-t használja a tempdb. A **tranzakciós feldolgozás** és **az adattárház**közötti különbség nem változik, de a [sáv konfigurációját és a nyomkövetési jelzőket](#workload-optimization-settings)is megváltoztatja. Ha a Premium Storage-t választja, a gyorsítótárazást *readonly* értékre állítja az adatmeghajtón, és a naplófájlok *egyikét sem* [SQL Server VM teljesítményre vonatkozó ajánlott eljárások](virtual-machines-windows-sql-performance.md)alapján. 

![SQL Server VM tárolási konfiguráció a kiépítés során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

A lemez konfigurációja teljesen testreszabható, így beállíthatja a SQL Server VM számítási feladathoz szükséges tárolási topológiát, lemez típusát és IOPs. Lehetősége van arra is, hogy a UltraSSD (előzetes verzió) lehetőséget adja a **lemez típusának** , ha a SQL Server VM az egyik támogatott régióban van (az USA 2. keleti régiója, Délkelet-Ázsia és Észak-Európa), és az [előfizetése számára](/azure/virtual-machines/windows/disks-enable-ultra-ssd)engedélyezte az ultra-lemezek használatát.  

Ezen kívül lehetősége van a lemezek gyorsítótárazásának beállítására is. Az Azure-beli virtuális gépek többrétegű gyorsítótárazási technológiával rendelkeznek, amelyet a [prémium szintű lemezek](/azure/virtual-machines/windows/disks-types#premium-ssd)használatakor használ a [blob cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) . A blob cache a virtuális gép RAM és a helyi SSD kombinációját használja a gyorsítótárazáshoz. 

A prémium SSD lemezes gyorsítótárazása *readonly*, *READWRITE* vagy *none*lehet. 

- A *readonly* gyorsítótárazás nagyon hasznos SQL Server Premium Storage tárolt adatfájlok esetében. Az *írásvédett* gyorsítótárazási szolgáltatás alacsony olvasási késést, nagy olvasási IOPS és adatátviteli sebességet biztosít, olvasásokat végez a gyorsítótárból, amely a virtuális gép memóriájában és a helyi SSD-n belüli operációs rendszer. Ezek az olvasások sokkal gyorsabbak, mint az adatlemezről beolvasott adatok, amelyek az Azure Blob Storage-ból származnak. A Premium Storage nem számítja ki a gyorsítótárból kiszolgált adatokat a lemez IOPS és az átviteli sebesség felé. Ezért az Ön megfelelője magasabb teljes IOPS-os adatátviteli sebességet érhet el. 
- *Nincs* szükség gyorsítótár-konfigurációra a SQL Server naplófájlt futtató lemezekhez, mivel a naplófájlt a rendszer szekvenciálisan írja le, és nem használja az *írásvédett* gyorsítótárazást. 
- A *READWRITE* gyorsítótárazás nem használható SQL Server fájlok üzemeltetéséhez, mivel SQL Server nem támogatja az adatkonzisztencia a *READWRITE* cache-sel. Az *írásvédett* blobos gyorsítótárban lévő adatmennyiséget írja, és a késleltetések kis mértékben megnő, ha az írások *readonly* blob cache-rétegeken mennek át. 


   > [!TIP]
   > Győződjön meg arról, hogy a tárolási konfiguráció megfelel a kiválasztott virtuálisgép-méret által támasztott korlátozásoknak. A virtuálisgép-méretet meghaladó tárolási paraméterek kiválasztásakor a következő hibaüzenetet fogja eredményezni: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Csökkentse a IOPs a lemez típusának módosításával, vagy növelje a teljesítmény korlátját a virtuális gép méretének növelésével. 


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

### <a name="quickstart-template"></a>Gyorsindítási sablon

A következő rövid útmutató sablon használatával telepítheti SQL Server VM a tárolási optimalizálással. 

* [Virtuális gép létrehozása tárolási optimalizálással](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Virtuális gép létrehozása a UltraSSD használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A meglévő SQL Server virtuális gépek esetében a Azure Portal egyes tárolási beállításait is módosíthatja. Nyissa meg az [SQL Virtual Machines-erőforrást](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), és válassza az **Áttekintés**lehetőséget. A SQL Server áttekintő oldal a virtuális gép aktuális tárterület-használatát jeleníti meg. Ebben a diagramban a virtuális gépen található összes meghajtó megjelenik. Az egyes meghajtók esetében a tárolóhely négy szakaszban jelenik meg:

* SQL-adatszolgáltatások
* SQL-napló
* Egyéb (nem SQL-alapú tárolás)
* Elérhető

A tárolási beállítások módosításához kattintson a **Konfigurálás** elemre a **Beállítások**területen. 

![Meglévő SQL Server VM tárterületének konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Módosíthatja a SQL Server VM létrehozási folyamat során konfigurált meghajtók lemezének beállításait. A **kibővített meghajtó** kiválasztásával megnyithatja a meghajtó módosítása lapot, amely lehetővé teszi a lemez típusának módosítását, valamint további lemezek hozzáadását. 

![Meglévő SQL Server VM tárterületének konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Tároló konfigurálása

Ez a szakasz a tárolási konfiguráció változásairól nyújt hivatkozást, amelyeket az Azure automatikusan végrehajt az SQL virtuális gépek üzembe helyezése vagy konfigurálása során a Azure Portal.

* Az Azure a virtuális gépről kiválasztott tárolóból konfigurálja a tárolási készletet. A témakör következő szakasza részletesen ismerteti a Storage-készlet konfigurációját.
* Az automatikus tárolási konfiguráció mindig [prémium SSD](../disks-types.md) -P30 adatlemezeket használ. Ennek következtében a kiválasztott számú terabájt és a virtuális géphez csatolt adatlemezek száma között van egy 1:1 leképezés.

A díjszabással kapcsolatos információkért tekintse meg a [Storage díjszabását](https://azure.microsoft.com/pricing/details/storage) ismertető oldalt a **Disk Storage** lapon.

### <a name="creation-of-the-storage-pool"></a>A Storage-készlet létrehozása

Az Azure a következő beállítások használatával hozza létre a tárolót SQL Server virtuális gépeken.

| Beállítás | Value (Díj) |
| --- | --- |
| Sáv mérete |256 KB (adattárház); 64 KB (tranzakciós) |
| Lemezméretek |1 TB |
| Gyorsítótár |Olvasás |
| Foglalás mérete |64 KB NTFS-foglalási egység mérete |
| Helyreállítás | Egyszerű helyreállítás (nincs rugalmasság) |
| Oszlopok száma |Adatlemezek száma legfeljebb 8<sup>1</sup> |


<sup>1</sup> a Storage-készlet létrehozása után nem módosítható a tárolóban lévő oszlopok száma.


## <a name="workload-optimization-settings"></a>Munkaterhelés-optimalizálási beállítások

Az alábbi táblázat az elérhető három munkaterhelés-típust és a hozzájuk tartozó optimalizálási lehetőségeket ismerteti:

| Számítási feladat típusa | Leírás | Optimalizálás |
| --- | --- | --- |
| **Általános** |Alapértelmezett beállítás, amely támogatja a legtöbb munkaterhelést |None |
| **Tranzakciós feldolgozás** |Optimalizálja a tárolót a hagyományos adatbázis-OLTP számítási feladatokhoz |Nyomkövetési jelző 1117<br/>Nyomkövetési jelző 1118 |
| **Adattárház** |Optimalizálja a tárolót analitikai és jelentéskészítési számítási feladatokhoz |Nyomkövetési jelző 610<br/>Nyomkövetési jelző 1117 |

> [!NOTE]
> A számítási feladatok típusát csak akkor adhatja meg, ha kijelöli az SQL-alapú virtuális gépet a tárolási konfiguráció lépésben.

## <a name="next-steps"></a>Következő lépések

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
