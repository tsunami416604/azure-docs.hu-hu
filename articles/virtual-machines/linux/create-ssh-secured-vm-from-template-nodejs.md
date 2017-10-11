---
title: "Linux virtuális gép létrehozása Azure-sablon alapján az Azure CLI 1.0 |} Microsoft Docs"
description: "Linux virtuális gép létrehozása az Azure-ban az Azure CLI 1.0 és az Azure Resource Manager-sablon használatával."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Linux virtuális gépet az Azure CLI 1.0 Azure Resource Manager-sablon létrehozása
Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépek az Azure CLI 1.0 és az Azure Resource Manager-sablon használatával. A cikkben foglaltak végrehajtásához szükség van:

* egy Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)),
* a [Azure CLI 1.0](../../cli-install-nodejs.md) bejelentkezett `azure login`.
* Az Azure parancssori felületnek `azure config mode arm` Azure Resource Manager módban *kell lennie*.

Az [Azure Portallal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is gyorsan üzembe helyezhet Linux rendszerű virtuálisgép-sablonokat.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-command-summary) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="quick-command-summary"></a>Gyors parancsösszegzés
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Részletes bemutató
A sablonokkal virtuális gépeket hozhat létre az Azure-ban az indításkor testre szabni kívánt beállításokkal, például felhasználónevekkel és állomásnevekkel. Ebben a cikkben elindítunk egy Ubuntu virtuális gépet használó Azure-sablont, valamint egy hálózati biztonsági csoportot (NSG-t), amelynek a 22-es portja nyitva van az SSH számára.

Az Azure Resource Manager-sablonok JSON-fájlok, amelyek egyszerű, egyszeri feladatokhoz használhatók, például egy Ubuntu virtuális gép indításához, mint ebben a cikkben.  Az Azure-sablonokkal teljes környezetek összetett Azure-konfigurációi is létrehozhatók, például a tesztelési, fejlesztési és éles üzembe helyezési vermek.

## <a name="create-the-linux-vm"></a>A Linux virtuális gép létrehozása
Az alábbi kódpélda az `azure group create` parancs hívását mutatja be egy erőforráscsoport és az SSH által biztonságossá tett Linux virtuális gép egyidejű létrehozásához [ezen Azure Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) használatával. Ne feledje, hogy a példában a környezet egyedi neveit kell használni. Ez a példa *myResourceGroup* az erőforráscsoport neve, mint és *myVM* Virtuálisgép-nevet.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

A kimenetnek az alábbi kimeneti blokkhoz hasonlóan kell kinéznie:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

A példa a `--template-uri` paraméter használatával helyezett üzembe egy virtuális gépet.  Emellett letölthet vagy létrehozhat egy sablont helyben, majd átadhatja a sablont a `--template-file` paraméterrel a sablonfájl elérési útját használva argumentumként. Az Azure parancssori felület felszólítja a sablonhoz szükséges paraméterek megadására.

## <a name="next-steps"></a>Következő lépések
A [sablontárban](https://azure.microsoft.com/documentation/templates/) indított kereséssel derítheti ki, hogy mely alkalmazás-keretrendszert helyezze üzembe következőként.

