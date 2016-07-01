<properties
   pageTitle="Linux virtuális gép létrehozása az Azure-ban a parancssori felülettel | Microsoft Azure"
   description="Létrehozhat Linux virtuális gépet az Azure-ban a parancssori felület használatával."
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
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# Linux virtuális gép létrehozása az Azure-ban a parancssori felülettel

Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépet az Azure-ban az Azure parancssori felület `azure vm quick-create` parancsának használatával. A `quick-create` parancs alapvető infrastruktúrával körülvett virtuális gépet hoz létre, amelyet prototípusként vagy egy elgondolás gyors teszteléséhez használhat (például így veheti a leggyorsabban használatba a Linux bash rendszerhéjat).  A cikkben foglaltak végrehajtásához Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)) van szükség, és be kell jelentkezni [az Azure parancssori felületre](../xplat-cli-install.md) (`azure login`) erőforrás-kezelő módban (`azure config mode arm`).  Linux virtuális gépet az [Azure portállal](virtual-machines-linux-quick-create-portal.md) is gyorsan üzembe helyezhet.

## Gyors parancsösszegzés

Egy parancs szolgál a CoreOS virtuális gép üzembe helyezésére és az SSH-kulcs csatolására:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## A Linux virtuális gép üzembe helyezése

Az alábbiak a fenti parancs egyes promptjai láthatók a várható kimenetekkel együtt, azonban az RHEL aliast használva egy RedHat Enteprise Linux 7.2 rendszerű virtuális gép létrehozásához.  

## ImageURN alias használata

Az Azure parancssori felület `quick-create` parancsában aliasok vannak leképezve az operációs rendszerek leggyakoribb disztribúciói számára. Az alábbi táblázat a disztribúciók aliasát tartalmazza (az Azure parancssori felület 0.10-es verziójától kezdve).  A `quick-create` parancsot használó összes üzemelő példány alapértelmezés szerint SSD-tárterülettel rendelkező virtuális gépeket hoz létre, amelyek kiváló teljesítményt nyújtanak.

| Alias     | Közzétevő | Ajánlat        | SKU         | Verzió |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | legújabb  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | legújabb  |
| Debian    | credativ  | Debian       | 8           | legújabb  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | legújabb  |
| RHEL      | Redhat    | RHEL         | 7.2         | legújabb  |
| SLES      | SLES      | SLES         | 12-SP1      | legújabb  |
| UbuntuLTS | Canonical | UbuntuServer | 14.04.4-LTS | legújabb  |



Az **ImageURN** kapcsoló esetén (`-Q`) az `RHEL` segítségével helyezünk üzembe egy RedHat Enterprise Linux 7.2 rendszerű virtuális gépet. (Ez a 7 alias az Azure-ban rendelkezésre álló operációs rendszerek kis részét képviseli, a piacon elérhető további rendszerképeket [a rendszerképek keresésével](virtual-machines-linux-cli-ps-findimage.md) találhatja meg, vagy [feltöltheti saját egyéni rendszerképét](virtual-machines-linux-create-upload-generic.md).)

Az alábbiakban bemutatott parancsban cserélje le a promptokat a saját környezete értékeivel, mi „példa” értékeket használunk.  

Kövesse a promptokat, és adja meg a saját neveket

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

A kimenetnek az alábbi kimeneti blokkhoz hasonlóan kell kinéznie.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Mostantól használhatja az SSH-t a virtuális gépre való bejelentkezéshez az alapértelmezett 22-es SSH-portot és a fenti kimenetben szereplő teljes tartománynevet használva. (A listában szereplő IP-címet is használhatja.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
A bejelentkezési folyamatnak az alábbiakhoz hasonlóan kell kinéznie:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## További lépések

Az `azure vm quick-create` a virtuális gépek gyors üzembe helyezésére szolgál, hogy bejelentkezhessen a rendszerhéjakba és elkezdhesse a munkát. A `vm quick-create` használata nem nyújtja az összetett környezetek egyéb előnyeit.  Az infrastruktúrának megfelelően beállított Linux virtuális gép üzembe helyezéshez kattintson az alábbi cikkek valamelyikére.

- [Adott üzembe helyezés létrehozása Azure Resource Manager-sablonnal](virtual-machines-linux-cli-deploy-templates.md)
- [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](virtual-machines-linux-create-cli-complete.md).
- [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Ezek a cikkek segítséget nyújtanak az Azure-infrastruktúrák, valamint tetszőleges számú jogvédett vagy nyílt forráskódú infrastruktúra üzembe helyezési, konfigurálási és vezénylési létrehozásához.



<!--HONumber=Jun16_HO2-->


