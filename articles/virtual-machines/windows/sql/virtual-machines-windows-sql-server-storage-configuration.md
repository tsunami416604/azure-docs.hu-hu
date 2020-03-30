---
title: SQL Server virtuális gépek tárolási konfigurációja | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy az Azure hogyan konfigurálja az SQL Server virtuális gépek tárolását a kiépítés során (Resource Manager telepítési modell). Azt is ismerteti, hogyan konfigurálhatja a meglévő SQL Server virtuális gépek tárolását.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243197"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Tárolási konfiguráció SQL Server rendszerű virtuális gépekhez

Amikor egy SQL Server virtuálisgép-lemezképet konfigurál az Azure-ban, a portál segít a tárolási konfiguráció automatizálásában. Ez magában foglalja a tároló csatlakoztatása a virtuális géphez, így a tároló elérhető az SQL Server, és konfigurálása, hogy optimalizálja az adott teljesítménykövetelmények.

Ez a témakör azt ismerteti, hogy az Azure hogyan konfigurálja a tárhelyet az SQL Server virtuális gépekhez mind a kiépítés során, mind a meglévő virtuális gépek hez. Ez a konfiguráció az SQL Servert futtató Azure virtuális gépek [teljesítményajánlott eljárásain](virtual-machines-windows-sql-performance.md) alapul.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek

Az automatikus tárolási konfigurációs beállítások használatához a virtuális gépnek a következő jellemzőkre van szüksége:

* [Sql Server-gyűjteményképpel](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)kiépítve.
* Az [Erőforrás-kezelő telepítési modelljét](../../../azure-resource-manager/management/deployment-models.md)használja.
* [Prémium SSD-ket](../disks-types.md)használ.

## <a name="new-vms"></a>Új virtuális gépek

A következő szakaszok bemutatják, hogyan konfigurálható az új SQL Server virtuális gépek tárolása.

### <a name="azure-portal"></a>Azure portál

Ha egy SQL Server-katalóguslemezkép használatával létesít egy Azure-gépvirtuális gépről, válassza a **Konfiguráció módosítása lehetőséget** az SQL Server **beállításai** lapon a Teljesítményoptimalizált tár konfigurációja lap megnyitásához. Az értékeket alapértelmezetten hagyhatja, vagy módosíthatja az igényeinek leginkább megfelelő lemezkonfiguráció típusát a munkaterhelés alapján. 

![SQL Server virtuálisgép-tároló konfigurációja a kiépítés során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Válassza ki, hogy milyen típusú számítási feladatokhoz telepíti az SQL Servert a **Storage-optimalizálás**csoportban. Az **Általános** optimalizálás iop- értékkel alapértelmezés szerint egy 5000 max IOPS-es adatlemezlesz, és ugyanezt a meghajtót fogja használni az adatokhoz, a tranzakciónaplóhoz és a TempDB-tárolóhoz. A **tranzakciós feldolgozás** (OLTP) vagy **az adattárház** kiválasztása korrelál egy külön lemezt az adatokhoz, egy külön lemezt a tranzakciónaplóhoz, és a TempDB helyi SSD-jét használja. Nincs tárolási különbség **a tranzakciós feldolgozás** és **az adattárolás**között, de megváltoztatja a [csíkkonfigurációt és a nyomkövetési jelzőket.](#workload-optimization-settings) A prémium szintű tárhely kiválasztása esetén a gyorsítótárazás *csak* az adatmeghajtóhoz, a naplómeghajtóhoz *pedig* nincs az [SQL Server virtuális gép teljesítményének ajánlott eljárása szerint.](virtual-machines-windows-sql-performance.md) 

![SQL Server virtuálisgép-tároló konfigurációja a kiépítés során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

A lemez konfigurációja teljesen testreszabható, így konfigurálhatja az SQL Server virtuális gép munkaterheléséhez szükséges tárolási topológiát, lemeztípust és IPP-ket. Az UltraSSD (előzetes verzió) használatát akkor is **használhatja** a Lemez típushoz, ha az SQL Server virtuális gép e támogatott régiók (USA keleti régiója 2, Délkelet-Ázsia és Észak-Európa) egyikében található, és engedélyezte az [ultralemezeket az előfizetéshez.](/azure/virtual-machines/windows/disks-enable-ultra-ssd)  

Ezenkívül beállíthatja a lemezek gyorsítótárazását is. Az Azure virtuális gépei egy többrétegű gyorsítótárazási technológiával rendelkeznek, amelyet [Blob Cache-nek](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) hívnak, ha [prémium szintű lemezekkel](/azure/virtual-machines/windows/disks-types#premium-ssd)használják. A Blob Cache a virtuális gép RAM és a helyi SSD kombinációját használja a gyorsítótárazáshoz. 

A prémium szintű SSD lemezgyorsítótárazás a következők lehet: *ReadOnly*, *ReadWrite* vagy *None*. 

- *A ReadOnly* gyorsítótárazás rendkívül hasznos a Prémium szintű storage-ban tárolt SQL Server adatfájlok esetében. *ReadOnly* gyorsítótárazás hozza alacsony olvasási késés, magas olvasási IOPS és átviteli, mint a gyorsítótárból, amely a virtuális gép memóriájában és a helyi SSD-n belül történik. Ezek az olvasások sokkal gyorsabbak, mint az adatoklemezről történő olvasás, amely az Azure blob storage-ból származik. A prémium szintű storage nem számítja a gyorsítótárból a lemez IOPS és átviteli igény felé kiszolgált olvasásokat. Ezért a vonatkozó képes elérni a magasabb teljes IOPS hangátviteli. 
- *Az* SQL Server naplófájlt tároló lemezekhez nincs gyorsítótár-konfiguráció t kell használni, mivel a naplófájl egymás után íródik, és nem élvezi a *ReadOnly* gyorsítótárazás előnyeit. 
- *Az ReadWrite* gyorsítótárazás nem használható SQL Server-fájlok üzemeltetésére, mivel az SQL Server nem támogatja az adatok konzisztenciáját a *ReadWrite* gyorsítótárral. Írja a *readonly* blob cache és a késések kis mértékben növekszik, ha írási műveletek et *readonly* blob cache rétegek. 


   > [!TIP]
   > Győződjön meg arról, hogy a tárolási konfiguráció megfelel a kijelölt virtuális gép mérete által előírt korlátozásoknak. A virtuális gép méretének teljesítménykorlátját meghaladó tárolási paraméterek `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`kiválasztása hibát eredményez: . Csökkentse az IPP-ket a lemeztípus módosításával, vagy növelje a teljesítménykorlát korlátozását a virtuális gép méretének növelésével. 


A lehetőségek alapján az Azure a következő tárolási konfigurációs feladatokat hajtja végre a virtuális gép létrehozása után:

* Prémium Szintű SSD-ket hoz létre és csatol a virtuális géphez.
* Úgy állítja be az adatlemezeket, hogy azok az SQL Server számára elérhetők legyenek.
* Az adatlemezeket a megadott méret és teljesítmény (IOPS és átviteli teljesítmény) követelmények alapján konfigurálja egy tárolókészletbe.
* A tárolókészletet egy új meghajtóhoz társítja a virtuális gépen.
* Optimalizálja ezt az új meghajtót a megadott számítási feladatok típusa (adattárház, tranzakciós feldolgozás vagy általános) alapján.

Arról, hogy az Azure hogyan konfigurálja a tárolási beállításokat, tekintse meg a [Storage konfigurációs szakaszát.](#storage-configuration) Az SQL Server virtuális gép azure-portálon való létrehozásáról a [kiépítési oktatóanyag](virtual-machines-windows-portal-sql-server-provision.md)című témakörben található.

### <a name="resource-manage-templates"></a>Erőforrás-kezelési sablonok

Ha a következő Resource Manager-sablonokat használja, alapértelmezés szerint két prémium szintű adatlemez csatlakozik, tárolókészlet-konfiguráció nélkül. Ezeket a sablonokat azonban testre szabhatja a virtuális géphez csatlakoztatott prémium szintű adatlemezek számának módosításához.

* [Virtuális gép létrehozása automatikus biztonsági mentéssel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Virtuális gép létrehozása automatikus javítással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Virtuális gép létrehozása AKV-integrációval](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Gyorsindítási sablon

A következő gyorsindítási sablon segítségével telepítheti az SQL Server virtuális gép tároló optimalizálása. 

* [Virtuális gép létrehozása tárolási optimalizálással](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Virtuális gép létrehozása az UltraSSD használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépek esetén módosíthatja az Azure Portalon bizonyos tárolási beállításokat. Nyissa meg az [SQL virtuális gépek erőforrását,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)és válassza **az Áttekintés**lehetőséget. Az SQL Server áttekintése lap a virtuális gép aktuális tárolási használatát mutatja. A virtuális gépen található összes meghajtó ezen a diagramon jelenik meg. A tárhely minden meghajtóesetében négy szakaszban jelenik meg:

* SQL-adatok
* SQL-napló
* Egyéb (nem SQL-tároló)
* Elérhető

A tárolási beállítások módosításához válassza a **Beállítások** **csoportBan** a Beállítás lehetőséget. 

![A meglévő SQL Server virtuális gép tárolásának konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Módosíthatja az SQL Server virtuálisgép-létrehozási folyamat során konfigurált meghajtók lemezbeállításait. A **Meghajtó kiterjesztése** lehetőséget választva megnyílik a meghajtómódosítási lap, amely lehetővé teszi a lemeztípus módosítását, valamint további lemezek hozzáadását. 

![A meglévő SQL Server virtuális gép tárolásának konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Tároló konfigurálása

Ez a szakasz egy hivatkozást a tárolási konfigurációs módosításokat, amelyek az Azure automatikusan végrehajtja az SQL virtuális gép kiépítése vagy konfigurálása az Azure Portalon.

* Az Azure konfigurálja a virtuális gépből kiválasztott tárolókészletet. A témakör következő szakasza a tárolókészlet konfigurációjának részleteit ismerteti.
* Az automatikus tárolási konfiguráció mindig [prémium szintű SSD](../disks-types.md) P30 adatlemezeket használ. Ennek következtében a kiválasztott terabájtok száma és a virtuális géphez csatolt adatlemezek száma között 1:1 leképezés van.

Az árképzésről a **Lemeztároló** lap [Tárolási díjszabás](https://azure.microsoft.com/pricing/details/storage) lapján talál.

### <a name="creation-of-the-storage-pool"></a>A tárolókészlet létrehozása

Az Azure a következő beállításokat használja a tárolókészlet létrehozásához az SQL Server virtuális gépeken.

| Beállítás | Érték |
| --- | --- |
| Csíkos méret |256 KB (adattárház); 64 KB (tranzakciós) |
| Lemezméretek |1 TB fejenként |
| Gyorsítótár |Olvasás |
| Felosztás mérete |64 KB NTFS-foglalási egység mérete |
| Helyreállítás | Egyszerű helyreállítás (nincs rugalmasság) |
| Oszlopok száma |Adatlemezek száma 8<sup>1-ig</sup> |


<sup>1</sup> A tárolókészlet létrehozása után nem módosíthatja a tárolókészlet oszlopainak számát.


## <a name="workload-optimization-settings"></a>Számítási feladatok optimalizálási beállításai

Az alábbi táblázat a rendelkezésre álló három számítási feladattípus-beállítását és a hozzájuk tartozó optimalizálásokat ismerteti:

| Munkaterhelés típusa | Leírás | Optimalizálás |
| --- | --- | --- |
| **Általános** |A legtöbb munkaterhelést támogató alapértelmezett beállítás |None |
| **Tranzakciós feldolgozás** |Optimalizálja a tárolót a hagyományos adatbázis OLTP számítási feladatokhoz |Nyomkövetési jelző 1117<br/>Nyomkövetési jelző 1118 |
| **Adattárház** |Optimalizálja a tárolót az analitikus és jelentési számítási feladatokhoz |Nyomkövetési jelző 610<br/>Nyomkövetési jelző 1117 |

> [!NOTE]
> Csak akkor adhatja meg a számítási feladatok típusát, ha egy SQL virtuális gép kiépítése a tárolási konfigurációs lépésben kiválasztja azt.

## <a name="next-steps"></a>További lépések

Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témaköröket [az SQL Server azure-beli virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-server-iaas-overview.md)
