---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 912d5ec399ac59d81a7d9da9b494d8ee50e720e1
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55736129"
---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Gyakran előforduló hibák a klasszikusból Azure Resource Manager-alapú környezetbe való migrálás során
Ez a cikk összegyűjti az IaaS-erőforrások klasszikus Azure üzemi modellből Azure Resource Manager verembe történő migrálása során leggyakrabban előforduló hibákat és kezelési lehetőségeiket.

## <a name="list-of-errors"></a>Hibalista
| Hibasztring | Kezelés |
| --- | --- |
| Belső kiszolgálóhiba |Bizonyos esetekben ez egy átmeneti hiba, ami újbóli próbálkozással megszűnik. Ha továbbra is fennáll, [vegye fel a kapcsolatot az Azure ügyfélszolgálatával](../articles/azure-supportability/how-to-create-azure-support-request.md), mivel a platformnaplók vizsgálata szükséges. <br><br> **MEGJEGYZÉS:** Miután az incidens követi nyomon a támogatási csoporthoz, ne próbálkozzon intézkedésekkel, előfordulhat, hogy ez az adott környezet nem kívánt következményekkel. |
| A migrálás az {üzemeltetett szolgáltatás} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem támogatott, mivel ez egy PaaS üzemelő példány (webes/feldolgozó). |Ez akkor fordul elő, ha az üzemelő példány egy webes/feldolgozói szerepkört tartalmaz. Mivel a migrálás csak a Virtual Machines esetében támogatott, távolítsa el a webes/feldolgozói szerepkört az üzemelő példányból, és próbálkozzon újra a migrálással. |
| A {sablonnév} sablon üzembe helyezése meghiúsult. CorrelationId={guid} |A migrálási szolgáltatás hátterében Azure Resource Manager-sablonok használatával hozunk létre erőforrásokat az Azure Resource Manager veremben. Mivel a sablonok idempotensek, ezért általában a migrálási művelet ismételt végrehajtásával biztonságosan megkerülheti ezt a hibát. Ha a hiba továbbra is fennáll, [forduljon az Azure ügyfélszolgálatához](../articles/azure-supportability/how-to-create-azure-support-request.md), és adja meg számokra a CorrelationId azonosítót. <br><br> **MEGJEGYZÉS:** Miután az incidens követi nyomon a támogatási csoporthoz, ne próbálkozzon intézkedésekkel, előfordulhat, hogy ez az adott környezet nem kívánt következményekkel. |
| A {virtuális hálózat neve} virtuális hálózat nem létezik. |Ez akkor fordulat elő, ha a Virtual Network hálózatot az új Azure Portalon hozta létre. A tényleges Virtual Network hálózat neve a „Csoport * <VNET name>” mintát követi |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a {bővítmény neve} bővítményt, amely az Azure Resource Managerben nem támogatott. Javasolt a virtuális gépről eltávolítani a bővítményt, mielőtt folytatná a migrálást. |Az XML bővítmények, például a BGInfo 1.*, nem támogatottak az Azure Resource Managerben. Ezért az ilyen bővítmények nem migrálhatók. Ha az ilyen bővítmények telepítve maradnak a virtuális gépen, a rendszer automatikusan eltávolítja azokat a migrálás befejezése előtt. |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a VMSnapshot/VMSnapshotLinux bővítményt, amely jelenleg a migrálási szolgáltatásban nem támogatott. Távolítsa el a bővítményt a virtuális gépről, majd a migrálás befejezése után adja újra hozzá az Azure Resource Manager használatával |Ez az a forgatókönyv, amelyikben a virtuális gép konfigurálva van az Azure Backup szolgáltatáshoz. Mivel ez a funkció jelenleg nem támogatott forgatókönyv, kérjük, kövesse a helyen található áthidaló megoldást https://aka.ms/vmbackupmigration |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a {bővítmény neve} bővítményt, amelynek az állapotát a virtuális gép nem jelenti. Ezért ez a virtuális gép nem migrálható. Gondoskodjon róla, hogy a virtuális gép jelentse a bővítmény állapotát, vagy távolítsa el a bővítményt a virtuális gépről, és próbálkozzon újra a migrálással. <br><br> Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a {bővítmény neve} bővítményt, amely a {kezelői állapot} kezelői állapotot jelenti. Ezért a virtuális gép nem migrálható. Gondoskodjon róla, hogy a bővítmény jelentett állapota {kezelői állapot}, vagy távolítsa el a virtuális gépről, és próbálkozzon újra a migrálással. <br><br> Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépének virtuálisgép-ügynöke az ügynök átfogó állapotát Not Ready állapotként jelenti. Ezért ez a virtuális gép nem migrálható, ha migrálható bővítménnyel rendelkezik. Gondoskodjon róla, hogy a virtuálisgép-ügynök átfogó állapotát Ready állapotként jelentse. Tekintse meg https://aka.ms/classiciaasmigrationfaqs. |Az Azure vendég ügynök és a VM-bővítmény számára szükség van kimenő internetkapcsolatra a VM-tárfiókhoz az állapotuk közléséhez. Az állapothibák gyakori okai a következők lehetnek <li> egy hálózati biztonsági csoport, amely blokkolja a kimenő internetkapcsolatokat <li> ha a VNET helyszíni DNS-kiszolgálókkal rendelkezik, és megszűnik a DNS-kapcsolat <br><br> Ha továbbra is nem támogatott állapot látható, a bővítmények eltávolításával átugorhatja ezt az ellenőrzést, és folytathatja a migrálást. |
| A migrálás az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem támogatott, mivel ez több rendelkezésre állási csoporttal rendelkezik. |Jelenleg csak az 1 vagy kevesebb rendelkezésre állási csoporttal rendelkező üzemeltetett szolgáltatások migrálhatók. A probléma megkerülése érdekében a további rendelkezésre állási csoportokat és az azokban lévő virtuális gépeket mozgassa át egy másik üzemeltetett szolgáltatásba. |
| A migrálás az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem támogatott, mivel olyan virtuális gépeket tartalmaz, amelyek nem részei rendelkezésre állási csoportnak, bár az üzemeltetett szolgáltatás tartalmaz egyet. |Ebben a forgatókönyvben megkerülő megoldásként áthelyezheti az összes virtuális gépet egyetlen rendelkezésre állási csoportba, vagy eltávolíthatja az összes virtuális gépet a rendelkezésre állási csoportból az üzemeltetett szolgáltatásban. |
| A {virtuális hálózat neve} tárfiók/üzemeltetett szolgáltatás/virtuális hálózat migrálása folyamatban van, és ezért nem módosítható |Ez a hiba akkor lép fel, ha az „Előkészítés” migrálási művelet már végre lett hajtva az erőforráson, és egy olyan művelet aktiválódik, amely módosítaná az erőforrást. Mivel az „Előkészítés” művelet után a felügyeleti sík zárolva van, az erőforrás bármilyen módosítását blokkolja a rendszer. A felügyeleti sík feloldásához futtassa a „Véglegesítés” migrálási műveletet a migrálás befejezéséhez, vagy a „Megszakítás” migrálási műveletet az „Előkészítés” művelet visszavonásához. |
| A migrálás nem engedélyezett {az üzemeltetett szolgáltatás-neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális Géphez, mert állapotban: RoleStateUnknown. A migrálás csak abban az esetben engedélyezett, ha a virtuális gép a következő állapotok valamelyikében van: Running (Fut), Stopped (Leállítva), Stopped Deallocated (Leállítva és felszabadítva). |A virtuális gép állapotváltáson mehet keresztül, ami általában az üzemeltetett szolgáltatáson végrehajtott frissítési műveletek, például az újraindítás, a bővítmények telepítése stb. során történik. Mielőtt folytatná a migrálást, javasolt megvárni, amíg a frissítési művelet befejeződik az üzemeltetett szolgáltatáson. |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példányában lévő {virtuális gép neve} virtuális gép {adatlemez neve} adatlemezének {adatlemez mögötti vhd blob mérete} bájtos fizikai blobmérete nem egyezik a virtuális gép adatlemezének {az adatlemez vm api-ban megadott mérete} bájtos logikai méretével. A migrálás során nem lesz megadva méret az Azure Resource Manager virtuális gép adatlemeze számára. | Ez a hiba akkor lép fel, ha anélkül méretezte át a virtuálismerevlemez-blobot, hogy frissítette volna a méretet a virtuális gép API modelljében. A hibakezelés részletes lépéseit az [alábbi szakasz](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes) ismerteti.|
| Tárolási hiba lépett fel a {felhőszolgáltatás neve} felhőszolgáltatásban lévő {virtuális gép neve} virtuális gépre mutató {adatlemez URI azonosítója} adathordozó-hivatkozással rendelkező {adatlemez neve} adatlemez érvényesítése során. Győződjön meg róla, hogy a virtuális merevlemez adathordozó-hivatkozása elérhető erről a virtuális gépről | Ez a hiba akkor léphet fel, ha a virtuális gép lemezei törölve lettek, és már nem érhetőek el. Gondoskodjon róla, hogy a virtuális gép lemezei léteznek.|
| A {felhőszolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a(z) {virtuális merevlemez uri-azonosítója} adathordozó-hivatkozással rendelkező lemezt, amely a {virtuális merevlemez blob neve} nevű blobot tartalmazza, amely az Azure Resource Managerben nem támogatott. | Ez a hiba akkor fordul elő, ha a blob neve tartalmazza a „/” karaktert, ami jelenleg nem támogatott a Compute erőforrás-szolgáltatóban. |
| A migrálás a {felhőszolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem engedélyezett, mivel az nem regionális hatókörű. Tekintse meg http://aka.ms/regionalscope regionális hatókörbe való áttéréssel, ehhez a központi telepítéshez. | Az Azure 2014-ben jelentette be, hogy a hálózati erőforrások a fürtszintű hatókörből átkerülnek a regionális hatókörbe. Lásd: [http://aka.ms/regionalscope] további részletekért (http://aka.ms/regionalscope). Ez a hiba akkor lép fel, ha a migrált üzemelő példányhoz nincs definiálva egy olyan frissítési művelet, amely automatikusan átmozgatná regionális hatókörbe. A legjobb áthidaló megoldás, ha hozzáad egy végpontot valamely virtuális géphez vagy egy adatlemezt a virtuális géphez, majd újra megkísérli a migrálást. <br> Lásd: [Végpontok beállítása klasszikus Windows virtuális gépeken az Azure-ban](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) vagy [Adatlemez csatolása a klasszikus üzemi modellel létrehozott Windows virtuális gépekhez](../articles/virtual-machines/windows/classic/attach-disk.md)|
| Áttelepítés nem támogatott a {virtuális hálózat neve} virtuális hálózati átjáró nélküli PaaS üzemelő példányok, mert. | Ez a hiba akkor fordul elő, ha például az Application Gateway vagy az API Management-szolgáltatások a virtuális hálózathoz csatlakozó átjáró nélküli PaaS üzemelő példányok.|


## <a name="detailed-mitigations"></a>Részletes hibakezelés

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Virtuális gép olyan adatlemezzel, amelynek fizikai blobmérete (bájtban) nem egyezik a virtuális gép adatlemezének logikai méretével (bájtban).

Ez akkor fordulhat elő, ha az adatlemez logikai mérete nincs szinkronban a virtuális merevlemez tényleges blobméretével. Ez a következő parancsok használatával könnyen ellenőrizhető:

#### <a name="verifying-the-issue"></a>A probléma ellenőrzése

```PowerShell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzureStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>A probléma kezelése

```PowerShell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Virtuális gép áthelyezése egy másik előfizetésre a migrálás végeztével

A migrálási folyamat után esetleg szeretné átmozgatni a virtuális gépet egy másik előfizetésre. Ha azonban olyan titokkal/tanúsítvánnyal rendelkezik a virtuális gépen, amely egy Key Vault-erőforrásra hivatkozik, az átmozgatás jelenleg nem támogatott. Az alábbi utasítások végrehajtásával hidalhatja át a problémát. 

#### <a name="powershell"></a>PowerShell
```powershell
$vm = Get-AzureRmVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzureRmVMSecret -VM $vm
Update-AzureRmVM -ResourceGroupName "MyRG" -VM $vm
```
#### <a name="azure-cli"></a>Azure CLI

```bash
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```
