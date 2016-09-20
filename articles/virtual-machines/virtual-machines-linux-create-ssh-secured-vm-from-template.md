<properties
    pageTitle="Linux virtuális gép létrehozása Azure-sablon alapján | Microsoft Azure"
    description="Létrehozhat Linux virtuális gépet az Azure-ban Azure Resource Manager-sablon használatával."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="v-livech"/>

# Linux virtuális gép létrehozása Azure-sablon alapján

Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépet az Azure-ban Azure-sablon használatával.  A cikkben foglaltak végrehajtásához Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)) van szükség, és be kell jelentkezni [az Azure parancssori felületre](../xplat-cli-install.md) (`azure login`) erőforrás-kezelő módban (`azure config mode arm`).  Linux virtuális gépet az [Azure Portallal](virtual-machines-linux-quick-create-portal.md) vagy az [Azure parancssori felülettel](virtual-machines-linux-quick-create-cli.md) is gyorsan üzembe helyezhet.

## Gyors parancsösszegzés

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Részletes bemutató

A sablonokkal virtuális gépeket hozhat létre az Azure-ban az indításkor testre szabni kívánt beállításokkal, például felhasználónevekkel és állomásnevekkel. Ebben a cikkben elindítunk egy Ubuntu virtuális gépet használó Azure-sablont, valamint egy hálózati biztonsági csoportot (NSG-t), amelynek a 22-es portja nyitva van az SSH számára.

Az Azure Resource Manager-sablonok JSON-fájlok, amelyek egyszerű, egyszeri feladatokhoz használhatók, például egy Ubuntu virtuális gép indításához, mint ebben a cikkben.  Az Azure-sablonokkal teljes környezetek összetett Azure-konfigurációi is létrehozhatók, például a tesztelési, fejlesztési és éles üzembe helyezési vermek.

## A Linux virtuális gép létrehozása

Az alábbi kódpélda az `azure group create` parancs hívását mutatja be egy erőforráscsoport és az SSH által biztonságossá tett Linux virtuális gép egyidejű létrehozásához [ezen Azure Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) használatával. Ne feledje, hogy a példában a környezet egyedi neveit kell használni. Ez a példa a `quicksecuretemplate` erőforráscsoport-nevet, a `securelinux` virtuálisgép-nevet és a `quicksecurelinux` altartománynevet használja.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Kimenet

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

A példa a `--template-uri` paraméter használatával helyezett üzembe egy virtuális gépet.  Emellett letölthet vagy létrehozhat egy sablont helyben, majd átadhatja a sablont a `--template-file` paraméterrel a sablonfájl elérési útját használva argumentumként. Az Azure parancssori felület felszólítja a sablonhoz szükséges paraméterek megadására.

## Következő lépések

A [sablontárban](https://azure.microsoft.com/documentation/templates/) indított kereséssel derítheti ki, hogy mely alkalmazás-keretrendszert helyezze üzembe következőként.



<!--HONumber=sep16_HO1-->


