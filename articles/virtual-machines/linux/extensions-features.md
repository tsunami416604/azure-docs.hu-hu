---
title: "Virtuálisgép-bővítmények és a Linux funkcióit |} Microsoft Docs"
description: "Ismerje meg, milyen bővítmények érhetők el, mi akkor adja meg, vagy javítania szerint csoportosítva, az Azure virtuális gépeken."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 8a5b39351f665c51ae7d83f755329e54ff3cf786
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuálisgép-bővítmények és a Linux funkcióit

Az Azure virtuálisgép-bővítmények kis alkalmazásokat, amelyek telepítés utáni konfigurációs és automatizálási feladatok Azure virtuális gépeken. Például ha egy virtuális gépet a Szoftvertelepítés, a víruskereső védelmet, vagy a Docker konfigurációs igényel, a Virtuálisgép-bővítmény segítségével ezeket a feladatokat. Az Azure Virtuálisgép-bővítmények futtathatja az Azure parancssori felület, PowerShell, az Azure Resource Manager-sablonok és az Azure-portál használatával. Bővítmények mellékelhető egy új virtuálisgép-telepítést, vagy bármely létező rendszert futtathat.

Ez a dokumentum áttekintést nyújt az Virtuálisgép-bővítmények, Azure Virtuálisgép-bővítmények használatára vonatkozó Előfeltételek és útmutatás észleléséhez, kezeléséhez, és távolítsa el a Virtuálisgép-bővítmények. A dokumentum általános információkat tartalmaz a Virtuálisgép-bővítmények érhetők el, mert egyes potenciálisan egyedi konfigurációval. Bővítmény felhasználóspecifikus adatainak található minden a dokumentumban a egyéni bővítmény egyedi.

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure Virtuálisgép-bővítmények érhetők el, az adott használati eset. Néhány példa:

- PowerShell kívánt állapot konfiguráció alkalmazása a DSC-bővítményt használ a Linux virtuális gép. További információkért lásd: [Azure kívánt állapot konfigurációs kiterjesztése](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- A virtuális gép a Microsoft Monitoring Agent Virtuálisgép-bővítmény a figyelés konfigurálása. További információkért lásd: [a Linux virtuális gépek figyelése](tutorial-monitoring.md).
- Az Azure infrastruktúra Datadog kiterjesztésű figyelés konfigurálása. További információkért lásd: a [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- A Docker állomás konfigurálása az Azure virtuális gépen a Docker Virtuálisgép-bővítmény használatával. További információkért lásd: [Docker Virtuálisgép-bővítmény](dockerextension.md).

Folyamatokra vonatkozó bővítmények mellett egy egyéni parancsprogramok futtatására szolgáló bővítmény érhető el a Windows és a Linux virtuális gépekhez. Az egyéni parancsprogramok futtatására szolgáló bővítmény Linux lehetővé teszi, hogy a virtuális gépen futtatandó Bash parancsfájlok. Egyéni parancsfájlok túl milyen natív Azure tooling biztosíthat beállításokra van szükség Azure központi telepítések tervezése során hasznosak. További információkért lásd: [Linux virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény](extensions-customscript.md).


## <a name="prerequisites"></a>Előfeltételek

Minden egyes virtuálisgép-bővítmény előfordulhat, hogy rendelkeznek a saját előfeltételek. Például a Docker Virtuálisgép-bővítmény van egy támogatott Linux-disztribúció előfeltétele. Egyes bővítmények követelményeinek részletes leírást talál a bővítmény-specifikus dokumentációját.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure Virtuálisgép-ügynök kezeli a Azure virtuális gép és az Azure fabric controller közötti kommunikációt. A Virtuálisgép-ügynök telepítése és kezelése az Azure virtuális gépeken, beleértve a Virtuálisgép-bővítmények futtató sok működési jellemzői felelős. Az Azure Virtuálisgép-ügynök telepítve van az Azure piactéren elérhető rendszerkép, és támogatott operációs rendszeren manuálisan telepíthető.

Információ a támogatott operációs rendszerek és a telepítési utasításokat: [Azure virtuális gépi ügynöke](../windows/classic/agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése

Számos különböző Virtuálisgép-bővítmények állnak rendelkezésre az Azure virtuális gépekkel. Teljes listájának megtekintéséhez futtassa a következő parancsot az Azure parancssori felület, a példa cseréje a megfelelő helyre.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Futtassa a Virtuálisgép-bővítmények

Az Azure virtuálisgép-bővítmények a meglévő virtuális gépeken, amelyek hasznosak, ha konfigurációs módosításokat, vagy állítsa helyre a kapcsolatot egy már telepített virtuális gépen kell futtatható. Virtuálisgép-bővítmények is Azure Resource Manager sablon központi telepítéseket is telepíthet. Bővítmények a Resource Manager-sablonok segítségével az Azure virtuális gépek is telepítését és konfigurálását telepítés utáni beavatkozás nélkül.

Az alábbi eljárások segítségével bővítmény futtathat egy meglévő virtuális gépet.

### <a name="azure-cli"></a>Azure CLI

Meglévő virtuális gépből ellen is futtatható Azure virtuálisgép-bővítmények használatával a `az vm extension set` parancsot. Ebben a példában egy virtuális gép fut az egyéni parancsprogramok futtatására szolgáló bővítmény.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

A parancsfájl az alábbihoz hasonló kimenetet hoz létre:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Virtuálisgép-bővítmények egy meglévő virtuális gépet az Azure portálon keresztül is alkalmazható. Ehhez jelölje ki a virtuális gépet, válassza a **bővítmények**, és kattintson a **Hozzáadás**. Jelölje ki a bővítményt, szeretné, hogy a rendelkezésre álló bővítmények a listából, és kövesse a varázsló utasításait.

A következő kép bemutatja az Azure-portálról Linux egyéni parancsprogramok futtatására szolgáló bővítmény telepítése.

![Egyéni parancsfájl-kiterjesztés telepítése](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények felvehető az Azure Resource Manager-sablon és a sablon a központi telepítés végrehajtása. Egy bővítmény a sablon telepítésekor hozhat létre teljesen konfigurált Azure-környezetekhez. Például egy Resource Manager-sablon a következő JSON származik. A sablon olyan virtuális gépek elosztott terhelésű és az Azure SQL-adatbázis telepíti, és telepíti a .NET Core alkalmazások minden egyes virtuális gépen. A Virtuálisgép-bővítmény gondoskodik a szoftver telepítését.

További információkért lásd: a teljes [Resource Manager-sablon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

További információkért lásd: [Azure Resource Manager-sablonok készítése](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatvédelem

Jelenik meg a Virtuálisgép-bővítmény, akkor lehet szükség, például a hitelesítő adatokat, a tárfiókok neve és a fiók tárelérési kulcsok bizalmas adatokat tartalmazza. Több Virtuálisgép-bővítmények közé tartozik a védett konfigurációkat, amelyek titkosítja az adatokat, és csak visszafejti a cél virtuális gépen belül. Mindegyik bővítményt egy adott védett konfigurációs séma van, és az egyes részleteit a bővítmény-specifikus dokumentációját.

A következő példa bemutatja az egyéni parancsprogramok futtatására szolgáló bővítmény példányának Linux. Figyelje meg, hogy a parancs végrehajtásának tartalmazza-e a hitelesítő adatokat. Ebben a példában a parancs végrehajtása nem lesznek titkosítva.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Helyezze át a **végrehajtandó parancs** tulajdonságot a **védett** konfigurációs titkosítja a végrehajtási karakterlánc.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
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
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibaelhárítása

Minden egyes Virtuálisgép-bővítmény hibaelhárítási lépések az adott a kiterjesztést is rendelkezhet. Például ha az egyéni parancsprogramok futtatására szolgáló bővítmény használata esetén parancsfájl végrehajtásának részletes adatai található helyileg a virtuális gépen, amelyen a bővítmény futott. Bővítmény-specifikus hibaelhárítási lépéseket részletes leírást talál bővítmény vonatkozó dokumentációt.

A következő hibaelhárítási lépéseket az összes virtuálisgép-bővítmény vonatkozik.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Után a virtuális gépek bővítmény futtatva lett a virtuális gépek elleni, a következő paranccsal Azure CLI térjen vissza a bővítmény állapotát. Példa paraméterneveknek cserélje le a saját értékeit.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A kimeneti néz ki a következő szöveget:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Bővítmény végrehajtási állapotát az Azure portálon is található. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális gépet, kattintson a **bővítmények**, és jelölje ki a kívánt bővítményt.

### <a name="rerun-a-vm-extension"></a>Futtassa újra a Virtuálisgép-bővítmény

Előfordulhatnak olyan esetekben, ahol egy virtuálisgép-bővítmény futtatható kell. Egy bővítmény akkor távolítsa el, majd futtassa újból a kiterjesztés egy végrehajtási módszer az Ön által választott újrafuttathatja. Egy bővítmény eltávolításához futtassa a következő parancsot az Azure parancssori felület segítségével. Példa paraméterneveknek cserélje le a saját értékeit.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Az alábbi lépéseket követve az Azure portálon kiterjesztéssel megszüntetheti:

1. Válasszon ki egy virtuális gépet.
2. Válasszon **bővítmények**.
3. Jelölje ki a kívánt bővítményt.
4. Válasszon **eltávolítása**.

## <a name="common-vm-extension-reference"></a>Virtuálisgép-bővítmény közös hivatkozása
| Bővítmény neve | Leírás | További információ |
| --- | --- | --- |
| Linux rendszeren egyéni parancsprogramok futtatására szolgáló bővítmény |Parancsfájlok futtatásához egy Azure virtuális gépen |[Linux rendszeren egyéni parancsprogramok futtatására szolgáló bővítmény](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Docker-bővítmény |Telepítse a Docker démon távoli Docker parancsok támogatására. |[Docker virtuálisgép-bővítmény](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Virtuálisgép-hozzáférési bővítmény |Újra hozzáférést nyerni egy Azure virtuális gépen |[Virtuálisgép-hozzáférési bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-bővítményt |Az Azure Diagnostics kezelése |[Azure Diagnostics-bővítményt](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Az Azure virtuális gép hozzáférési bővítmény |Felhasználók és a hitelesítő adatok kezelése |[Linux virtuális gép hozzáférési bővítmény](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
