---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74566280"
---
Az írásgyorsító az M sorozatú virtuális gépek (VM-ek) lemezes funkciója a prémium szintű storage-ban, kizárólag az Azure Felügyelt lemezekkel. Ahogy a neve is kimondja, a funkció célja, hogy javítsa az Azure Premium Storage-on történő írási műveletek I/O-késését. Az írásgyorsító ideális, ahol a naplófájlok frissítése imperációs fájl szükséges a lemezen való, a modern adatbázisok számára rendkívül nagy teljesítményt nyújtva.

Az Írásgyorsító általában elérhető az M sorozatú virtuális gépekhez a nyilvános felhőben.

## <a name="planning-for-using-write-accelerator"></a>Az Írásgyorsító használatának megtervezése

Írásgyorsítót kell használni a tranzakciónaplót tartalmazó kötetekhez vagy a DBMS-naplóinak ismétléséhez. Nem ajánlott írásgyorsítót használni a DBMS adatköteteihez, mivel a szolgáltatás úgy lett optimalizálva, hogy naplólemezeken használható legyen.

Az Írásgyorsító csak az [Azure által felügyelt lemezekkel](https://azure.microsoft.com/services/managed-disks/)együtt működik.

> [!IMPORTANT]
> A virtuális gép operációsrendszer-lemezének írásgyorsító engedélyezése újraindítja a virtuális gépet.
>
> Írásgyorsító engedélyezése egy meglévő Azure-lemezre, amely nem része egy kötetbuild több lemezből windowsos lemez- vagy kötetkezelőkkel, Windows tárolóhelyekkel, Windows kibővített fájlkiszolgálóval (SOFS), Linux LVM-mel vagy MDADM-mel, az Azure-lemezt elérő számítási feladatból le kell állítani. Az Azure-lemezt használó adatbázis-alkalmazásokat le kell állítani.
>
> Ha engedélyezni vagy letiltani szeretné az Írásgyorsítót egy olyan meglévő kötethez, amely több Azure Premium Storage lemezből épül fel, és windowsos lemez- vagy kötetkezelők, Windows-tárolóhelyek, Windows kibővített fájlkiszolgáló (SOFS), Linux LVM vagy MDADM használatával csíkozott, az összes a kötetet építő lemezeket külön lépésekben kell engedélyezni vagy letiltani az Írássegéd hez. **Az írásgyorsító engedélyezése vagy letiltása előtt állítsa le az Azure virtuális gépleállítását.**

Az írásgyorsító engedélyezése az operációs rendszer lemezeihez nem szükséges az SAP-val kapcsolatos virtuális gép konfigurációkhoz.

### <a name="restrictions-when-using-write-accelerator"></a>Az írásgyorsító használatával kapcsolatos korlátozások

Írásgyorsító használata kor egy Azure-lemez/VHD, ezek a korlátozások érvényesek:

- A prémium szintű lemezgyorsítótárazását "Nincs" vagy "Csak olvasható" beállításra kell állítani. Az összes többi gyorsítótárazási mód nem támogatott.
- A pillanatkép jelenleg nem támogatott az írásgyorsítót engedélyező lemezek esetében. A biztonsági mentés során az Azure Backup szolgáltatás automatikusan kizárja a virtuális géphez csatlakoztatott írásgyorsító-kompatibilis lemezeket.
- Csak a kisebb I/O-méretek (<=512 KiB) haladnak a gyorsított útvonalon. Olyan munkaterhelési helyzetekben, amikor az adatok tömeges betöltése, vagy ahol a tranzakciós napló pufferek a különböző DBMS-ben nagyobb mértékben vannak feltöltve, mielőtt a tárolóba kerülne, valószínű, hogy a lemezre írt I/O nem a gyorsított elérési utat veszi figyelembe.

Vannak korlátok az Azure Premium Storage virtuális gépek, amelyek a Write Accelerator által támogatott korlátok. A jelenlegi határértékek a következők:

| Virtuális gép termékváltozata | Írásgyorsító lemezek száma | Írásgyorsító lemezIOPS virtuális gépenként |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2 M208s_v2 M208s_v2 M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Az IOPS-korlátok virtuális gépenként, és *nem* lemezenként. Minden írásgyorsító lemez ugyanazt az IOPS-korlátot rendelkezik virtuális gépenként.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Írásgyorsító engedélyezése egy adott lemezen

A következő néhány szakasz ismerteti, hogyan write accelerator engedélyezhető az Azure Premium Storage virtuális felhasználó.

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak az Írásgyorsító használatára ebben az időpontban:

- Az Azure Write Accelerator alkalmazásához kívánt lemezek kell [Azure felügyelt lemezek](https://azure.microsoft.com/services/managed-disks/) prémium szintű storage-on.
- M sorozatú virtuális gépnek kell lennie.

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Azure-beli írásgyorsító engedélyezése az Azure PowerShell-lel

Az Azure Power Shell modul az 5.5.0-s verzióból tartalmazza a megfelelő parancsmagok módosításait, hogy engedélyezze vagy letiltsa a Write Accelerator-t adott Azure Premium Storage-lemezekhez.
Az Írásgyorsító által támogatott lemezek engedélyezéséhez vagy üzembe helyezéséhez a következő Power Shell-parancsok módosultak, és kiterjesztették az Írásgyorsító paraméterének elfogadására.

Egy új kapcsolóparaméter, **a -WriteAccelerator** lett hozzáadva a következő parancsmagokhoz:

- [Set-AzVmosDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Ha nem adja meg a paramétert, a tulajdonság hamis lesz, és olyan lemezeket telepít, amelyeket az Írásgyorsító nem támogat.

Egy új kapcsolóparaméter, **az -OsDiskWriteAccelerator** lett hozzáadva a következő parancsmagokhoz:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

A paraméter megadása nem adja meg a tulajdonságot alapértelmezés szerint hamisra, és olyan lemezeket ad vissza, amelyek nem használják az Írásgyorsítót.

Egy új, választható logikai (nem nullázható) paraméter, **az -OsDiskWriteAccelerator** lett hozzáadva a következő parancsmagokhoz:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Adja meg $true vagy $false az Azure Write Accelerator támogatásának szabályozásához a lemezekkel.

Példák a parancsokra így nézhetnek ki:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Két fő forgatókönyv parancsfájlt, ahogy az a következő szakaszokban látható.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Írási gyorsító által támogatott új lemez hozzáadása a PowerShell használatával

Ezzel a parancsfájllal új lemezt adhat a virtuális géphez. Az ezzel a parancsfájllal létrehozott lemez írásgyorsítót használ.

Cserélje `myVM` `myWAVMs`le `log001`a , , , a lemez méretét és a lemez LunID azonosítóját az adott központi telepítésnek megfelelő értékekre.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Írásgyorsító engedélyezése meglévő Azure-lemezen a PowerShell használatával

Ezzel a parancsfájllal engedélyezheti az Írásgyorsítót egy meglévő lemezen. Cserélje `myVM` `myWAVMs`le `test-log001` a , és az adott központi telepítésnek megfelelő értékekre. A parancsfájl írásgyorsítót ad egy meglévő **lemezhez,** ahol a $newstatus értéke "$true" értékre van állítva. A "$false" érték használatával letiltja az Írásgyorsítót egy adott lemezen.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> A fenti parancsfájl végrehajtása leválasztja a megadott lemezt, engedélyezi az Írásgyorsítót a lemezhez, majd ismét csatlakoztatja a lemezt

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Írásgyorsító engedélyezése az Azure Portalon

Az írásgyorsítót azon a portálon keresztül engedélyezheti, ahol megadhatja a lemezgyorsítótárazási beállításokat:

![Gyorssegéd írása az Azure Portalon](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Írásgyorsító engedélyezése az Azure CLI-vel

Az Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) használatával engedélyezheti a gyorsírási gyorsítót.

Az írásgyorsító meglévő lemezen történő engedélyezéséhez használja az [az vm frissítést,](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)és a következő példákat használhatja, ha a lemeznevet, a VMName-t és a ResourceGroup-ot a saját értékeire cseréli le:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Ha olyan lemezt szeretne csatolni, amelyen az Írásgyorsító engedélyezve van, használja az [az vm lemezcsatolást,](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach)a következő példát használhatja, ha saját értékeit helyettesíti:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Az írásgyorsító letiltásához használja [az az vm frissítést,](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)és a tulajdonságokat hamisra állítsa:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Írásgyorsító engedélyezése rest api-k használatával

Az Azure Rest API-n keresztül történő üzembe helyezéshez telepítenie kell az Azure armclient.

### <a name="install-armclient"></a>Armclient telepítése

Az armclient futtatásához a Chocolatey-n keresztül kell telepítenie. Telepítheti a cmd.exe vagy powershell. Emelt szintű jogosultságok használata ezekhez a parancsokhoz ("Futtatás rendszergazdaként").

A cmd.exe segítségével futtassa a következő parancsot:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

A Power Shell használatával futtassa a következő parancsot:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Most már telepítheti az armclient segítségével a következő parancsot cmd.exe vagy PowerShell`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>A virtuális gép jelenlegi konfigurációjának beszerzése

A lemezkonfiguráció attribútumainak módosításához először be kell szereznie az aktuális konfigurációt egy JSON-fájlban. Az aktuális konfigurációt a következő parancs végrehajtásával szerezheti be:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Cserélje le a "<<   >>" kifejezéseit az adatokra, beleértve a JSON-fájl által megadott fájlnevet is.

A kimenet így nézhet ki:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Ezután frissítse a JSON-fájlt, és engedélyezze az Írásgyorsítót a "log1" nevű lemezen. Ez úgy valósítható meg, hogy ezt az attribútumot a lemez gyorsítótár-bevitele után hozzáadja a JSON-fájlhoz.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Ezután frissítse a meglévő központi telepítést ezzel a paranccsal:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

A kimenetnek az alábbihoz hasonlóan kell kinéznie. Láthatja, hogy az írási gyorsító engedélyezve van egy lemezhez.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Miután végrehajtotta ezt a módosítást, a meghajtót az Írásgyorsítónak támogatnia kell.
