---
title: Klasszikus Azure-tárfiókok, tárolók vagy virtuális gépek törlésekor előforduló hibák elhárítása | Microsoft dokumentumok
description: A csatlakoztatott virtuális gépeket tartalmazó tárolóerőforrások törlésekor felmerülő problémák elhárítása.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915037"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Klasszikus tárolási erőforrás-törlési hibák elhárítása
Ez a cikk hibaelhárítási útmutatást nyújt, ha az alábbi hibák egyike történik, miközben megpróbálja törölni az Azure klasszikus tárfiókot, tárolót vagy *.vhd lapblob-fájlt. 


Ez a cikk csak a klasszikus tárolási erőforrásokkal kapcsolatos problémákat ismerteti. Ha egy felhasználó töröl egy klasszikus virtuális gépet az Azure Portalon, a PowerShell vagy a CLI használatával, majd a lemezek nem törlődnek automatikusan. A felhasználó lehetőséget kap a "Lemez" erőforrás törlésére. Abban az esetben, ha a beállítás nincs bejelölve, a "Lemez" erőforrás megakadályozza a tárfiók, a tároló és a tényleges *.vhd lapblob-fájl törlését.

Az Azure-lemezekről itt talál további [információt.](../../virtual-machines/windows/managed-disks-overview.md) Az Azure megakadályozza a virtuális géphez csatlakoztatott lemez törlését a sérülés megelőzése érdekében. Azt is megakadályozza, hogy a tárolók és a tárfiókok törlését, amelyek egy lap blob, amely egy virtuális géphez van csatolva. 

## <a name="what-is-a-disk"></a>Mi az a "Lemez"?
A "Lemez" erőforrás segítségével csatlakoztatja a *.vhd lap blob fájlt egy virtuális gép, mint egy operációs rendszer lemez vagy adat lemez. Az operációs rendszer lemeze vagy data lemezerőforrása a törlésig továbbra is fenntartja a *.vhd fájl bérletét. Az alábbi képen látható elérési út bármely tárolóerőforrása nem törölhető, ha egy "Lemez" erőforrás mutat rá.

![Képernyőkép a portálról, amelyen meg van nyitva a lemez (klasszikus) "Tulajdonság" ablaktábla](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Lépések a klasszikus virtuális gépek törlése közben 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Törölje a klasszikus virtuális gépet.
2. Ha a "Lemezek" jelölőnégyzet be van jelölve, a **lemezbérlet** (a fenti képen látható) társított lap blob *.vhd megszakad. A tényleges lapblob *.vhd fájl továbbra is létezik a tárfiókban.
![Képernyőkép a portálról, amelyen a "Delete" hibaablak meg van nyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. A lemez(ek) bérletének megbontása után maga a lapblob(ok) törölhetők. A tárfiók vagy tároló törölhető, ha az összes "Lemez" erőforrás jelen van bennük törlődik.

>[!NOTE] 
>Ha a felhasználó törli a virtuális gép, de nem a virtuális merevlemez, tárolási díjak továbbra is felhalmozódnak a lap blob *.vhd fájlt. A díjak összhangban lesznek a tárfiók típusával, további részletekért ellenőrizze az [árképzési oldalt.](https://azure.microsoft.com/pricing/details/storage/) Ha a felhasználó már nem kívánja használni a virtuális merevlemez(ek)et, törölje azt/őket a jövőbeli díjak elkerülése érdekében. 

## <a name="unable-to-delete-storage-account"></a>Nem lehet törölni a tárfiókot 

Amikor a felhasználó megpróbálja törölni a klasszikus tárfiókot, amely már nincs szükség, a felhasználó láthatja a következő viselkedést.

#### <a name="azure-portal"></a>Azure portál 
A felhasználó az [Azure Portalon](https://portal.azure.com) a klasszikus tárfiókra navigál, és a **Delete**gombra kattint, a felhasználó a következő üzenet jelenik meg: 

A virtuális géphez "csatlakoztatott" lemez(ek)

![Képernyőkép a portálról, amelyen a "Delete" hibaablak meg van nyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


A lemez(ek) "független" a virtuális gép

![Képernyőkép a portálról, amelyen a "Delete" (Törlés) nem hibaablak van megnyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
A felhasználó megpróbálja törölni a tárfiókot, amely már nem használatos, a klasszikus PowerShell-parancsmagok használatával. A felhasználó a következő üzenetet fogja látni:

> <span style="color:cyan">**Eltávolítás-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: A myclassicaccount tárfiók rendelkezik néhány aktív lemezkép(ek) és/vagy lemez(ek), például.  
> myclassicaccount. Győződjön meg arról, hogy ezek a lemezképek és/vagy lemez(ek) eltávolítása a tárfiók törlése előtt.</span>

## <a name="unable-to-delete-storage-container"></a>Nem lehet törölni a tárolót

Amikor a felhasználó megpróbálja törölni a klasszikus tárolási blob tároló, amely már nincs szükség, a felhasználó láthatja a következő viselkedést.

#### <a name="azure-portal"></a>Azure portál 
Az Azure Portal nem engedélyezné a felhasználónak egy tároló törlését, ha létezik egy "Lemez(ek)" címbérlet, amely egy *.vhd lapblob-fájlra mutat a tárolóban. Szándékosan megakadályozza a vhd(ek) fájl véletlen törlését lemez(ek) bérlettel. 

![Képernyőkép a portálról, megnyitva a tárolótároló "lista" ablaktáblájával](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Ha a felhasználó úgy dönt, hogy törli a PowerShell használatával, a következő hibát eredményezi. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: A távoli kiszolgáló hibát adott vissza: (412) Jelenleg van egy címbérlet a tárolón, és nincs megadva címbérlet-azonosító a kérelemben.. HTTP-állapotkód: 412 – HTTP-hibaüzenet: Jelenleg van egy címbérlet a tárolón, és a kérelemben nincs megadva címbérlet-azonosító.</span>

## <a name="unable-to-delete-a-vhd"></a>Nem lehet törölni egy vhd-t 

Az Azure virtuális gép törlése után a felhasználó megpróbálja törölni a vhd fájlt (lapblob), és megkapja az alábbi üzenetet:

#### <a name="azure-portal"></a>Azure portál 
A portálon két felület lehet a törlésre kiválasztott blobok listájától függően.

1. Ha csak a "Bérelt" blobok vannak kiválasztva, majd a Törlés gomb nem jelenik meg.
![Képernyőkép a portálról, megnyitva a "List" tárolóblob ablaktáblával](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Ha a "Bérelt" és az "Elérhető" blobok vegyesen jelennek meg, megjelenik a "Törlés" gomb. De a "Törlés" művelet hátrahagyja a lapblobokat, amelyeken lemezbérlet található. 
![Képernyőkép a portálról, a tárolóblob](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
!["lista" ablaktáblájával megnyitva A portál képernyőképe, a kijelölt "törlés" ablaktábla megnyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Ha a felhasználó úgy dönt, hogy törli a PowerShell használatával, a következő hibát eredményezi. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: A távoli kiszolgáló hibát adott vissza: (412) Jelenleg van egy bérlet a blobon, és nincs megadva címbérlet-azonosító a kérelemben.. HTTP-állapotkód: 412 – HTTP hibaüzenet: Jelenleg van egy bérlet a blobon, és a kérelemben nincs megadva címbérlet-azonosító.</span>


## <a name="resolution-steps"></a>A megoldás lépései

### <a name="to-remove-classic-disks"></a>Klasszikus lemezek eltávolítása
Kövesse az alábbi lépéseket az Azure Portalon:
1.  Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2.  Keresse meg a Lemezek(klasszikus). 
3.  Kattintson a Lemezek ![fülre. Képernyőkép a portálról, amikor meg van nyitva a tárolóblob "lista" ablaktábla](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Válassza ki az adatlemezt, majd kattintson a Lemez törlése gombra.
 ![Képernyőkép a portálról, megnyitva a "List" tárolóblob ablaktáblával](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Próbálkozzon újra a korábban sikertelen Törlés művelettel.
6.  A tárfiók vagy tároló nem törölhető, amíg egyetlen lemezzel rendelkezik.

### <a name="to-remove-classic-images"></a>Klasszikus képek eltávolítása   
Kövesse az alábbi lépéseket az Azure Portalon:
1.  Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2.  Keresse meg az operációs rendszer lemezképeit (klasszikus).
3.  Törölje a képet.
4.  Próbálkozzon újra a korábban sikertelen Törlés művelettel.
5.  Egy tárfiók vagy tároló nem törölhető, amíg egyetlen lemezképpel rendelkezik.
