---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 5/9/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 4db9fe907ab6625fcad74ceae59f17115458a3ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="write-accelerator"></a>Gyorsító írása
Az írási gyorsító egy lemez képesség az M sorozatú virtuális gépek (VM) a prémium szintű Storage Azure felügyelt lemezzel kizárólag. Állapota a neve, mint a funkció célja a prémium szintű Azure Storage elleni írások késleltetéséről javítása érdekében. Az írási gyorsító akkor ajánlott, ahol napló fájl frissítések magas performant módon modern adatbázisok lemezre megőrzéséhez szükséges.

Az írási gyorsító általánosan elérhető az M sorozatú virtuális gépek a nyilvános felhőben.

## <a name="planning-for-using-write-accelerator"></a>Írási gyorsító használatának megtervezése
Az írási gyorsító használja a rendszer a kötetek, a tranzakciós napló tartalmaz, vagy visszaállítása egy adatbázis-kezelő rögzít. Nem ajánlott használandó gyorsító írni egy adatbázis-kezelő adatok mennyiségének, a szolgáltatás használható szemben napló lemezek lett optimalizálva.

Írás a gyorsító csak működik együtt [Azure által kezelt lemezeken](https://azure.microsoft.com/services/managed-disks/). 


> [!IMPORTANT]
> Ha szeretné engedélyezni vagy letiltani egy létező kötet, amely több prémium szintű Azure Storage-lemez ki van építve, és a Windows lemezre vagy kötetre-kezelők használatával csíkozott írási gyorsító, Windows tárhelyeken, a Windows a kibővített fájlkiszolgáló (sofs-sel) Linux LVM vagy MDADM, minden a kötet felépítése lemezek kell kell engedélyezésekor vagy letiltásakor a írási gyorsító külön lépéseket. **Engedélyezése vagy letiltása írási gyorsító ilyen konfiguráció, előtt állítsa le az Azure virtuális gép**. 


> [!IMPORTANT]
> Egy meglévő Azure lemezre, amely nem része egy kötet build kívül több lemezre, lemezre vagy kötetre kezelők, a Windows tárhelyeken, a Windows kibővített fájlkiszolgálóként (SOFS), Linux LVM, vagy a MDADM, a munkaterhelés fér hozzá a lemezt az Azure Windows írási gyorsító engedélyezése meg kell leállítani. Adatbázis-alkalmazások lemezt az Azure használatával le kell állítani.

> [!IMPORTANT]
> Írási gyorsító az az operációsrendszer-lemez a virtuális gép engedélyezése a virtuális gép újraindul. 

Engedélyezze az operációs rendszer lemezek nem kell az SAP írási gyorsító kapcsolatos Virtuálisgép-konfigurációk

### <a name="restrictions-when-using-write-accelerator"></a>Írási gyorsító használatakor korlátozások
Ha írási gyorsító egy Azure lemez/VHD-t használ, ezek a korlátozások érvényesek:

- A prémium szintű lemez gyorsítótárazás kell állítani a "None" vagy "Csak Olvasás". Minden más gyorsítótárazási mód nem támogatott.
- Az engedélyezett írási gyorsító lemezen pillanatkép még nem támogatott. Ez a korlátozás tiltása, hajtsa végre a virtuális gép összes lemeze konzisztens alkalmazás pillanatképe egy Azure biztonsági mentés szolgáltatás képes-e.
- Csak kisebb i/o-méretű (< = 32KiB) végzése a gyorsított elérési útja. Az alkalmazások és szolgáltatások olyan helyzetekben, ahol adatokat kezd tömeges betöltve, vagy ha a tranzakciós napló pufferek, a másik adatbázis-kezelő van kitöltve nagyobb mértékben előtt első maradnak meg a tárolási, valószínűleg, amely a írt i/o lemez nem tart a gyorsított elérési útja.

Nincsenek Azure prémium szintű Storage a VHD-k egy virtuális Gépet, amely támogatja-e írási gyorsító korlátozások. A jelenlegi korlátozások a következők:

| VIRTUÁLIS GÉP TERMÉKVÁLTOZAT | Írási gyorsító lemezek számát | Írható gyorsító lemez IOPS virtuális gépenként |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Adott lemezre írás gyorsító engedélyezése
A következő néhány szakasz ismerteti, hogyan engedélyezhető írási gyorsító Azure Premium Storage virtuális merevlemezeket.


### <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek ezen a ponton a időben történő alkalmazása az írási gyorsító használatát:

- Azure írási gyorsító elleni alkalmazni kívánt lemezeket kell lenniük [Azure által kezelt lemezeken](https://azure.microsoft.com/services/managed-disks/) a prémium szintű Storage.
- Használjon egy M sorozatú virtuális Gépre

### <a name="enabling-through-power-shell"></a>A Power Shell engedélyezése
Az Azure PowerShell modul 5.5.0 verziójáról a változásokat a vonatkozó parancsmagjainak engedélyezhető vagy tiltható le meghatározott prémium szintű Azure Storage-lemezek írási gyorsító.
Ahhoz, hogy engedélyezi vagy lemezek írási gyorsító által támogatott központi telepítése, a következő PowerShell-parancsok megváltozott, és terjeszteni a írási gyorsító fogad el paramétert.

Egy új kapcsolóparaméter "WriteAccelerator" készült fel a következő parancsmagokat: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

A paraméter nincs megadva a tulajdonság beállítása FALSE, és telepíti a lemezek, amelyek írási gyorsító által nem támogatott.

Egy új kapcsolóparaméter "OsDiskWriteAccelerator" hozzá lett adva a következő parancsmagokat: 

- Set-AzureRmVmssStorageProfile

Nincs megadva a paramenter tulajdonságát hamis értékre állítja be, és lemezek használó nem írható gyorsító fog továbbítani.

Egy új választható logikai (nem nullázható) paraméter, "OsDiskWriteAccelerator" készült fel a következő parancsmagokat: 

- Update-AzureRmVM
- Update-AzureRmVmss

Adja meg a $true vagy $false vezérlésére az Azure írási gyorsító támogatásához a lemezeket.

A parancsok példák nézhet:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Az alábbi szakaszokban ismertetett módon kétféle módon történhet parancsfájlalapú lehet.

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>Írási gyorsító által támogatott új lemez hozzáadása
Ez a parancsfájl segítségével adjon hozzá egy új lemezt a virtuális géphez. Ezzel a parancsprogrammal létrehozott lemez üzemeltetéséhez kívánja használni a gyorsító írni.

```

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
A virtuális gép, a lemez, az erőforráscsoport, a lemez mérete és a lemez számára az adott központi telepítés LunID nevek igazítja kell.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Meglévő Azure lemezen Azure írási gyorsító engedélyezése
Engedélyezze a gyorsító írni a meglévő lemez van szüksége, ha a a feladat végrehajtásához használhatja ezt a parancsfájlt:

```

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

Szüksége való igazításának lehetősége a virtuális gép, a lemez és az erőforráscsoport nevét. A fenti parancsfájl hozzáadása egy meglévő lemezt, ahol a $newstatus értéke "$true" gyorsító írni. "$False" érték letiltja a gyorsító írni a adott lemez.

> [!Note]
> A fenti parancsprogram végrehajtása a megadott lemez leválasztása, engedélyezi gyorsító írni a lemezen, és majd csatlakoztassa újra a lemezt

### <a name="enabling-through-azure-portal"></a>Az Azure portálon keresztül engedélyezése

A portálon, amelyben meg kell határoznia a gyorsítótárazási beállítások lemez írási gyorsító engedélyezéséhez: 

![Az Azure portálon gyorsító írása](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

### <a name="enabling-through-azure-cli"></a>Az Azure parancssori felületen keresztül engedélyezése
Használhatja a [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) írási gyorsító engedélyezéséhez. 

Meglévő lemez írási gyorsító engedélyezéséhez használja a parancsot, és a diskName VMName és ResourceGroup saját: 
```
az vm update -g group1 -n vm1 –write-accelerator 1=true
```
Egy lemezt csatolni a írási gyorsító engedélyezve adja használja az alábbi parancsot a értékekkel:
```
az vm disk attach -g group1 –vm-name vm1 –disk d1 --enable-write-accelerator
```
Írási gyorsító letiltásához állítsa be a tulajdonságot false értékre: 
```
az vm update -g group1 -n vm1 –write-accelerator 0=false 1=false
```

### <a name="enabling-through-rest-apis"></a>A Rest API-k engedélyezése
Azure Rest API-n keresztül telepítéséhez telepítenie kell az Azure armclient

#### <a name="install-armclient"></a>Armclient telepítése

Armclient futtatásához Chocolatey keresztül telepíteni kell. Telepítheti a cmd.exe vagy a powershellen keresztül. Emelt szintű jogosultságok használata parancsok ("Futtatás rendszergazdaként").

A következő parancsot a cmd.exe használatával:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

PowerShell használatával kell használni:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Most már telepítheti a armclient alatt a cmd.exe vagy a PowerShell paranccsal

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Az aktuális Virtuálisgép-konfiguráció beolvasása
Ahhoz, hogy módosítsa a lemezkonfigurációt attribútumait, először a jelenlegi konfiguráció beolvasása a JSON-fájlt. A jelenlegi konfiguráció kérhető le a következő paranccsal:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Cserélje le a "<> <>" kifejezések az adatait, beleértve a fájlnevet a JSON-fájlt kell rendelkeznie.

A kimeneti nézhet:

```
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

Következő lépés a JSON-fájlt, és írási gyorsító engedélyezéséhez kattintson a lemez "log1" néven. Ebben a lépésben adja hozzá ezt az attribútumot a JSON-fájlba a gyorsítótári bejegyzés, a lemez után is elvégezhető. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Módosítsa a meglévő telepítés ezzel a paranccsal:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

A kimenet az alábbihoz kell hasonlítania. Láthatja, hogy van-e írási gyorsító lemezenként engedélyezve van.

```
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
          **"writeAcceleratorEnabled": true,**
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

A módosítás a pont a meghajtó támogatnia kell írási gyorsító.

 
