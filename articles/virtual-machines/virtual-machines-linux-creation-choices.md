---
title: "Linux virtuális gépek létrehozásának különböző módszerei | Microsoft Docs"
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
ms.date: 09/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8156467ac97445576517ed7280307f6ca94ff0bf


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Linux virtuális gépek létrehozásának különböző módszerei az Azure-ban
Az Azure-ban rugalmasan hozhat létre Linux virtuális gépeket olyan eszközökkel és munkafolyamatokkal, amelyeket szívesen használ. Ez a cikk a Linux virtuális gépek létrehozásának ezen különböző módszereit és példáit foglalja össze.

## <a name="azure-cli"></a>Azure CLI
Az Azure CLI több platformon elérhető egy npm-csomagon, disztribúció által biztosított csomagokon vagy a Docker-tárolón keresztül. Az [Azure CLI telepítésével és konfigurálásával kapcsolatban itt](../xplat-cli-install.md) olvashat további információt. Az alábbi oktatóanyagok az Azure parancssori felület használatával kapcsolatos példákat biztosítanak. Olvassa el az alábbi cikkeket a bemutatott CLI gyors üzembe helyezési parancsok további részleteivel kapcsolatban:

* [Linux virtuális gép létrehozása az Azure parancssori felületen fejlesztéshez és teszteléshez](virtual-machines-linux-quick-create-cli.md)
  
  * Az alábbi példa egy CoreOS virtuális gépet hoz létre egy `azure_id_rsa.pub` nevű nyilvános kulccsal:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
  
  * Az alábbi példa egy virtuális gépet hoz létre a GitHubon tárolt sablonnal:
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Teljes Linux-környezet létrehozása az Azure parancssori felülettel](virtual-machines-linux-create-cli-complete.md)
  
  * Magában foglalja egy terheléselosztó és több virtuális gép létrehozását egy rendelkezésre állási csoportban.
* [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](virtual-machines-linux-add-disk.md)
  
  * Az alábbi példa hozzáad egy 5 GB-os lemezt a `TestVM` nevű meglévő virtuális géphez:
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Azure Portal
Az [Azure portálon](https://portal.azure.com) gyorsan létrehozhat egy virtuális gépet, mivel semmit nem kell telepítenie a rendszerre. A virtuális gép létrehozása az Azure Portallal:

* [Linux virtuális gép létrehozása az Azure Portal használatával](virtual-machines-linux-quick-create-portal.md) 
* [Lemez csatolása az Azure Portal használatával](virtual-machines-linux-attach-disk-portal.md)

## <a name="operating-system-and-image-choices"></a>Választható operációs rendszerek és rendszerképek
A virtuális gépek létrehozásakor egy rendszerképet választ ki a futtatni kívánt operációs rendszer alapján. Az Azure és a partnerei számos rendszerképet kínálnak, amelyek némelyike előre telepített alkalmazásokat és eszközöket tartalmaz. Feltöltheti az egyik saját rendszerképét is (lásd [a következő szakaszt](#use-your-own-image)).

### <a name="azure-images"></a>Azure-rendszerképek
Az `azure vm image` CLI-parancsok használatával megtekintheti az elérhető elemeket közzétevő, disztribúciós kiadás, illetve build szerint.

A következőképpen listázhatja az elérhető közzétevőket:

```azurecli
azure vm image list-publishers --location WestUS
```

A következőképpen listázhatja egy adott közzétevő elérhető termékeit (ajánlatait):

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

A következőképpen listázhatja egy adott ajánlat elérhető termékváltozatait (disztribúciós kiadásait):

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

A következőképpen listázhatja egy adott kiadás összes elérhető rendszerképét:

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Az elérhető rendszerképek tallózásával és használatával kapcsolatos további példák: [Navigate and select Azure virtual machine images with the Azure CLI](virtual-machines-linux-cli-ps-findimage.md) (Azure virtuális gépek rendszerképének keresése és kiválasztása az Azure parancssori felülettel).

Az `azure vm quick-create` és az `azure vm create` parancs is rendelkezik aliasokkal, amelyek segítségével gyorsan hozzáférhet a leggyakoribb disztribúciókhoz és azok legújabb kiadásaihoz. Az aliasok használata gyakran gyorsabb, mintha meg kellene adnia a közzétevőt, ajánlatot, termékváltozatot és verziót, valahányszor létrehoz egy virtuális gépet:

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

* [Azure által támogatott disztribúciók](virtual-machines-linux-endorsed-distros.md)
* [Nem támogatott disztribúciókkal kapcsolatos tudnivalók](virtual-machines-linux-create-upload-generic.md)
* [How to capture a Linux virtual machine as a Resource Manager template](virtual-machines-linux-capture-image.md) (Linux virtuális gép rögzítése Resource Manager-sablonként).
  
  * Gyors üzembe helyezési példaparancsok egy meglévő virtuális gép rögzítésére:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Következő lépések
* Hozzon létre egy Linux virtuális gépet a [portálon](virtual-machines-linux-quick-create-portal.md) a [parancssori felülettel](virtual-machines-linux-quick-create-cli.md) vagy [Azure Resource Manager-sablonnal](virtual-machines-linux-cli-deploy-templates.md).
* A Linux virtuális gép létrehozása után [adjon hozzá egy adatlemezt](virtual-machines-linux-add-disk.md).
* Gyors lépések [jelszó vagy SSH-kulcsok alaphelyzetbe állításához és felhasználók kezeléséhez](virtual-machines-linux-using-vmaccess-extension.md)




<!--HONumber=Nov16_HO2-->


