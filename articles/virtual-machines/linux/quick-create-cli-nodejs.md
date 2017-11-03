---
title: "Linux-alapú virtuális gép létrehozása az Azure CLI 1.0-val | Microsoft Docs"
description: "Linux-alapú virtuális gép létrehozása az Azure CLI 1.0-val"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: 71bb55ab4800389c956cebd00eb3e6e506610153
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-linux-vm-using-the-azure-cli-10"></a>Linux virtuális gép létrehozása az Azure.CLI 1.0-val

Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépet (VM) az Azure-ban az Azure parancssori felület (CLI) `azure vm quick-create` parancsának használatával. A `quick-create` parancs egy alapvető, biztonságos infrastruktúrában lévő virtuális gépet helyez üzembe, amelyet prototípusként vagy egy elgondolás gyors teszteléséhez használhat.

> [!NOTE]
Ha az Azure CLI 2.0-s verziójával szeretne virtuális gépet létrehozni, tekintse meg a [Virtuális gép létrehozása az Azure parancssori felülettel](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című cikket.

Linux virtuális gépet az [Azure Portallal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is gyorsan üzembe helyezhet.

A cikk szükséges egy [SSH nyilvános és titkos kulcs fájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Gyors parancsok

Az alábbi példa azt szemlélteti, hogyan helyezheti üzembe a CoreOS virtuális gépet, és hogyan csatolhatja a Secure Shell-kulcsot (SSH) (az argumentumok eltérhetnek):

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

A következő bemutató egy UbuntuLTS virtuális gép üzembe helyezését szemlélteti lépésről lépésre, az egyes lépések funkciójának magyarázatával együtt.

## <a name="vm-quick-create-aliases"></a>Virtuális gép – gyorslétrehozási aliasok

A disztribúcióválasztás egyszerű módja az operációs rendszerek leggyakoribb disztribúciói számára leképezett Azure parancssori felület aliasainak a használata. Az alábbi táblázat az aliasokat tartalmazza (az Azure parancssori felület 0.10-es verziójától kezdve). A `quick-create` parancsot használó minden üzembe helyezés SSD-tárhelyen alapuló virtuális gépeket használ alapértelmezettként, ami gyorsabb üzembe helyezést és magas teljesítményű lemezelérést tesz lehetővé. (Ezek az aliasok az Azure-ban rendelkezésre álló disztribúcióknak csak egy kis részét jelentik. Az Azure Marketplace-en további rendszerképekhez [rendszerképek PowerShellben történő keresésével](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [a weben](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), illetve [saját egyéni rendszerképének feltöltésével](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) juthat.)

| Alias | Közzétevő | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |legújabb |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |
| Debian |credativ |Debian |8 |legújabb |
| openSUSE |SUSE |openSUSE |13.2 |legújabb |
| RHEL |Red Hat |RHEL |7.2 |legújabb |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |legújabb |

Az alábbi szakaszok az **ImageURN** kapcsoló (`-Q`) `UbuntuLTS` aliasát használják az Ubuntu 14.04.4 LTS Server üzembe helyezéséhez.

A korábbi `quick-create` példa csak az `-M` jelölőt emelte ki a feltöltendő nyilvános SSH-kulcs azonosításához, és közben letiltotta az SSH-jelszavakat, így a rendszer a következő argumentumok megadását kéri:

* az erőforráscsoport neve (az első Azure-erőforráscsoport esetén általában bármilyen karakterlánc használható)
* a virtuális gép neve
* hely (`westus` vagy `westeurope` jó alapértelmezett értékek)
* Linux (hogy az Azure tudja, melyik operációs rendszert kívánja használni)
* felhasználónév

Az alábbi példa meghatározza az összes értéket, így nincs szükség további adatkérésre. Ha `~/.ssh/id_rsa.pub` ssh-rsa formátumú nyilvános kulcsfájllal rendelkezik, nincs szükség beavatkozásra:

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

A kimenetnek az alábbi kimeneti blokkhoz hasonlóan kell kinéznie:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Jelentkezzen be az új virtuális gépre
Jelentkezzen be a virtuális gépre a kimenetben szereplő nyilvános IP-cím használatával. A listában szereplő teljes tartománynevet (FQDN) is használhatja:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

A bejelentkezési folyamatnak az alábbi kimeneti blokkhoz hasonlóan kell kinéznie:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Következő lépések
Az `azure vm quick-create` parancs a virtuális gépek gyors üzembe helyezésére szolgál, hogy bejelentkezhessen a rendszerhéjakba, és elkezdhesse a munkát. A `vm quick-create` használata azonban nem biztosít széles körű vezérlést, illetve összetettebb környezet létrehozását sem teszi lehetővé.  Az infrastruktúrának megfelelően beállított Linux virtuális gép üzembe helyezéséhez kattintson az alábbi cikkek valamelyikére:

* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Használhatja a `docker-machine` Azure-illesztőt is különféle parancsokkal egy Linux virtuális gép Docker-gazdagépként való gyors létrehozásához](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
