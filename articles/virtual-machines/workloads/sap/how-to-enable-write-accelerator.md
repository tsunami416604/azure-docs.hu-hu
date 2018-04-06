---
title: SAP központi telepítések Azure írási gyorsító |} Microsoft Docs
description: Üzemeltetési útmutató a SAP HANA-rendszerek üzembe helyezett Azure virtuális gépeken.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 177bc05eea3aa05231c71a42950fa622b68afc53
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Az Azure írási gyorsító SAP telepítésekhez
Azure írási gyorsító az első megkezdődött M sorozatú virtuális gépek kizárólag funkcionalitást. Az Azure-írási gyorsító adatsorozattal bármely más Virtuálisgép-Azure, kivéve az M-sorozat nem érhető el. Állapota a neve, mint a funkció célja javítása a prémium szintű Azure Storage elleni írások késését i/o. 

>[!NOTE]
> Ezen a ponton az Azure-írási gyorsító nyilvános előzetes verziójában, és megköveteli az Azure-előfizetése Azonosítóját fehér listája

Az Azure írási gyorsító funkciók M-sorozat telepítési, mint a nyilvános előzetes verzió érhető el:

- Nyugat-US2
- Nyugat-Európa
- Délkelet-Ázsia

## <a name="planning-for-using-azure-write-accelerator"></a>Azure írási gyorsító használatának megtervezése
A kötetek, a tranzakciós napló tartalmaz, vagy rögzít egy adatbázis-kezelő visszaállítása Azure írási gyorsító használható. Egy adatbázis-kezelő adatkötetek Azure írási gyorsító használandó nem ajánlott. E korlátozás szükség, hogy Azure írási gyorsító igényel-e az Azure Premium Storage VHD-k csatlakoztatni kell a további olvasási gyorsítótárazása nélkül, amelyet a prémium szintű Storage. Az ilyen típusú gyorsítótár nagyobb előnyeit hagyományos adatbázisok figyelhető. Írási gyorsító csak írási tevékenységek érinti, és nem felgyorsíthatja az olvasási, a támogatott kialakítása SAP gyorsító írni a tranzakciós napló ellen, vagy napló meghajtók támogatott SAP-adatbázisok visszaállítása. 

Azure írási gyorsító csak működik együtt [Azure által kezelt lemezeken](https://azure.microsoft.com/services/managed-disks/). Ezért kell ennek megfelelően tervezheti. 

>[!NOTE]
> Mivel Azure írási gyorsító funkcióinak még nyilvános előzetes, nem éles forgatókönyvben szereplő telepítések támogatottak a funkció még.

Nincsenek Azure prémium szintű Storage a VHD-k, amely támogatja-e írási gyorsító Azure virtuális gépenként korlátozások. A jelenlegi korlátozások a következők:

- Egy M128xx 16 virtuális merevlemezek méretű VM
- Egy M64xx 8 virtuális merevlemezek méretű VM

> [!IMPORTANT]
> Ha szeretné engedélyezni vagy letiltani egy létező kötet, amely több prémium szintű Azure Storage-lemez ki van építve, és a Windows lemezre vagy kötetre-kezelők használatával csíkozott Azure írási gyorsító, Windows tárhelyeken, a Windows a kibővített fájlkiszolgáló (sofs-sel) Linux LVM vagy MDADM, a kötet felépítése az összes lemez kell lennie engedélyezésekor vagy letiltásakor gyorsító írni a különálló lépések. **Engedélyezése vagy letiltása írási gyorsító ilyen konfiguráció, előtt állítsa le az Azure virtuális gép**. 


> [!IMPORTANT]
> Azure írási gyorsító egy meglévő Azure lemezre, amely nem része egy kötet build kívül több lemez is van a Windows-lemez vagy kötet kezelők, a tárolóhelyek a Windows, Windows kibővített fájlkiszolgálóként (SOFS), Linux LVM vagy MDADM, hogy a munkaterhelés fér hozzá a Az Azure lemez kell leállítani. Adatbázis-alkalmazások lemezt az Azure használatával le kell állítani.

> [!IMPORTANT]
> Az Azure operációsrendszer-lemez a virtuális gép írási gyorsító engedélyezése a virtuális gép újraindul. 

A lemezek működő nem lehet szükség az SAP, amely lehetővé teszi, hogy Azure írási gyorsító kapcsolatos Virtuálisgép-konfigurációk

### <a name="restrictions-when-using-azure-write-accelerator"></a>Azure írási gyorsító használatakor korlátozások
Ha Azure írási gyorsító egy Azure lemez/VHD-t használ, ezek a korlátozások érvényesek:

- A prémium szintű lemez gyorsítótárazás kell "Nincs" értékre állítani. Minden más gyorsítótárazási mód nem támogatott.
- Az engedélyezett írási gyorsító lemezen pillanatkép még nem támogatott. Ez a korlátozás tiltása, hajtsa végre a virtuális gép összes lemeze konzisztens alkalmazás pillanatképe egy Azure biztonsági mentés szolgáltatás képes-e.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Adott lemezre írás gyorsító engedélyezése
A következő néhány szakasz ismerteti, hogyan engedélyezhető Azure írási gyorsító Azure Premium Storage virtuális merevlemezeket.

Ezen a ponton az időben csak írási gyorsító Azure Rest API és PowerShell engedélyezése. Más módszerekkel legfeljebb támogatása az Azure portál során a következő néhány hét követi.

### <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek ezen a ponton a időben történő alkalmazása az Azure írási gyorsító használatát:

- Az előfizetés-Azonosítóval, amely szerepel a virtuális Gépet a virtuális gép és tároló telepíteni kell lennie a fehér felsorolt. Lépjen kapcsolatba a Microsoft CSA GBB, vagy fiókkezelő beolvasni az előfizetés-azonosító üres felsorolt. 
- Azure írási gyorsító elleni alkalmazni kívánt lemezeket kell lenniük [Azure által kezelt lemezeken](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>A Power Shell engedélyezése
Az Azure PowerShell modul 5.5.0 verziójáról a változásokat a vonatkozó parancsmagjainak engedélyezheti vagy tilthatja le az Azure írási gyorsító adott prémium szintű Azure Storage-lemezek.
Ahhoz, hogy engedélyezi vagy lemezek írási gyorsító által támogatott központi telepítése, a következő PowerShell-parancsok megváltozott, és terjeszteni a írási gyorsító fogad el paramétert.

Egy új kapcsolóparaméter "WriteAccelerator" készült fel a következő parancsmagokat: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

A paraméter nincs megadva a tulajdonság beállítása FALSE, és telepíti a lemezek, amelyek Azure írási gyorsító által nem támogatott.

Egy új kapcsolóparaméter "OsDiskWriteAccelerator" hozzá lett adva a következő parancsmagokat: 

- Set-AzureRmVmssStorageProfile

Nem a készletek a tulajdonság hamis és ad hasznosítja Azure írási gyorsító lemezek fog továbbítani.

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

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Azure írási gyorsító által támogatott új lemez hozzáadása
Ez a parancsfájl segítségével adjon hozzá egy új lemezt a virtuális géphez. Ezzel a parancsprogrammal létrehozott lemez üzemeltetéséhez kívánja használni az Azure írási gyorsító.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
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
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Szüksége való igazításának lehetősége a virtuális gép, a lemez és az erőforráscsoport nevét. A fenti parancsfájl hozzáadása egy meglévő lemezre írás gyorsító a $newstatus értéke "$true". "$False" érték letiltja a gyorsító írni a adott lemez.

> [!Note]
> A fenti parancsprogram végrehajtása a megadott lemez leválasztása, engedélyezi gyorsító írni a lemezen, és majd csatlakoztassa újra a lemezt




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

Következő lépés a JSON-fájlt, és írási gyorsító engedélyezéséhez kattintson a lemez "log1" néven. Ezt megteheti ezt az attribútumot a JSON-fájlba a gyorsítótári bejegyzés, a lemez után adja hozzá. 

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

 