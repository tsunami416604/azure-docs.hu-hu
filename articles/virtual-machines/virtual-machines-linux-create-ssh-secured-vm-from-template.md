<properties
    pageTitle="Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján | Microsoft Azure"
    description="Létrehozhat biztonságos Linux virtuális gépet az Azure-ban Azure Resource Manager-sablon használatával."
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
    ms.date="04/27/2016"
    ms.author="v-livech"/>

# Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján

Linux virtuális gép sablonból való létrehozásához szüksége lesz az erőforrás-kezelő módban megnyitott [az Azure parancssori felületre](../xplat-cli-install.md) (`azure config mode arm`).

## Gyors parancsösszegzés

```bash
chrisl@fedora$ azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]
```

## Részletes bemutató

A sablonokkal virtuális gépeket hozhat létre az Azure-ban az indításkor testre szabni kívánt beállításokkal, például felhasználónevekkel és állomásnevekkel. Ebben a cikkben egy Ubuntu virtuális gép olyan Azure-sablonnal való indítását tárgyaljuk, amely hálózati biztonsági csoportot (NSG-t) hoz létre csak egy megnyitott porttal (22-es az SSH számára), és amelyhez SSH-kulcsokra van szükség a bejelentkezéshez.

Az Azure Resource Manager-sablonok JSON-fájlok, amelyek egyszerű, egyszeri feladatokhoz – például Ubuntu virtuális gép indításához, mint ebben a cikkben –, illetve olyan teljes környezetek összetett Azure-konfigurációjának a létrehozásához használhatók, amilyenek a hálózatról az operációs rendszeren vagy az alkalmazásveremben üzembe helyezett tesztelési, fejlesztési és éles üzemelő példányok.

## A Linux virtuális gép létrehozása

Az alábbi kódpélda az `azure group create` parancs hívását mutatja be egy erőforráscsoport és az SSH által biztonságossá tett Linux virtuális gép egyidejű létrehozásához [ezen Azure Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) használatával. Ne feledje, hogy a példában a környezet egyedi neveit kell használni. Ez a példa a `quicksecuretemplate` erőforráscsoport-nevet, a `securelinux` virtuálisgép-nevet és a `quicksecurelinux` altartománynevet használja.

```bash
chrisl@fedora$ azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Új erőforráscsoport létrehozásához és virtuális gép üzembe helyezéséhez a `--template-uri` paramétert használhatja, vagy letölthet vagy létrehozhat egy sablont helyben, majd átadhatja a sablont a `--template-file` paraméterrel a sablonfájl elérési útját használva argumentumként. Az Azure parancssori felület felszólítja a sablonhoz szükséges paraméterek megadására.

## További lépések

Miután létrehozott egy Linux virtuális gépet sablonok alapján, érdemes utánajárni, hogy mely más alkalmazás-keretrendszerek használhatók a sablonokkal. A [sablontárban](https://azure.microsoft.com/documentation/templates/) indított kereséssel derítheti ki, hogy mely alkalmazás-keretrendszert helyezze üzembe következőként.



<!--HONumber=Jun16_HO2-->


