---
title: Hibák elhárítása a klasszikus Azure Storage-fiókok,-tárolók vagy virtuális merevlemezek törlésekor | Microsoft Docs
description: A csatlakoztatott virtuális merevlemezeket tartalmazó tárolási erőforrások törlésekor felmerülő problémák elhárítása.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915037"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>A klasszikus tárolási erőforrás törlésével kapcsolatos hibák elhárítása
Ez a cikk hibaelhárítási útmutatót tartalmaz, ha a következő hibák valamelyike a klasszikus Azure Storage-fiók, a tároló vagy a *. vhd oldal blob-fájljának törlésére tett kísérlet során következik be. 


Ez a cikk csak a klasszikus tárolási erőforrásokkal kapcsolatos problémákat fedi le. Ha a felhasználó töröl egy klasszikus virtuális gépet a Azure Portal, a PowerShell vagy a CLI használatával, a lemezek nem törlődnek automatikusan. A felhasználó lekéri a "lemez" erőforrás törlésének lehetőségét. Ha a lehetőség nincs bejelölve, a "lemez" erőforrás megakadályozza a Storage-fiók, a tároló és a tényleges *. vhd oldal blob-fájljának törlését.

Az Azure-lemezekkel kapcsolatos további információkért tekintse meg [a következőt](../../virtual-machines/windows/managed-disks-overview.md):. Az Azure megakadályozza a virtuális gépekhez csatolt lemezek törlését a sérülés megelőzése érdekében. Emellett megakadályozza a tárolók és a Storage-fiókok törlését is, amelyek egy virtuális géphez csatolt oldal-blobtal rendelkeznek. 

## <a name="what-is-a-disk"></a>Mi a "lemez"?
A "Disk" (lemez) erőforrás egy *. vhd oldal blob-fájljának a virtuális géphez, operációsrendszer-lemezként vagy adatlemezként való csatlakoztatására szolgál. Az operációsrendszer-lemez vagy az adatlemez-erőforrás a törlésig továbbra is a *. vhd fájl bérletét fogja tartani. Az alábbi képen látható elérési úton lévő összes tárolási erőforrás nem törölhető, ha a "lemez" erőforrás rá mutat.

![Képernyőkép a portálról, amelyen a lemez (klasszikus) "tulajdonság" panel nyílik meg.](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>A klasszikus virtuális gépek törlésének lépései 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Törölje a klasszikus virtuális gépet.
2. Ha a "lemezek" jelölőnégyzet be van jelölve, akkor a blobhoz (a fenti képen látható) tartozó **lemez címbérlete** megszakadt. A tényleges oldal blob *. vhd fájlja továbbra is létezik a Storage-fiókban.
![képernyőkép a portálról, amelyen a virtuális gép (klasszikus) "Delete" (Törlés) ablaktáblája nyitva van](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Ha a lemez (ek) bérlete megszakadt, maga a blob (ok) is törölhető. A Storage-fiók vagy-tároló törölhető, ha a bennük lévő összes "Disk" erőforrás törölve van.

>[!NOTE] 
>Ha a felhasználó törli a virtuális GÉPET, de nem a VHD-t, a tárolási díjak továbbra is az oldal blob *. vhd fájljában lesznek felhalmozva. A díjak a Storage-fiók típusától függenek, további részletekért lásd a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/storage/) . Ha a felhasználó már nem kívánja használni a VHD (ka) t, törölje azokat/azokat a jövőbeli költségek elkerülése érdekében. 

## <a name="unable-to-delete-storage-account"></a>Nem sikerült törölni a Storage-fiókot 

Ha a felhasználó egy már nem szükséges klasszikus Storage-fiókot próbál törölni, a felhasználó a következő viselkedést láthatja.

#### <a name="azure-portal"></a>Azure Portal 
A felhasználó a [Azure Portal](https://portal.azure.com) klasszikus Storage-fiókjára navigál, és a **Törlés**gombra kattint, a felhasználó a következő üzenetet fogja látni: 

Virtuális géphez csatlakoztatott lemez (ek)

![Képernyőkép a portálról, amelyen a virtuális gép (klasszikus) "Delete" (Törlés) panelje nyitva van](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Ha a lemez (ek) "nincs csatlakoztatva" a virtuális géphez

![Képernyőkép a portálról, amelyen a virtuális gép (klasszikus) "Delete" nem hiba panelje van megnyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
A felhasználó olyan Storage-fiókot próbál törölni, amelyet már nem használ a klasszikus PowerShell-parancsmagok használatával. A felhasználó a következő üzenetet fogja látni:

> <span style="color:cyan">**Remove-AzureStorageAccount-StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: BadRequest: a Storage-fiók myclassicaccount tartalmaz néhány aktív lemezképet és/vagy lemezt, például a következőt:  
> myclassicaccount. A Storage-fiók törlése előtt győződjön meg arról, hogy ezek a lemezképek és/vagy lemezek el lesznek távolítva.</span>

## <a name="unable-to-delete-storage-container"></a>Nem sikerült törölni a tárolót

Ha a felhasználó egy már nem szükséges klasszikus Storage BLOB-tárolót próbál törölni, a felhasználó a következő viselkedést láthatja.

#### <a name="azure-portal"></a>Azure Portal 
Azure Portal nem engedélyezi, hogy a felhasználó törölje a tárolót, ha a "lemez (ek)" bérlet egy *. vhd oldal blob-fájljára mutat a tárolóban. Ez a terv a lemez (ek) bérlettel rendelkező VHD (k) fájl véletlen törlésének megakadályozása. 

![Képernyőkép a portálról, amelyen a Storage tároló "List" paneljén nyílik meg](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Ha a felhasználó a PowerShell használatával választja a törlést, a következő hibaüzenetet fogja eredményezni. 

> <span style="color:cyan">**Remove-AzureStorageContainer-Context $context – VHD-k neve**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: a távoli kiszolgáló a következő hibát adta vissza: (412) jelenleg van bérlet a tárolóban, és nincs megadva címbérlet-azonosító a kérelemben. HTTP-állapotkód: 412 – HTTP-hibaüzenet: a tárolón jelenleg bérlet található, és a kérelemben nincs megadva címbérlet-azonosító.</span>

## <a name="unable-to-delete-a-vhd"></a>Nem sikerült törölni a VHD-t 

Az Azure-beli virtuális gép törlése után a felhasználó megpróbálja törölni a VHD-fájlt (blob), és az alábbi üzenetet kapja:

#### <a name="azure-portal"></a>Azure Portal 
A portálon két élmény lehet a törlésre kijelölt Blobok listájától függően.

1. Ha csak a "bérelt" Blobok vannak kiválasztva, akkor a törlés gomb nem jelenik meg.
![képernyőkép a portálról, amelyen a tároló blob "List" panelje nyitva van](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Ha a "bérelt" és "elérhető" Blobok kombinációja be van jelölve, a "Törlés" gomb jelenik meg. A "Törlés" művelet azonban az oldal Blobok mögött marad, amelyeknek lemezes bérletük van. 
![képernyőkép a portálról: a tároló blob "List" panelje nyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![képernyőkép a portálról, amelyen a kijelölt blob "Delete" (Törlés) ablaktáblája meg van nyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Ha a felhasználó a PowerShell használatával választja a törlést, a következő hibaüzenetet fogja eredményezni. 

> <span style="color:cyan">**Remove-AzureStorageBlob-Context $context – Container VHD-blob "classicvm-os-8698. vhd"** </span>
> 
> <span style="color:red">Remove-AzureStorageBlob: a távoli kiszolgáló a következő hibát adta vissza: (412) jelenleg van bérlet a blobon, és nincs megadva címbérlet-azonosító a kérelemben. HTTP-állapotkód: 412 – HTTP-hibaüzenet: jelenleg van bérlet a blobon, és nincs megadva címbérlet-azonosító a kérelemben.</span>


## <a name="resolution-steps"></a>A megoldás lépései

### <a name="to-remove-classic-disks"></a>Klasszikus lemezek eltávolítása
Kövesse az alábbi lépéseket a Azure Portalon:
1.  Lépjen az [Azure Portalra](https://portal.azure.com).
2.  Navigáljon a lemezekhez (klasszikus). 
3.  Kattintson a lemezek fülre. ![képernyőkép a portálról, amelyen a tároló blob "List" panelje meg van nyitva](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Válassza ki az adatlemezt, majd kattintson a Lemez törlése gombra.
 ![képernyőkép a portálról, amelyen a tároló blob "List" panelje nyitva van](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Próbálkozzon újra a korábban meghiúsult törlési művelettel.
6.  A Storage-fiók vagy-tároló nem törölhető, amíg egyetlen lemezzel rendelkezik.

### <a name="to-remove-classic-images"></a>Klasszikus rendszerképek eltávolítása   
Kövesse az alábbi lépéseket a Azure Portalon:
1.  Lépjen az [Azure Portalra](https://portal.azure.com).
2.  Navigáljon az operációsrendszer-lemezképek (klasszikus) elemre.
3.  Törölje a rendszerképet.
4.  Próbálkozzon újra a korábban meghiúsult törlési művelettel.
5.  A Storage-fiók vagy-tároló nem törölhető, ha egyetlen képpel rendelkezik.
