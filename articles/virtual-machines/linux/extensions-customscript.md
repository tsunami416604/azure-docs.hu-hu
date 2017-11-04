---
title: "Egyéni parancsfájlok futtathatók a Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával Linux virtuális gép konfigurációs feladatok automatizálásához"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 1dde64aac72c11ccfccf4fdb676279692befaadd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Az Azure egyéni parancsprogramok futtatására szolgáló bővítmény használata a Linux virtuális gépek
Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájl az Azure virtuális gépeken. A bővítmény akkor hasznos, ha a feladás egy vagy több központi telepítés konfigurálása, a szoftver telepítése vagy a más beállításokat / kezelési feladatot. Parancsfájlok le: az Azure storage vagy más elérhető internet helyen, vagy a bővítmény futásidejű megadott. Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok, és is futtathat az Azure parancssori felület, PowerShell, Azure-portálon vagy az Azure virtuális gép REST API használatával.

Ez a dokumentum az Azure parancssori felület és az Azure Resource Manager-sablon az egyéni parancsprogramok futtatására szolgáló bővítmény használatával, és adatokat is a Linux rendszereken hibaelhárítási lépéseket részletezi.

## <a name="extension-configuration"></a>Bővítmény konfigurációja
Az egyéni parancsprogramok futtatására szolgáló bővítmény konfigurációja határozza meg, többek között a parancsfájl helyét és a futtatandó parancsot. Ebben a konfigurációban tárolható konfigurációs fájlok, a parancssorban vagy egy Azure Resource Manager-sablon a megadott. Egy védett konfigurációban, amely titkosított, és csak a virtuális gépen belüli visszafejteni bizalmas adatokat tárolhatja. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs tartalmazza a titkos kulcsokat például a jelszót.

### <a name="public-configuration"></a>Nyilvános konfigurációja
Séma:

**Megjegyzés:** -e a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. -Neveket használja az alább látható módon telepítési problémák elkerülése érdekében.

* **commandToExecute**: (szükség esetén karakterlánc) a belépési pont parancsprogram végrehajtására szolgáló
* **fileUris**: (nem kötelező, karakterlánc-tömbben) a letöltendő fájlok URL-címeit.
* **Timestamp típusú** (nem kötelező, egész szám) segítségével ebben a mezőben csak egy futtassa újból a parancsfájl indítás, ha megváltoztatja ennek a mezőnek értéket.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Védett konfiguráció
Séma:

**Megjegyzés:** -e a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. -Neveket használja az alább látható módon telepítési problémák elkerülése érdekében.

* **commandToExecute**: (nem kötelező, karakterlánc) a belépési pont parancsfájl végrehajtására. Ehelyett használja ezt a mezőt, ha a parancs tartalmazza a titkos kulcsok, például jelszavakat.
* **storageAccountName**: (nem kötelező, karakterlánc) storage-fiók nevét. Ha megadja a tároló hitelesítő adatait, minden fileUris URL-címek az Azure BLOB kell lennie.
* **storageAccountKey**: (nem kötelező, karakterlánc) storage-fiók hozzáférési kulcsának.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Ha az Azure parancssori felület használatával az egyéni parancsprogramok futtatására szolgáló bővítmény futtatásához, hozzon létre egy konfigurációs fájlban vagy a fájl uri és a parancsfájlt végrehajtó legalább tartalmazó fájlokat.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Opcionálisan a beállításokat adhat meg a parancsban JSON formátumú karakterlánc. Ez lehetővé teszi a konfigurációját, és végrehajtása során, és egy külön konfigurációs fájl nem adható meg.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],"commandToExecute": "./test.sh"}'
```

### <a name="azure-cli-examples"></a>Az Azure CLI példák

**1. példa** -parancsfájl nyilvános konfigurációja.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],
  "commandToExecute": "./test.sh"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**2. példa** -nyilvános konfigurációja nem parancsfájlt.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**3. példa** – nyilvános konfigurációs fájl használatával adja meg a parancsfájl URI és egy védett konfigurációs fájl használatával adja meg a végrehajtani a parancsot.

Nyilvános konfigurációs fájlban:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Védett konfigurációs fájlban:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Resource Manager-sablon
Az Azure egyéni parancsprogramok futtatására szolgáló bővítmény futtatható a virtuális gép központi telepítéskor Resource Manager-sablon használatával. Ehhez a központi telepítési sablont megfelelően formázott JSON hozzá.

### <a name="resource-manager-examples"></a>Erőforrás-kezelő példák
**1. példa** -nyilvános konfigurációjától.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**2. példa** -végrehajtási parancs védett konfigurációban.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Lásd: a .net Core zene tároló bemutató teljes példa - [zene tároló bemutató](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Hibaelhárítás
Ha az egyéni parancsprogramok futtatására szolgáló bővítmény fut, a parancsfájl létrehozásakor vagy egy olyan könyvtárba, az alábbi példához hasonló letöltött. A parancs kimenetében is menti a könyvtárba, amely a `stdout` és `stderr` fájlt.

```bash
/var/lib/waagent/custom-script/download/0/
```

Az Azure parancsprogramok futtatására szolgáló bővítmény hoz létre a napló, amely itt található.

```bash
/var/log/azure/custom-script/handler.log
```

Az egyéni parancsprogramok futtatására szolgáló bővítmény végrehajtási állapotát is beolvashatók az Azure parancssori felület segítségével.

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A kimeneti néz ki a következő szöveget:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Következő lépések
A többi virtuális gép parancsfájl-kiterjesztés információkért lásd: [Linux Azure parancsprogramok futtatására szolgáló bővítmény áttekintése](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

