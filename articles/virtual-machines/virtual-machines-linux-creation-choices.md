---
title: "Linux virtuális gépek Azure-ban való létrehozásának módszerei | Microsoft Azure"
description: "Ez a cikk a Linux virtuális gépek Azure-ban való létrehozásának különböző módszereit ismerteti, és minden módszer esetén további eszközökre és oktatóanyagokra mutató hivatkozásokat tartalmaz."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Linux virtuális gép létrehozásának módjai, beleértve az Azure CLI 2.0 (előzetes) verzióját
Az Azure-ban rugalmasan hozhat létre Linux virtuális gépeket olyan eszközökkel és munkafolyamatokkal, amelyeket szívesen használ. Ez a cikk a Linux virtuális gépek létrehozásának ezen különböző módszereit és példáit foglalja össze.

## <a name="azure-cli"></a>Azure CLI

A következő CLI-verziók egyikével elvégezheti a feladatot:

- Azure CLI 1.0 – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez
- [Azure CLI 2.0 (előzetes verzió)](../xplat-cli-install.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli

Az Azure CLI 2.0 (előzetes) verziója több platformon érhető el egy npm-csomag, disztribúció által biztosított csomagok vagy Docker-tároló útján. Ügyeljen arra, hogy be legyen jelentkezve az **az login** segítségével.

Az alábbi oktatóanyagok az Azure CLI 2.0 (előzetes verzió) használatával kapcsolatos példákat biztosítanak. Olvassa el az alábbi cikkeket a bemutatott parancsok további részleteivel kapcsolatban:

* [Linux virtuális gép létrehozása az Azure CLI 2.0 (előzetes) verziójával](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Ez a példa létrehoz egy myResourceGroup nevű erőforráscsoportot: 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * Ez a példa létrehoz egy virtuális gépet az új erőforráscsoportban a legújabb Debian rendszerképet egy `id_rsa.pub` nevű nyilvános kulccsal használva:

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

* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Az alábbi példa egy virtuális gépet hoz létre a GitHubon tárolt sablonnal:
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Teljes Linux-környezet létrehozása az Azure parancssori felülettel](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Magában foglalja egy terheléselosztó és több virtuális gép létrehozását egy rendelkezésre állási csoportban.

* [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Az alábbi példa hozzáad egy 5 GB-os lemezt a `myVM` nevű meglévő virtuális géphez:
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Azure Portal
Az [Azure portálon](https://portal.azure.com) gyorsan létrehozhat egy virtuális gépet, mivel semmit nem kell telepítenie a rendszerre. A virtuális gép létrehozása az Azure Portallal:

* [Linux virtuális gép létrehozása az Azure Portal használatával](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Lemez csatolása az Azure Portal használatával](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Választható operációs rendszerek és rendszerképek
A virtuális gépek létrehozásakor egy rendszerképet választ ki a futtatni kívánt operációs rendszer alapján. Az Azure és a partnerei számos rendszerképet kínálnak, amelyek némelyike előre telepített alkalmazásokat és eszközöket tartalmaz. Feltöltheti az egyik saját rendszerképét is (lásd [a következő szakaszt](#use-your-own-image)).

### <a name="azure-images"></a>Azure-rendszerképek
Az `az vm image` CLI-parancsok használatával megtekintheti az elérhető elemeket közzétevő, disztribúciós kiadás, illetve build szerint.

Az elérhető közzétevők listázása:

```azurecli
az vm image list-publishers -l WestUS
```

Egy adott közzétevő elérhető termékeinek (ajánlatainak) listázása:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

Egy adott ajánlat elérhető termékváltozatainak (disztribúciós kiadásainak) listázása:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

Egy adott kiadás összes elérhető rendszerképének listázása:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

Az elérhető rendszerképek tallózásával és használatával kapcsolatos további példák: [Navigate and select Azure virtual machine images with the Azure CLI](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Azure virtuális gépek rendszerképének keresése és kiválasztása az Azure parancssori felülettel).

Az `az vm create` parancs rendelkezik aliasokkal, amelyek segítségével gyorsan hozzáférhet a leggyakoribb disztribúciókhoz és azok legújabb kiadásaihoz. Az aliasok használata gyakran gyorsabb, mintha meg kellene adnia a közzétevőt, ajánlatot, termékváltozatot és verziót, valahányszor létrehoz egy virtuális gépet:

| Alias | Közzétevő | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |legújabb |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |
| Debian |credativ |Debian |8 |legújabb |
| openSUSE |SUSE |openSUSE |13.2 |legújabb |
| RHEL |Redhat |RHEL |7.2 |legújabb |
| SLES |SLES |SLES |12-SP1 |legújabb |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |legújabb |

### <a name="use-your-own-image"></a>Saját rendszerkép használata
Ha speciális egyéni beállításokra van szüksége, használhat egy meglévő Azure virtuális gépen alapuló rendszerképet a virtuális gép *rögzítésével*. Emellett feltölthet egy helyszínen létrehozott rendszerképet is. A támogatott disztribúciókkal és a saját rendszerképek használatával kapcsolatban az alábbi cikkekben tekinthet meg további információt:

* [Azure által támogatott disztribúciók](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Nem támogatott disztribúciókkal kapcsolatos tudnivalók](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [How to capture a Linux virtual machine as a Resource Manager template](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux virtuális gép rögzítése Resource Manager-sablonként).
  
  * Gyors üzembe helyezési példaparancsok egy meglévő virtuális gép rögzítésére:
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Következő lépések
* Hozzon létre egy Linux virtuális gépet a [portálon](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [parancssori felülettel](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [Azure Resource Manager-sablonnal](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* A Linux virtuális gép létrehozása után [adjon hozzá egy adatlemezt](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Gyors lépések [jelszó vagy SSH-kulcsok alaphelyzetbe állításához és felhasználók kezeléséhez](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


