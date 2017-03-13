---
title: "Linux-alapú virtuális gép létrehozása az Azure CLI 2.0-s verziójával | Microsoft Azure"
description: "Linux virtuális gép létrehozása az Azure CLI 2.0-val"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Linux virtuális gép létrehozása az Azure CLI 2.0-val
Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux-alapú virtuális gépet az Azure-ban az Azure CLI 2.0-s verziójának [az vm create](/cli/azure/vm#create) parancsával, akár felügyelt lemezek, akár natív tárfiókban lévő lemezek használatával. Az [Azure CLI 1.0-s](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket.

Virtuális gép létrehozásához: 

* Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)) van szükség
* telepítenie kell az [Azure CLI 2.0-s](/cli/azure/install-az-cli2) verzióját
* be kell jelentkeznie az Azure-fiókjába (írja be a következőt: [az login](/cli/azure/#login))

(A Linux-alapú virtuális gépeket az [Azure Portallal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is üzembe helyezheti.)

Az alábbi példa azt szemlélteti, hogyan helyezhet üzembe egy Debian rendszerű virtuális gépet, és hogyan csatlakozhat hozzá Secure Shell- (SSH-) kulccsal. Az argumentumok eltérhetnek; ha másik rendszerképet szeretne használni, [kereshet egyet](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Felügyelt lemezek használata

Az Azure által felügyelt lemezek használatához olyan régióban kell lennie, amely támogatja őket. Először írja be az [az group create](/cli/azure/group#create) parancsot az összes telepített erőforrást tartalmazó erőforráscsoport létrehozásához:

```azurecli
 az group create -n myResourceGroup -l westus
```

A parancs kimenete a következőképpen néz ki (ha más formátumot szeretne látni, más `--output` kapcsolót is választhat):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>A virtuális gép létrehozása 
Most mát létrehozhatja a virtuális gépet és annak környezetét. Ne feledjen egyedei értéket megadni a következőhöz: `--public-ip-address-dns-name`; előfordulhat, hogy a jelenlegi érték már foglalt.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


A kimenetnek a következőképpen néz ki. Jegyezze fel a `publicIpAddress` vagy az `fqdn` értékek egyikét a virtuális gépre való bejelentkezéshez **SSH**-val.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Jelentkezzen be a virtuális gépre a kimenetben szereplő nyilvános IP-cím vagy a teljes tartománynév (FQDN) használatával.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

A választott disztribúciótól függően a következő kimenethez hasonlót kell látnia:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

A [További lépések](#next-steps) témakörből megtudhatja, milyen egyéb műveleteket végezhet újonnan létrehozott, felügyelt lemezeket használó virtuális gépével.

## <a name="using-unmanaged-disks"></a>Nem felügyelt lemezek használata 

A nem felügyelt tárolólemezeket használó virtuális gépek nem felügyelt tárfiókkal rendelkeznek. Először írja be az [az group create](/cli/azure/group#create) parancsot az összes telepített erőforrást tartalmazó erőforráscsoport létrehozásához:

```azurecli
az group create --name nativedisks --location westus
```

A parancs kimenete a következőképpen néz ki (ha szeretne, más `--output` beállítást is választhat):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>A virtuális gép létrehozása 

Most mát létrehozhatja a virtuális gépet és annak környezetét. Nem felügyelt lemezekkel rendelkező virtuális gép létrehozásához használja a `--use-unmanaged-disk` jelzőt. Egy nem felügyelt tárfiók is létrejön. Ne feledjen egyedei értéket megadni a következőhöz: `--public-ip-address-dns-name`; előfordulhat, hogy a jelenlegi érték már foglalt.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

A kimenetnek a következőképpen néz ki. Jegyezze fel a `publicIpAddress` vagy az `fqdn` értékek egyikét a virtuális gépre való bejelentkezéshez **SSH**-val.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Jelentkezzen be a virtuális gépre a fentebbi kimenetben szereplő nyilvános IP-cím vagy a teljes tartománynév (FQDN) használatával.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

A választott disztribúciótól függően a következő kimenethez hasonlót kell látnia:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Következő lépések
Az `az vm create` parancs a virtuális gépek gyors üzembe helyezésére szolgál, hogy bejelentkezhessen a rendszerhéjakba, és elkezdhesse a munkát. A `az vm create` használata azonban nem biztosít széles körű vezérlést, illetve összetettebb környezet létrehozását sem teszi lehetővé.  Az infrastruktúrának megfelelően beállított Linux virtuális gép üzembe helyezéséhez kattintson az alábbi cikkek valamelyikére:

* [Adott üzembe helyezés létrehozása Azure Resource Manager-sablonnal](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Használhatja a `docker-machine` Azure-illesztőt is különféle parancsokkal egy Linux virtuális gép Docker-gazdagépként való gyors létrehozásához](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ha pedig Javát használ, próbálkozzon a [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) metódussal.


