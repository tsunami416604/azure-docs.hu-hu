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
    ms.date="04/29/2016"
    ms.author="v-livech"/>

# Linux virtuális gép létrehozása Azure-sablon alapján

Ez a cikk bemutatja, hogyan helyezhet üzembe gyorsan Linux virtuális gépet az Azure-ban Azure-sablon használatával.  A cikkben foglaltak végrehajtásához Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/)) van szükség, és be kell jelentkezni [az Azure parancssori felületre](../xplat-cli-install.md) (`azure login`) erőforrás-kezelő módban (`azure config mode arm`).  Linux virtuális gépet az [Azure portállal](virtual-machines-linux-quick-create-portal.md) vagy az [Azure parancssori felülettel](virtual-machines-linux-quick-create-cli.md) is gyorsan üzembe helyezhet.


## Gyors parancs

Az alábbi parancspéldákban cserélje a &lt; és &gt; közötti értékeket a saját környezet értékeire.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Részletes bemutató

A sablonokkal virtuális gépeket hozhat létre az Azure-ban az indításkor testre szabni kívánt beállításokkal, például felhasználónevekkel és állomásnevekkel. Ebben a cikkben egy Ubuntu virtuális gép olyan Azure-sablonnal való indítását tárgyaljuk, amely hálózati biztonsági csoportot (NSG-t) hoz létre csak egy megnyitott porttal (22-es az SSH számára), és amelyhez SSH-kulcsokra van szükség a bejelentkezéshez.

Az Azure Resource Manager-sablonok JSON-fájlok, amelyek egyszerű, egyszeri feladatokhoz – például Ubuntu virtuális gép indításához, mint ebben a cikkben –, illetve olyan teljes környezetek összetett Azure-konfigurációjának a létrehozásához használhatók, amilyenek a hálózatról az operációs rendszeren vagy az alkalmazásveremben üzembe helyezett tesztelési, fejlesztési és éles üzemelő példányok.

## A Linux virtuális gép létrehozása

Az alábbi kódpélda az `azure group create` parancs hívását mutatja be egy erőforráscsoport és az SSH által biztonságossá tett Linux virtuális gép egyidejű létrehozásához [ezen Azure Resource Manager-sablon](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) használatával. Ne feledje, hogy a példában a környezet egyedi neveit kell használni. Ez a példa a `quicksecuretemplate` erőforráscsoport-nevet, a `securelinux` virtuálisgép-nevet és a `quicksecurelinux` altartománynevet használja.

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

Új erőforráscsoport létrehozásához és virtuális gép üzembe helyezéséhez a `--template-uri` paramétert használhatja, vagy letölthet vagy létrehozhat egy sablont helyben, majd átadhatja a sablont a `--template-file` paraméterrel a sablonfájl elérési útját használva argumentumként. Az Azure parancssori felület felszólítja a sablonhoz szükséges paraméterek megadására.

## További lépések

Miután létrehozott egy Linux virtuális gépet sablonok alapján, érdemes utánajárni, hogy mely más alkalmazás-keretrendszerek helyezhetők üzembe a sablonokkal. A [sablontárban](https://azure.microsoft.com/documentation/templates/) indított kereséssel derítheti ki, hogy mely alkalmazás-keretrendszert helyezze üzembe következőként.



<!--HONumber=Jun16_HO2-->


