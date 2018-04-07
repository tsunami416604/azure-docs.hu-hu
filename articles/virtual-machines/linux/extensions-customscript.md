---
title: Egyéni parancsfájlok futtathatók a Linux virtuális gépek Azure-ban |} Microsoft Docs
description: Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával Linux virtuális gép konfigurációs feladatok automatizálásához
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 88f337df14b7e2647a76cce7ef91ec6f8950bbc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Az Azure egyéni parancsprogramok futtatására szolgáló bővítmény használata a Linux virtuális gépek
Az egyéni parancsprogramok futtatására szolgáló bővítmény tölti le, és futtatja a parancsfájlokat, az Azure virtuális gépeken. A bővítmény akkor hasznos, a telepítés utáni konfigurációjának, a szoftver telepítése vagy a bármely egyéb konfigurációs /-felügyeleti feladatot. Azure Storage vagy más elérhető internetes helyről töltheti parancsfájlok, vagy megadhatja azokat a bővítmény futásidejű. 

Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok. Is futtathatja az Azure parancssori felület, PowerShell, az Azure-portálon vagy az Azure virtuális gépek REST API használatával.

Ez a cikk részletezi az Azure parancssori Felületet az egyéni parancsprogramok futtatására szolgáló bővítmény használatával, és a bővítmény futtatása az Azure Resource Manager-sablon használatával. Ez a cikk a Linux rendszerek a hibaelhárítási lépéseket is biztosítja.

## <a name="extension-configuration"></a>Bővítmény konfigurációja
Az egyéni parancsprogramok futtatására szolgáló bővítmény konfigurációja határozza meg, többek között a parancsfájl helyét és a futtatandó parancsot. Ebben a konfigurációban tárolható konfigurációs fájlok, a parancssorban adja meg azt, vagy adja meg az Azure Resource Manager-sablonok. 

Egy védett konfigurációban, amely titkosított, és csak a virtuális gépen belüli visszafejteni bizalmas adatokat is tárolhatja. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs tartalmazza a titkos kulcsokat például a jelszót.

### <a name="public-configuration"></a>Nyilvános konfigurációja
A nyilvános bővítménykonfiguráció sémája a következőképpen történik.

>[!NOTE]
>A tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevét itt látható módon.

* **commandToExecute** (szükség esetén karakterlánc): A belépési pont parancsfájl futtatásához.
* **fileUris** (nem kötelező, karakterlánc-tömbben): az URL-címéből letöltendő fájlokat.
* **Timestamp típusú** (nem kötelező, egész szám): A időbélyegzőjét, parancsfájl. Ez a mező értékének megváltoztatásához aktiválják a parancsfájl egy futtassa újra a műveletet.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Védett konfiguráció
A védett konfigurációs sémája a következőképpen történik.

>[!NOTE]
>A tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevét itt látható módon.

* **commandToExecute** (nem kötelező, karakterlánc): A belépési pont parancsfájl futtatásához. Ez a mező akkor használja, ha a parancs tartalmazza a titkos kulcsok, például jelszavakat.
* **storageAccountName** (nem kötelező, karakterlánc): a tárfiók nevét. Tároló hitelesítő adatait adja meg, ha minden fájl URI-azonosítók kell URL-címek az Azure-blobokat.
* **storageAccountKey** (nem kötelező, karakterlánc): A hozzáférési kulcsot a tárfiók.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Amikor az Azure parancssori felület használja az egyéni parancsprogramok futtatására szolgáló bővítmény futtatásához, hozzon létre egy konfigurációs fájl vagy fájlokat. Legalább a konfigurációs fájlokat tartalmaz, a fájl URI és a parancsprogram-utasítás végrehajtása.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Ha szükséges megadhatja a beállításokat a parancsban JSON formátumú karakterlánc. Ez lehetővé teszi a konfigurációját, és végrehajtása során, és egy külön konfigurációs fájl nem adható meg.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-script-file"></a>Nyilvános parancsfájl-konfiguráció

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Nyilvános nincs parancsfájl-konfiguráció

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Nyilvános és a védett konfigurációs fájlok

Egy nyilvános konfigurációs fájl segítségével adja meg a parancsfájl URI azonosítója. Egy védett konfigurációs fájl segítségével adja meg a futtatandó parancsot.

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
A virtuális gép központi telepítéskor az Azure egyéni parancsprogramok futtatására szolgáló bővítmény futtathatja a Resource Manager-sablon használatával. Ehhez a központi telepítési sablont megfelelően formázott JSON hozzá.

### <a name="resource-manager-examples"></a>Erőforrás-kezelő példák

#### <a name="public-configuration"></a>Nyilvános konfigurációja

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

#### <a name="execution-command-in-protected-configuration"></a>Védett konfigurációban végrehajtási parancs

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

Teljes példa, tekintse meg a [.NET zeneáruház bemutató](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Hibaelhárítás
Ha az egyéni parancsprogramok futtatására szolgáló bővítmény fut, a parancsfájl létrehozásakor vagy egy olyan könyvtárba, az alábbi példához hasonló le. A parancs kimenetében is menti a könyvtárba, amely a `stdout` és `stderr` fájlokat.

```bash
/var/lib/waagent/custom-script/download/0/
```

Az Azure parancsprogramok futtatására szolgáló bővítmény hoz létre a napló, amely itt található:

```bash
/var/log/azure/custom-script/handler.log
```

Az egyéni parancsprogramok futtatására szolgáló bővítmény végrehajtási állapotát is Azure parancssori felület használatával kérheti le:

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

## <a name="next-steps"></a>További lépések
A többi virtuális gép parancsfájl-kiterjesztés információkért lásd: [linuxos Azure parancsfájl-bővítmény áttekintése](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

