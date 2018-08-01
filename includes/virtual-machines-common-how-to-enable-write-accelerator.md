---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 6/8/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 049c5d86bc78a8861faff13d82a47579ac24c516
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361401"
---
# <a name="enable-write-accelerator"></a>Írásgyorsító engedélyezése

Az írási gyorsító egy lemez képesség az M-sorozatú Virtual Machines (VM) a prémium szintű tárolóba az Azure Managed Disks kizárólag. Az államok neve, ahogy az a funkciók célja, hogy javítása az Azure Premium Storage írására i/o késését. Az írási gyorsító kiválóan alkalmas ahol log fájl frissítések szükségesek-e megőrizni kívánt lemez egy rendkívül nagy teljesítményű módon modern adatbázisok.

Az írási gyorsító általánosan elérhető az M-sorozat virtuális gépei a nyilvános felhőben.

## <a name="planning-for-using-write-accelerator"></a>Írásgyorsító használatának megtervezése

Az írási megoldásgyorsító használandó a kötetek, a tranzakciós napló tartalmaznak, vagy újra elvégzi az adatbázis-kezelő naplóit. Nem ajánlott használandó Írásgyorsító az adatköteteket az adatbázis-kezelő, a funkciót naplózási használható lett optimalizálva.

Írási gyorsító csak együtt működik [Azure managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Az operációsrendszer-lemez a virtuális gép Írásgyorsító engedélyezése a virtuális gép újraindul.
>
> Egy meglévő Azure lemezhez, amely nem része egy kötet build kívül több lemez a lemeznek vagy kötetnek kezelők, a Windows Storage Spaces, Windows kibővíthető fájlkiszolgáló (SOFS) Linux LVM, vagy a MDADM, a munkaterhelés elérése az Azure-lemez Windows Írásgyorsító engedélyezése meg kell leállítani. Adatbázis-alkalmazások az Azure disk használatával le kell állítani.
>
> Ha szeretné engedélyezni vagy letiltani a meglévő kötet, amely több Azure Premium Storage-lemez ki van építve, és a Windows-lemeznek vagy kötetnek-kezelők használatával csíkozott Write Accelerator, Windows Storage Spaces Windows kibővíthető fájlkiszolgáló (SOFS), Linux LVM vagy MDADM, minden a kötet létrehozásához lemezeket kell kell engedélyezhető vagy letiltható az Írásgyorsító különálló lépések. **Engedélyezése, illetve az ilyen konfiguráció Írásgyorsító letiltása előtt állítsa le az Azure virtuális gép**.

Az operációsrendszer-lemezek Írásgyorsító engedélyezése nem lehet az SAP-kapcsolódó Virtuálisgép-konfiguráció szükséges.

### <a name="restrictions-when-using-write-accelerator"></a>Amikor Írásgyorsítót használó korlátozások

Írásgyorsító egy Azure lemez/VHD-t használja, ha ezek a korlátozások vonatkoznak:

- A prémium szintű lemezek gyorsítótárazása állítsa "None" vagy "Csak Olvasás". Minden más gyorsítótárazási mód nem támogatott.
- Az engedélyezett Írásgyorsító lemezek pillanatképek még nem támogatott. Ez a korlátozás blokkolja az Azure Backup szolgáltatás hajthatnak végre a virtuális gép összes lemezének alkalmazáskonzisztens pillanatképet.
- Csak kisebb méretű i/o-méretek (< = 32 KiB) gyorsított elérési tart. Számítási helyzetekben, ahol adatok tömeges kezd betöltve, vagy ha a tranzakciós napló pufferek, a különböző DBMS előtt első megőrzi a a storage nagyobb mértékben van kitöltve, valószínűleg, amely az i/o írt lemez nem tart a gyorsított elérési útja.

Az Azure Premium Storage virtuális merevlemezek Írásgyorsító támogató virtuális gépenként korlátozva van. A jelenlegi korlátok a következők:

| A VM-TERMÉKVÁLTOZATOK | Írásgyorsító lemezek száma | Írható gyorsító lemez iops-t, virtuális gépenként |
| --- | --- | --- |
| M128ms, 128s | 16 | 8000 |
| M64ms, M64ls, M64s | 8 | 4000 |
| M32ms, M32ls, M32ts, M32s | 4 | 2000 |
| M16ms, M16s | 2 | 1000 |
| M8ms, M8s | 1 | 500 |

Virtuális gépenként vannak az IOPS-korlátok és *nem* lemezenként. Az összes Írásgyorsító lemezek ossza meg a virtuális géphez ugyanazon IOPS-korlátját.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Írásgyorsító engedélyezése egy adott lemezen

A következő néhány szakaszban leírja, hogyan Írásgyorsító Azure Premium Storage virtuális merevlemezeken is engedélyezhető.

### <a name="prerequisites"></a>Előfeltételek

Az alábbi előfeltételek ezen a ponton a időben történő alkalmazása Írásgyorsító használatát:

- Azure Write Accelerator ellen a alkalmazni kívánt lemezeket kell [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) a Premium Storage.
- Az M-sorozatú virtuális Gépet kell használnia

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Azure PowerShell-lel az Azure Write Accelerator engedélyezése

Az Azure PowerShell modul 5.5.0-s a változásokat a kapcsolódó parancsmagok engedélyezheti vagy tilthatja le az adott Azure Premium Storage-lemez Write Accelerator.
Annak érdekében, hogy engedélyezi vagy támogatja Írásgyorsító lemezek üzembe helyezése, a következő PowerShell-parancsok módosítva, és fogadja el a paramétert a Írásgyorsító terjeszteni.

Új kapcsolóparaméter **- WriteAccelerator** hozzá van adva a következő parancsmagokat:

- [Set-AzureRmVMOsDisk](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-6.0.0)
- [Add-AzureRmVMDataDisk](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Add-AzureRmVMDataDisk?view=azurermps-6.0.0)
- [Set-AzureRmVMDataDisk](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Set-AzureRmVMDataDisk?view=azurermps-6.0.0)
- [Add-AzureRmVmssDataDisk](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Add-AzureRmVmssDataDisk?view=azurermps-6.0.0)

A paraméter nincs megadva tulajdonságát hamis értékre állítja be, és nem támogató által Írásgyorsító lemezek fog üzembe helyezni.

Új kapcsolóparaméter **- OsDiskWriteAccelerator** hozzá lett adva a következő parancsmagokat:

- [Set-AzureRmVmssStorageProfile](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile?view=azurermps-6.0.0)

Nem adja meg a paraméter tulajdonságot állítja be a hamis értéket visszaadó, nem az Írásgyorsító lemezek alapértelmezés szerint.

Új választható logikai (nem nullázható) paramétert, **- OsDiskWriteAccelerator** hozzá lett adva a következő parancsmagokat:

- [Update-azurermvm parancsmaghoz](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Update-AzureRmVM?view=azurermps-6.0.0)
- [Update-AzureRmVmss](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Update-AzureRmVmss?view=azurermps-6.0.0)

Adja meg a $true vagy $false szabályozhatja az Azure Írásgyorsító lemezek által támogatott.

A parancsok példák nézhet:

```PowerShell
New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false
```

Az alábbiakban látható módon két fő forgatókönyv parancsfájlalapú lehet.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>PowerShell-lel Írásgyorsító által támogatott új lemez hozzáadása

Ez a szkript használatával adjon hozzá egy új lemezt a virtuális gép. Az ezzel a parancsprogrammal létrehozva lemez Írásgyorsító használja.

Cserélje le `myVM`, `myWAVMs`, `log001`, a lemez és LunID az adott központi telepítés számára megfelelő értékekkel a lemez méretét.

```PowerShell
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
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Írásgyorsító engedélyezése a lemezen egy meglévő Azure PowerShell-lel

Ez a szkript használatával a meglévő lemez Írásgyorsító engedélyezése. Cserélje le `myVM`, `myWAVMs`, és `test-log001` az adott központi telepítés számára megfelelő értékekkel. A parancsfájl hozzáad Írásgyorsító egy meglévő lemezhez ahol értéke **$newstatus** "$true" értékre van állítva. Az érték "$false" használatával adott lemez Írásgyorsító letiltja.

```PowerShell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Hajtsa végre a fenti parancsprogramot fog a megadott lemez leválasztása Írásgyorsító engedélyezése a lemezt érintő és majd csatlakoztassa újra a lemezt

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Az Azure portal használatával Írásgyorsító engedélyezése

A portálon, ahol meg kell adnia a lemezt gyorsítótárazási beállítások Írásgyorsító engedélyezheti:

![Az Azure Portalon írásgyorsító](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Az Azure CLI-vel Írásgyorsító engedélyezése

Használhatja a [Azure CLI-vel](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) Írásgyorsító engedélyezése.

Meglévő lemez Írásgyorsító engedélyezéséhez használja [az vm update](https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest#az-vm-update), használhatja az alábbi példák, ha a diskName VMName és ResourceGroup lecseréli a saját értékeire: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Lemez csatolása írásgyorsítót engedélyezve a használati [az vm disk attach](https://docs.microsoft.com/en-us/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), ha Ön a saját értékeit helyettesítse be a következő példában használhatja: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Írásgyorsító letiltásához használja [az vm update](https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest#az-vm-update), a tulajdonságok beállítása false értékre: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Rest API-k használatával Írásgyorsító engedélyezése

Azure Rest API-n keresztüli üzembe helyezéséhez telepíteni szeretné az Azure armclient.

### <a name="install-armclient"></a>Armclient telepítése

Armclient futtatásához szüksége telepítheti át a chocolatey-t. Telepítheti a cmd.exe vagy a powershellen keresztül. Használja az emelt szintű jogosultságokkal rendelkeznek a parancsokhoz ("Futtatás rendszergazdaként").

A cmd.exe használja, futtassa a következő parancsot: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

A Power Shell használata esetén futtassa a következő parancsot: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Most már telepítheti a armclient a következő parancsot a cmd.exe vagy a PowerShell használatával `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Az aktuális virtuális gép konfigurációjának beolvasása

Ha módosítani szeretné a lemezkonfigurációt attribútumait, először a jelenlegi konfiguráció beolvasása a JSON-fájlt. A jelenlegi konfiguráció kaphat a következő parancs végrehajtásával: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

A használati feltételeket <> <>cserélje le az adatokat, köztük a fájl nevét, a JSON-fájlt kell rendelkeznie.

A kimenet módon nézhet:

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

Ezután frissítse a JSON-fájlt, és a "log1" nevű lemez Írásgyorsító engedélyezése. Ez az attribútum való felvételével a JSON-fájlt a lemez gyorsítótár-bejegyzés után is elvégezhető.

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

Ezután frissítse a meglévő üzemelő példányt a következő paranccsal: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

A kimenet az alábbihoz hasonlóan kell kinéznie. Láthatja, hogy Írásgyorsítót használó lemez egy.

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

Miután végrehajtotta ezt a módosítást, a meghajtó Írásgyorsító támogatnia kell.
