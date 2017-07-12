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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2f93579eb1c7a69d0dbd1b0ef112aed9b2168c3
ms.contentlocale: hu-hu
ms.lasthandoff: 05/31/2017


---
<a id="different-ways-to-create-a-linux-vm" class="xliff"></a>

# Különböző módszerek Linux rendszerű virtuális gépek létrehozásához
Az Azure-ban rugalmasan hozhat létre Linux virtuális gépeket olyan eszközökkel és munkafolyamatokkal, amelyeket szívesen használ. Ez a cikk a Linux-alapú virtuális gépek létrehozásának ezen különböző módszereit és példáit foglalja össze, az Azure CLI 2.0-s verzióját is beleértve. Emellett megtekintheti a létrehozási lehetőségeket, beleértve az [Azure CLI 1.0](creation-choices-nodejs.md) használatát.

Az [Azure CLI 2.0](/cli/azure/install-az-cli2) több platformon elérhető egy npm-csomagon, disztribúció által biztosított csomagokon vagy a Docker-tárolón keresztül. Telepítheti a környezet számára legmegfelelőbb buildet, és bejelentkezhet egy Azure-fiókba az [az login](/cli/azure/#login) paranccsal

* [Linux-alapú virtuális gép létrehozása az Azure CLI 2.0-s verziójával](quick-create-cli.md)
  
  * Hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal: 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Hozzon létre egy *myVM* nevű virtuális gépet az [az vm create](/cli/azure/vm#create) paranccsal a legújabb *UbuntuLTS* rendszerkép használatával, majd hozzon létre SSH-kulcsokat, ha azok még nem léteznek az *~/.ssh* mappában:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md)
  
  * Az alábbi példa egy virtuális gépet hoz létre a GitHubon tárolt sablonból az [az group deployment create](/cli/azure/group/deployment#create) paranccsal:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Linux virtuális gép létrehozása és testreszabása a cloud-init paranccsal](tutorial-automate-vm-deployment.md)

* [Elosztott terhelésű és magas rendelkezésre állású alkalmazás létrehozása több Linux virtuális gépen](tutorial-load-balancer.md)


<a id="azure-portal" class="xliff"></a>

## Azure Portal
Az [Azure portálon](https://portal.azure.com) gyorsan létrehozhat egy virtuális gépet, mivel semmit nem kell telepítenie a rendszerre. A virtuális gép létrehozása az Azure Portallal:

* [Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md) 


<a id="operating-system-and-image-choices" class="xliff"></a>

## Választható operációs rendszerek és rendszerképek
A virtuális gépek létrehozásakor egy rendszerképet választ ki a futtatni kívánt operációs rendszer alapján. Az Azure és a partnerei számos rendszerképet kínálnak, amelyek némelyike előre telepített alkalmazásokat és eszközöket tartalmaz. Feltöltheti az egyik saját rendszerképét is (lásd [a következő szakaszt](#use-your-own-image)).

<a id="azure-images" class="xliff"></a>

### Azure-rendszerképek
Az [az vm image](/cli/azure/vm/image) paranccsal megtekintheti az elérhető elemeket közzétevő, disztribúciós kiadás, illetve build szerint.

Az elérhető közzétevők listázása:

```azurecli
az vm image list-publishers --location eastus
```

Egy adott közzétevő elérhető termékeinek (ajánlatainak) listázása:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Egy adott ajánlat elérhető termékváltozatainak (disztribúciós kiadásainak) listázása:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Egy adott kiadás összes elérhető rendszerképének listázása:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Az elérhető rendszerképek tallózásával és használatával kapcsolatos további példák: [Navigate and select Azure virtual machine images with the Azure CLI](cli-ps-findimage.md) (Azure virtuális gépek rendszerképének keresése és kiválasztása az Azure parancssori felülettel).

Az [az vm create](/cli/azure/vm#create) parancs rendelkezik aliasokkal, amelyek segítségével gyorsan hozzáférhet a leggyakoribb disztribúciókhoz és azok legújabb kiadásaihoz. Az aliasok használata gyakran gyorsabb, mintha meg kellene adnia a közzétevőt, ajánlatot, termékváltozatot és verziót, valahányszor létrehoz egy virtuális gépet:

| Alias | Közzétevő | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |legújabb |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |
| Debian |credativ |Debian |8 |legújabb |
| openSUSE |SUSE |openSUSE |13.2 |legújabb |
| RHEL |Redhat |RHEL |7.2 |legújabb |
| SLES |SLES |SLES |12-SP1 |legújabb |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |legújabb |

<a id="use-your-own-image" class="xliff"></a>

### Saját rendszerkép használata
Ha speciális egyéni beállításokra van szüksége, használhat egy meglévő Azure virtuális gépen alapuló rendszerképet a virtuális gép rögzítésével. Emellett feltölthet egy helyszínen létrehozott rendszerképet is. A támogatott disztribúciókkal és a saját rendszerképek használatával kapcsolatban az alábbi cikkekben tekinthet meg további információt:

* [Azure által támogatott disztribúciók](endorsed-distros.md)
* [Nem támogatott disztribúciókkal kapcsolatos tudnivalók](create-upload-generic.md)
* [Rendszerkép létrehozása meglévő Azure virtuális gépből](tutorial-custom-images.md).
  
  * Gyors üzembehelyezési példaparancsok rendszerkép meglévő Azure virtuális gépből való létrehozására:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

<a id="next-steps" class="xliff"></a>

## Következő lépések
* Hozzon létre egy Linux virtuális gépet a [parancssori felülettel](quick-create-cli.md), a [portálon](quick-create-portal.md) vagy [Azure Resource Manager-sablonnal](../windows/cli-deploy-templates.md).
* A Linux virtuális gép létrehozása után [ismerje meg az Azure lemezeket és tárolót](tutorial-manage-disks.md).
* Gyors lépések [jelszó vagy SSH-kulcsok alaphelyzetbe állításához és felhasználók kezeléséhez](using-vmaccess-extension.md).

