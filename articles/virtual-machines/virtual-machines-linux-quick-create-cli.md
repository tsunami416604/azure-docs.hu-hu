---
title: "Linux virtuális gép létrehozása az Azure parancssori felület 2.0 (előzetes) verziójával | Microsoft Azure"
description: "Linux virtuális gép létrehozása az Azure parancssori felület 2.0 (előzetes) verziójával."
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Linux virtuális gép létrehozása az Azure parancssori felület 2.0 (előzetes) verziójával (az.py)
Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépet (VM) az Azure-ban az Azure parancssori felület (CLI) 2.0 (előzetes) verzió [az vm create](/cli/azure/vm#create) parancsának használatával. 

> [!NOTE] 
> Az Azure parancssori felület 2.0 előzetes verzió a többplatformos parancssori felületek következő generációját képviseli. [Próbálja ki!](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> A többi dokumentum a meglévő Azure parancssori felületet használja. Ha az Azure parancssori felület 2.0 (előzetes) verziója helyett a meglévő 1.0-s verziójú Azure parancssori felülettel szeretne Linux virtuális gépet létrehozni, tekintse meg a [Virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című cikket.

Virtuális gép létrehozásához: 

* Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)) van szükség
* telepítenie kell az [Azure parancssori felület 2.0 (előzetes)](/cli/azure/install-az-cli2) verzióját
* be kell jelentkeznie az Azure-fiókjába (írja be a következőt: [az login](/cli/azure/#login))

(Linux virtuális gépet az [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) használatával is gyorsan üzembe helyezhet.)

Az alábbi példa azt szemlélteti, hogyan helyezheti üzembe a Debian virtuális gépet, és hogyan csatolhatja a Secure Shell-kulcsot (SSH) (az argumentumok eltérhetnek; ha másik rendszerképet szeretne használni, [itt kereshet egyet](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Először írja be az [az group create](/cli/azure/group#create) parancsot az összes telepített erőforrást tartalmazó erőforráscsoport létrehozásához:

```azurecli
az group create -n myResourceGroup -l westus
```

A parancs kimenete a következőképpen néz ki (ha szeretne, más `--output` beállítást is választhat):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Hozza létre a virtuális gépet a legfrissebb Debian rendszerképből

Most mát létrehozhatja a virtuális gépet és annak környezetét. Ne feledjen egyedei értéket megadni a következőhöz: `----public-ip-address-dns-name`; előfordulhat, hogy a jelenlegi érték már foglalt.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


A kimenetnek a következőképpen néz ki. Jegyezze fel a `publicIpAddress` vagy az `fqdn` értékek egyikét a virtuális gépre való bejelentkezéshez **SSH**-val.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Jelentkezzen be a virtuális gépre a kimenetben szereplő nyilvános IP-cím használatával. A listában szereplő teljes tartománynevet (FQDN) is használhatja.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

A választott disztribúciótól függően a következő kimenethez hasonlót kell látnia:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Következő lépések
Az `az vm create` parancs a virtuális gépek gyors üzembe helyezésére szolgál, hogy bejelentkezhessen a rendszerhéjakba, és elkezdhesse a munkát. A `az vm create` használata azonban nem biztosít széles körű vezérlést, illetve összetettebb környezet létrehozását sem teszi lehetővé.  Az infrastruktúrának megfelelően beállított Linux virtuális gép üzembe helyezéséhez kattintson az alábbi cikkek valamelyikére:

* [Adott üzembe helyezés létrehozása Azure Resource Manager-sablonnal](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSH-védelemmel rendelkező Linux virtuális gép létrehozása az Azure-ban sablonok használatával](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Használhatja a `docker-machine` Azure-illesztőt is különféle parancsokkal egy Linux virtuális gép Docker-gazdagépként való gyors létrehozásához](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ha pedig Javát használ, próbálkozzon a [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) metódussal.




<!--HONumber=Jan17_HO1-->


