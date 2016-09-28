<properties
   pageTitle="Linux virtuális gép létrehozása az Azure-ban a parancssori felülettel | Microsoft Azure"
   description="Linux virtuális gép létrehozása az Azure-ban a parancssori felülettel."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/08/2016"
   ms.author="v-livech"/>



# Linux virtuális gép létrehozása az Azure-ban a parancssori felülettel

Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépet (VM) az Azure-ban az Azure parancssori felület (CLI) `azure vm quick-create` parancsának használatával. A `quick-create` parancs egy alapvető, biztonságos infrastruktúrában lévő virtuális gépet helyez üzembe, amelyet prototípusként vagy egy elgondolás gyors teszteléséhez használhat. A cikkben foglaltak végrehajtásához

- Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)) van szükség

- és be kell jelentkeznie az [Azure parancssori felületre](../xplat-cli-install.md) a következővel: `azure login`.

- Az Azure parancssori felületnek `azure config mode arm` Azure Resource Manager módban _kell lennie_.  

Linux virtuális gépet az [Azure Portallal](virtual-machines-linux-quick-create-portal.md) is gyorsan üzembe helyezhet.

## Gyors parancsok

Az alábbi példa azt szemlélteti, hogyan helyezheti üzembe a CoreOS virtuális gépet, és hogyan csatolhatja a Secure Shell-kulcsot (SSH) (az argumentumok eltérhetnek).

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

Az alábbi szakaszok a parancsot és annak követelményeit ismertetik az Ubuntu Server 14.04 LTS Linux-disztribúció használatával.  

## Virtuális gép – gyorslétrehozási aliasok

A disztribúcióválasztás egyszerű módja az operációs rendszerek leggyakoribb disztribúciói számára leképezett Azure parancssori felület aliasainak a használata. Az alábbi táblázat az aliasokat tartalmazza (az Azure parancssori felület 0.10-es verziójától kezdve). A `quick-create`parancsot használó minden üzembe helyezés SSD-tárhelyen alapuló virtuális gépeket használ alapértelmezésként, amelyek gyorsabb üzembe helyezést és magas teljesítményű lemezelérést tesznek lehetővé. (Ezek az aliasok az Azure-ban rendelkezésre álló disztribúcióknak csak egy kis részét jelentik. Az Azure Marketplace-en elérhető további rendszerképeket [a rendszerképek keresésével](virtual-machines-linux-cli-ps-findimage.md) találhatja meg, vagy [feltöltheti saját egyéni rendszerképét](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Közzétevő | Ajánlat        | SKU         | Verzió |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | legújabb  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | legújabb  |
| Debian    | credativ  | Debian       | 8           | legújabb  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | legújabb  |
| RHEL      | Red Hat    | RHEL         | 7.2         | legújabb  |
| UbuntuLTS | Canonical | Ubuntu Server | 14.04.4-LTS | legújabb  |

Az alábbi szakaszok az **ImageURN** kapcsoló (`-Q`) `UbuntuLTS` aliasát használják az Ubuntu 14.04.4 LTS Server üzembe helyezéséhez.

## Részletes bemutató

A korábbi `quick-create` példa csak a `-M` jelölőt emelte ki a feltöltendő nyilvános SSH-kulcs azonosításához, és közben letiltotta az SSH-jelszókat, így a rendszer a következő adatok megadását kéri:

- az erőforráscsoport neve (az első Azure-erőforráscsoport esetén általában bármilyen karakterlánc használható)
- a virtuális gép neve
- hely (az USA nyugati régiója és a Nyugat-Európa megfelelő alapértelmezett értékek)
- Linux (hogy az Azure tudja, melyik operációs rendszert kívánja használni)
- felhasználónév

Az alábbiak meghatározzák az összes értéket, így nincs szükség további adatkérésre. Ha `~/.ssh/id_rsa.pub` ssh-rsa formátumú nyilvános kulcsfájllal rendelkezik, nincs szükség beavatkozásra.

```bash
azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub \
-Q UbuntuLTS
```

A kimenetnek az alábbi kimeneti blokkhoz hasonlóan kell kinéznie.

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "exampleVMName"
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
+ Creating VM "exampleVMName"
+ Looking up the VM "exampleVMName"
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
data:      Computer Name                 :exampleVMName
data:      User Name                     :exampleAdminUser
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

Jelentkezzen be a virtuális gépre a kimenetben szereplő nyilvános IP-cím használatával. A listában szereplő teljes tartománynevet (FQDN) is használhatja.

```bash
ssh -i ~/.ssh/id_rsa.pub exampleAdminUser@138.91.247.29
```

A bejelentkezési folyamatnak az alábbiakhoz hasonlóan kell kinéznie:

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

exampleAdminUser@exampleVMName:~$
```

## Következő lépések

Az `azure vm quick-create` parancs a virtuális gépek gyors üzembe helyezésére szolgál, hogy bejelentkezhessen a rendszerhéjakba, és elkezdhesse a munkát. A `vm quick-create` használata azonban nem biztosít széles körű vezérlést, illetve összetettebb környezet létrehozását sem teszi lehetővé.  Az infrastruktúrának megfelelően beállított Linux virtuális gép üzembe helyezéséhez kattintson az alábbi cikkek valamelyikére:

- [Adott üzembe helyezés létrehozása Azure Resource Manager-sablonnal](virtual-machines-linux-cli-deploy-templates.md)
- [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](virtual-machines-linux-create-cli-complete.md)
- [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

[Használhatja a `docker-machine` Azure-illesztőt is különféle parancsokkal egy Linux virtuális gép Docker-gazdagépként való gyors létrehozásához](virtual-machines-linux-docker-machine.md).



<!--HONumber=Sep16_HO4-->


