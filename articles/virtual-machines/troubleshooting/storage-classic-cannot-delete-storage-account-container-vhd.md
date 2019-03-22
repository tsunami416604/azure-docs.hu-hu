---
title: Hibaelhárítás az Azure klasszikus tárfiókok, tárolók vagy virtuális merevlemezek törlésekor |} A Microsoft Docs
description: Problémák elhárításáról tárolási erőforrásokat tartalmazó csatlakoztatott virtuális merevlemezek törlésekor.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078159"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Hagyományos tároló erőforrás törlésével kapcsolatos hibák elhárítása
Ez a cikk nyújt hibaelhárítási útmutatót, ha hibák a következők egyike történik az Azure klasszikus tárfiókot, tárolót vagy *.vhd lapozófájl blob törlésének megkísérlésekor. 


Ez a cikk csak a hagyományos tárolási erőforrásokhoz problémákkal foglalkozik. Ha a felhasználó töröl egy klasszikus virtuális gép az Azure portal használatával, a PowerShell vagy parancssori felület és a lemezek a rendszer nem automatikusan törli. A felhasználó élvezheti a "Lemez" erőforrás törlésének lehetőségét. Abban az esetben, ha a beállítás nincs bejelölve, a "Lemez" erőforrás megakadályozza, hogy a storage-fiók, tároló és a tényleges *.vhd lapozófájl blob törlését.

További információ az Azure disks található [Itt](../../virtual-machines/windows/managed-disks-overview.md). Azure megakadályozza, hogy az egy lemezt, amely csatolva van egy virtuális gép meghibásodásának elkerülése érdekében törlését. Megakadályozza a tárolók és a storage-fiókok, amelyek rendelkeznek egy lapblob egy virtuális géphez csatolt törlését is. 

## <a name="what-is-a-disk"></a>Mit jelent a "Lemez"?
A "Lemez" erőforrás-operációsrendszer-lemez és az adatlemez egy *.vhd page blob fájlt egy virtuális géphez csatlakoztatásához használatos. Az operációsrendszer-lemez vagy adatok lemezerőforrás, amíg nem törlik, továbbra is, amely tárolja a címbérlet *.vhd fájlon. Az alábbi képen látható elérési út minden tárolási erőforrás nem törölhető, ha a "Lemez" erőforrás mutat.

![Képernyőkép a portal, a lemez (klasszikus) "Property" ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>A lépések egy klasszikus virtuális gép törlése közben 
1. A klasszikus virtuális gép törlése.
2. Ha a "Lemezek" jelölőnégyzet be van jelölve, a **lemez bérleti** (a fenti ábrán látható) kapcsolódó együtt a lapblob *.vhd megszakad. Az aktuális lap blobfájlra *.vhd még létezni fognak a storage-fiókban.
![Képernyőkép a portal, a virtuális gép (klasszikus) "Delete" Hiba ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. A lemez(ek) bérlet megszakad, a lap BLOB(ok) maga törölhetők. A storage-fiók vagy a tároló összes bennük található "Lemez" erőforrás törlése után lehet törölni.

>[!NOTE] 
>Ha a felhasználó törli a virtuális Gépet, de nem a virtuális merevlemez, tárolási díjakat továbbra is elkezdjük számolni a blob *.vhd lapozófájl. A díjak lesznek megfelelően ellenőrizze a tárfiók típusát a [díjszabását ismertető lapon](https://azure.microsoft.com/en-us/pricing/details/storage/) további részletekért. Ha a felhasználó már nem kíván használni a virtuális, törölje a informatikai/azokat későbbi díjak elkerülése érdekében. 

## <a name="unable-to-delete-storage-account"></a>Nem sikerült törölni a storage-fiók 

Amikor a felhasználó megkísérel törölni egy klasszikus tárfiókot, amelyet már nincs rá szükség, a felhasználó a következő viselkedés jelenhet meg.

#### <a name="azure-portal"></a>Azure Portal 
A klasszikus tárfiók felhasználó nyit meg a [az Azure portal](https://portal.azure.com) elemre kattint, és **törlése**, felhasználói a következő üzenet jelenik meg: 

(Eke) t a "csatlakoztatva" virtuális géphez

![Képernyőkép a portal, a virtuális gép (klasszikus) "Delete" Hiba ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


-(Eke) t "leválasztott" virtuális géphez

![Képernyőkép a portal, a virtuális gép (klasszikus) "Delete" nem hibás ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
A felhasználó megkísérel törölni egy tárfiókot, már nem használt, klasszikus PowerShell-parancsmagok használatával. Felhasználó a következő üzenet jelenik meg:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Storage-fiók myclassicaccount rendelkezik néhány aktív lemezképek és/vagy lemezek, például:  
> myclassicaccount. Győződjön meg arról, ezek a lemezképek és/vagy (eke) t a tárfiók törlése előtt el kell távolítani.</span>

## <a name="unable-to-delete-storage-container"></a>Nem sikerült törölni a storage-tároló

Amikor a felhasználó megkísérel törölni egy hagyományos tárfiók blob-tárolóba, amely már nincs rá szükség, a felhasználó a következő viselkedés jelenhet meg.

#### <a name="azure-portal"></a>Azure Portal 
Az Azure portal nem engedélyezi a felhasználó egy tároló törlése, ha a "Lemez" bérlet létezik a tárolóban *.vhd oldal blobfájlra mutat. Azt a rendszer kialakításából fakad, hogy azokon (eke) t bérleti szolgáltatásbeli fájl véletlen törlését. 

![Nyissa meg a portál, a storage-tároló "list" ablaktábla képernyőképe](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Ha a felhasználó úgy dönt, hogy törli a PowerShell használatával, azt a következő hibát eredményez. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : A távoli kiszolgáló hibát adott vissza: (412) itt jelenleg a tároló bérletét, és nem a bérlet Azonosítóját a kérésben megadott... HTTP-állapotkód: 412 - HTTP hibaüzenet: Jelenleg a címbérlet a tárolót, és a kérésben megadott nincs bérlet azonosítója.</span>

## <a name="unable-to-delete-a-vhd"></a>Nem sikerült törölni a virtuális merevlemez 

Miután az Azure virtuális gépen, a felhasználó megpróbál a vhd-fájl (lapblob) törölje, majd az alábbi üzenetet kapja:

#### <a name="azure-portal"></a>Azure Portal 
A portálon, a két megközelítés a törlésre kijelölt blobok listáját függően lehetnek.

1. Ha csak a "Bérelt" blobok ki van jelölve, majd a Törlés gomb nem jelenik meg.
![Képernyőkép a portal, a tárolót a blob "list" ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Ha a "Bérelt" és "Elérhető" blobok vegyesen ki van jelölve, a "Törlés" gombra. megjelenik-e. De a "Delete" művelet hagynak a lapblobok és lemez bérletét amelyek mögött. 
![A portálon nyissa meg a tárolót a blob "list" ablaktábla képernyőképe](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Képernyőkép a portal, a kiválasztott blobbal együtt a "delete" panel megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Ha a felhasználó úgy dönt, hogy törli a PowerShell használatával, azt a következő hibát eredményez. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : A távoli kiszolgáló hibát adott vissza: (412) itt jelenleg a BLOB bérletét, és nem a bérlet Azonosítóját a kérésben megadott... HTTP-állapotkód: 412 - HTTP hibaüzenet: Jelenleg a címbérlet a blob és a kérésben megadott nincs bérlet azonosítója.</span>


## <a name="resolution-steps"></a>A megoldás lépései

### <a name="to-remove-classic-disks"></a>Klasszikus lemezek eltávolítása
Az Azure Portal webhelyen kövesse az alábbi lépéseket:
1.  Lépjen az [Azure Portalra](https://portal.azure.com).
2.  Keresse meg a Disks(classic). 
3.  Kattintson a Lemezek fülre. ![Képernyőkép a portal, a tárolót a blob "list" ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Válassza ki az adatlemezt, majd kattintson a Lemez törlése gombra.
 ![Képernyőkép a portal, a tárolót a blob "list" ablaktábla megnyitása](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Ismételje meg a törlési műveletet, amely korábban nem sikerült.
6.  A storage-fiók vagy a tároló nem törölhető, mindaddig, amíg egyetlen lemez rendelkezik.

### <a name="to-remove-classic-images"></a>Klasszikus lemezkép eltávolítása   
Az Azure Portal webhelyen kövesse az alábbi lépéseket:
1.  Lépjen az [Azure Portalra](https://portal.azure.com).
2.  Keresse meg az operációsrendszer-lemezképek (klasszikus).
3.  A kép törléséhez.
4.  Ismételje meg a törlési műveletet, amely korábban nem sikerült.
5.  A storage-fiók vagy a tároló nem törölhető, mindaddig, amíg egy lemezkép rendelkezik.
