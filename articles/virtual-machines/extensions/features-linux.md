---
title: Az Azure Virtuálisgép-bővítmények és szolgáltatások Linux rendszeren |} A Microsoft Docs
description: Ismerje meg, hogy mely bővítmények érhetők el az Azure virtual machines, mit, adja meg vagy javíthatja a szerint csoportosítva.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 255fe84f0d155902b2a3ac477a1e677efef42bb4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386656"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuális gépi bővítmények és szolgáltatások linuxhoz

Az Azure virtuális gép (VM) a bővítmények olyan kisebb alkalmazásoknál, amelyek az üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure virtuális gépeken. Ha például egy virtuális géphez szükséges szoftver telepítése, víruskereső védelmet, vagy belül, a parancsfájl futtatásához a VM-bővítmény segítségével. Az Azure Virtuálisgép-bővítmények az Azure CLI, PowerShell, az Azure Resource Manager-sablonokkal és az Azure Portalon is futtatható. Bővítmények egy új virtuális gép üzembe helyezésének mellékelhető, vagy minden meglévő rendszeren futtatható.

Ez a cikk a Virtuálisgép-bővítmények, az Azure Virtuálisgép-bővítmények, használatára vonatkozó Előfeltételek áttekintést nyújt, és ismeri fel, hogyan kezelheti, és távolítsa el a Virtuálisgép-bővítmények. Ez a cikk általános információkat nyújt, számos Virtuálisgép-bővítmények érhetők el, mert egyes potenciálisan egyedi konfigurációval. Bővítmény-specifikus részletei megtalálhatók az egyes dokumentumok az egyéni bővítmény egyedi.

## <a name="use-cases-and-samples"></a>Használatieset-forgatókönyveit és a minták

Számos különböző Azure-beli Virtuálisgép-bővítmények érhetők el, amelyek mindegyike egy adott használati eset. Néhány példa:

- PowerShell célállapot-konfiguráció alkalmazása a egy virtuális Gépet a DSC-bővítmény a Linuxhoz készült. További információkért lásd: [Azure Desired State configuration bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- A Microsoft Monitoring Agent Virtuálisgép-bővítmény rendelkező virtuális gépek figyelés konfigurálása. További információkért lásd: [Linux rendszerű virtuális gép figyelése](../linux/tutorial-monitoring.md).
- Az Azure-infrastruktúrát a Chef vagy a Datadoggal kiterjesztésű figyelésének konfigurálása. További információkért lásd: a [Chef docs](https://docs.chef.io/azure_portal.html) vagy [Datadoggal blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Folyamat-specifikus kiterjesztéseket mellett egy egyéni szkriptek bővítménye Windows és a Linux rendszerű virtuális gépek számára érhető el. Az egyéni szkriptek futtatására szolgáló bővítmény linuxhoz lehetővé teszi, hogy bármilyen Bash szkript futtatható virtuális gépen. Egyéni parancsfájlok tervezése az Azure-telepítések konfigurációs túli milyen natív Azure-eszközkészlet biztosíthat igénylő hasznosak. További információkért lásd: [Linux rendszerű virtuális gép egyéni szkriptek futtatására szolgáló bővítmény](custom-script-linux.md).

## <a name="prerequisites"></a>Előfeltételek

A bővítményt a virtuális gép kezelése érdekében az Azure Linux-ügynök telepítve van szükség. Néhány egyéni bővítményt kell Előfeltételek, például az erőforrások és a függőségek eléréséhez.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure-beli Virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric controller közötti interakciókat. A Virtuálisgép-ügynök üzembe helyezése és kezelése az Azure virtuális gépekhez, beleértve a futó Virtuálisgép-bővítmények számos funkcionális szempontból felelős. Az Azure-beli Virtuálisgép-ügynök az Azure Marketplace-rendszerképek előre telepítve van, és a egy támogatott operációs rendszeren manuálisan telepíthető. Az Azure linuxos Virtuálisgép-ügynök a Linux-ügynök néven ismert.

A támogatott operációs rendszerek és telepítésével kapcsolatos tudnivalókat lásd: [Azure-beli virtuálisgép-ügynök](agent-linux.md).

#### <a name="supported-agent-versions"></a>Az ügynök támogatott verziója

Annak érdekében, hogy a lehető legjobb élményt, vannak az ügynök minimális verziója. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Beállított támogatott operációs rendszerek

A Linux-ügynök fut több OSE-kre, azonban a bővítmények keretrendszer van korlátozva a OSE-kre vonatkozó adott bővítmények. További információkért lásd: [Ez a cikk] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Néhány bővítmény nem támogatja az összes OSE-kre között, és bocsáthat *hiba kódja 51, a "Nem támogatott operációs rendszer"*. Támogatási lehetőségek az egyes bővítmény dokumentációjában.

#### <a name="network-access"></a>Hálózati hozzáférés

Bővítménycsomagok letöltődnek az Azure Storage-bővítmény adattárból, és az Azure Storage-bővítmény állapota feltöltések videóközpontba. Ha [támogatott](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) az ügynökök verzióját, akkor nem kell engedélyezi a hozzáférést az Azure Storage a virtuális gép régióban, mivel az ügynök használhatják a kommunikáció átirányítása az Azure fabric controller, az ügynök-kommunikációhoz. Ha az ügynök nem támogatott verziója van, engedélyezi a kimenő hozzáférést az adott régióban az Azure Storage a virtuális gépről szeretne.

> [!IMPORTANT]
> Ha a hozzáférést a letiltott *168.63.129.1* a Vendég tűzfalat használ, akkor bővítmények sikertelen a fenti függetlenül.

Ügynökök csak bővítménycsomagok és állapotjelentést letöltéséhez használható. Például, ha a bővítmény telepítését le kell töltenie egy szkriptet a Githubról (egyéni szkript), vagy kell elérni az Azure Storage (Azure Backup), majd további tűzfal és a hálózati biztonsági csoport portokat kell megnyitni. A különböző bővítmények eltérő követelmények vonatkoznak, lehet, mert a saját jobb alkalmazások. Az Azure Storage-hozzáférést igénylő bővítmények, Azure NSG szolgáltatás címkék használatával hozzáférést biztosíthat [tárolási](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Ügynökkérelmek forgalom átirányítása, a Linux-ügynök van a proxykiszolgáló-támogatás. A proxykiszolgáló-támogatás azonban nem vonatkozik a bővítmények. Be kell állítania minden egyes bővítmény proxyval működjenek.

## <a name="discover-vm-extensions"></a>Fedezze fel a Virtuálisgép-bővítmények

Számos különböző Virtuálisgép-bővítmények Azure virtuális gépekhez való használatra érhetők el. Teljes listájának megtekintéséhez használja [az vm-bővítmény képlista](/cli/azure/vm/extension/image#az-vm-extension-image-list). Az alábbi példa felsorolja az összes rendelkezésre álló bővítményeket a *westus* helye:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Futtassa a Virtuálisgép-bővítmények

Azure Virtuálisgép-bővítmények futtassa a meglévő virtuális gépeken, amelyek akkor hasznos, ha a konfigurációs módosításokat, vagy helyreállítani a kapcsolatot egy már üzembe helyezett virtuális gépen kell. Virtuálisgép-bővítmények emellett az Azure Resource Manager-sablon-üzembehelyezések is telepíthet. Bővítmények használatával a Resource Manager-sablonok, Azure virtuális gépeken telepíthető és üzembe helyezés utáni beavatkozás nélkül konfigurálva.

Az alábbi módszerek bővítmény egy meglévő virtuális gép futtatásához használható.

### <a name="azure-cli"></a>Azure CLI

Az Azure Virtuálisgép-bővítmények a meglévő virtuális gép is futtatható a [az virtuálisgép-bővítmény csoportot](/cli/azure/vm/extension#az-vm-extension-set) parancsot. Az alábbi példa egy nevű virtuális gép fut az egyéni szkriptek bővítménye *myVM* nevű erőforráscsoportból *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Ha a bővítmény megfelelően fut, a kimenet a az alábbi példához hasonló:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

A Virtuálisgép-bővítmények is alkalmazható egy meglévő virtuális Gépet az Azure Portalon keresztül. Válassza ki a virtuális Gépet a portálon, válassza a **bővítmények**, majd **Hozzáadás**. Válassza ki a bővítményt, az elérhető bővítmények listájából szeretne, majd kövesse a varázsló utasításait.

Az alábbi képen látható a telepítés, a Linux egyéni szkriptek bővítménye az Azure Portalról:

![Egyéni szkriptek futtatására szolgáló bővítmény telepítése](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények hozzáadható az Azure Resource Manager-sablon és a központi telepítési sablon végrehajtani. Amikor telepít egy bővítmény-sablonnal, teljes konfigurációjú Azure-környezetek is létrehozhat. Például a következő JSON veszi át egy Resource Manager-sablon, amely telepíti az elosztott terhelésű virtuális gépek és a egy Azure SQL database, majd telepíti a .NET Core-alkalmazást az egyes virtuális Gépeken. A Virtuálisgép-bővítmény gondoskodik a szoftver telepítését.

További információkért tekintse meg a teljes [Resource Manager-sablon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>VM-bővítmény adatok védelme

Amikor futtat egy Virtuálisgép-bővítmény, lehet szükséges a például hitelesítő adatokat, a tárfiókok nevének és a tárfiók hozzáférési kulcsainak bizalmas adatokat. Számos Virtuálisgép-bővítmények közé tartozik egy védett konfigurációt, amely titkosítja az adatokat, és csak visszafejti a cél virtuális Gépen belül. Minden kiterjesztéssel rendelkezik egy adott védett konfigurációs sémáját, és az egyes részleteit a bővítmény-specifikus dokumentációját.

Az alábbi példa bemutatja egy példányát az egyéni szkriptek futtatására szolgáló bővítmény linuxhoz. A parancs végrehajtásához hitelesítő adatok készletét tartalmazza. Ebben a példában a parancs végrehajtásához nincs titkosítva:

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

Áthelyezés a **végrehajtandó parancs** tulajdonságot a **védett** konfigurációs védi a végrehajtási karakterlánc az alábbi példában látható módon:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan hajtsa végre az ügynökök és a bővítmény frissített?

Az ügynökök és a bővítmények ossza meg az azonos frissítési mechanizmust. Néhány frissítést nem szükséges további tűzfalszabályokat.

Ha egy frissítés érhető el, csak telepíti a rendszer a virtuális gépen, és megváltoznak a bővítmények és más virtuális gép adatmodell változásainak, mint például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztika tároló
- Vendég-operációsrendszer titkok
- Virtuális gép mérete
- Hálózati profil

A kiadók elérhetővé frissítések régiók eltérő időpontokban, így a különböző verziói különböző régiókban található virtuális gépek is használhat.

#### <a name="agent-updates"></a>Ügynökfrissítések

Linuxos Virtuálisgép-ügynök tartalmaz *kiépítési ügynök kód* és *kiterjesztés kezelő kód* egy csomagban, amely nem kell elválasztani. Letilthatja a *kiépítési ügynök* való üzembe helyezése a cloud-init használata Azure-ban. Ehhez lásd [cloud-init használata](../linux/using-cloud-init.md).

Az ügynökök támogatott verzióit használhatja az automatikus frissítések. A csak kód frissíthető a *kiterjesztés kezelő kód*, nem a kiépítési kódot. A *kiépítés ügynökkód* egyszer futtatott kód.

A *kiterjesztés kezelő kód* felelős az Azure-hálót kommunikáló, illetve a VM-bővítmények műveletek kezelésére például telepíti, állapotát, az egyes bővítmények frissítése és eltávolítása őket. Frissítések tartalmazzák a biztonsági javítások, a hibajavítások és a webhelyszolgáltatásokkal kapcsolatos fejlesztéseket tartalmaz az *kiterjesztés kezelő kód*.

Ha az ügynök telepítve van, a szülő démon jön létre. Ezzel a szülővel majd indít egy alárendelt folyamat, amely bővítmények kezelésére szolgál. Az ügynök frissítésként érhető el, ha le lettek töltve, a szülő a gyermekfolyamat leállítása, akkor frissíti, majd újraindítja. Kell egy problémát a frissítéssel, a szülő folyamat visszaállítja az előző gyermek verzió.

A szülő folyamat nem lehet automatikusan frissíteni. A szülő által disztribúció csomag frissítése csak akkor lehet frissíteni.

Ellenőrizze, melyik verzióját használja, ellenőrizze a `waagent` módon:

```bash
waagent --version
```

A kimenet a következő példához hasonló:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Az előző példa kimenetében, a szülő vagy a "csomag telepített verzió: az *WALinuxAgent-2.2.17*

A "cél állapot ügynök" az automatikus frissítési verziója.

Azt javasoljuk, hogy mindig legyen az automatikus frissítés az ügynökhöz [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Nem kellene az engedélyezett azt jelenti, hogy továbbra is szeretné frissíteni az ügynököt, és nem kérdezhető le a hibajavításokat és biztonsági javításokat.

#### <a name="extension-updates"></a>Bővítmény-frissítések

Bővítmény frissítés érhető el, ha a Linux-ügynök tölti le, és frissíti a bővítményt. Frissítések automatikus bővítmény kiadásokhoz *kisebb* vagy *gyorsjavítás*. Engedélyezve van a hibajelentések, vagy tilthatják le az bővítmények *kisebb* frissíti, amikor üzembe helyezi a bővítményt. A következő példa bemutatja, hogyan automatikus frissítése a Resource Manager-sablonnal rendelkező alverziót *autoUpgradeMinorVersion ": true,"*:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

A legújabb kiadás kisebb hibajavítások lekéréséhez azt javasoljuk, hogy mindig választja az automatikus frissítés a bővítmény központi telepítések. Gyorsjavítás-frissítések, biztonsági vagy kulcs hibajavításokat tartalmaz, amelyek nem kilépteti a programból.

### <a name="how-to-identify-extension-updates"></a>Bővítmény frissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Ha a bővítmény az autoUpgradeMinorVersion van beállítva a virtuális gép azonosítása

Ha a bővítmény lett üzembe helyezve, az "autoUpgradeMinorVersion" láthatja a virtuális gép modellből. Ellenőrzéséhez használja [az vm show](/cli/azure/vm#az-vm-show) , és adja meg az erőforráscsoportot és a virtuális gép neve a következő:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Az alábbi példa kimenetében látható, amely *autoUpgradeMinorVersion* értékre van állítva *igaz*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Amikor egy autoUpgradeMinorVersion történt azonosítása

Tekintse meg, mikor történt, a bővítmény frissítését, tekintse át az ügynököt a virtuális gép egyidejű bejelentkezik */var/log/waagent.log*.

Az alábbi példában a virtuális Gépet kellett *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* telepítve. A gyorsjavítás volt elérhető *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Az ügynök engedélyek

A feladatok végrehajtásához az ügynököt futtató minden *legfelső szintű*.

## <a name="troubleshoot-vm-extensions"></a>A Virtuálisgép-bővítmények hibaelhárítása

Minden egyes Virtuálisgép-bővítmény lehet hibaelhárítási lépések adott, a bővítményt. Például az egyéni szkriptek futtatására szolgáló bővítmény használatakor szkriptvégrehajtás részletei találhatók helyileg a virtuális gép, ahol a bővítmény futtatták. Bővítmény-specifikus hibaelhárítási lépéseket részletesen ismertetett bővítmény-specifikus dokumentációját.

Az alábbi hibaelhárítási lépéseket az összes Virtuálisgép-bővítmények vonatkozik.

1. A Linux-ügynök naplójában ellenőrzéséhez tekintse meg a tevékenység során lett üzembe helyezve a bővítményt a */var/log/waagent.log*

2. Tekintse meg a tényleges bővítmény naplókat a további részletekért   */var/log/azure /<extensionName>*

3. Ellenőrizze a bővítmény-specifikus dokumentáció hibaelhárítási szakaszokat hibakódok ismert problémák stb.

3. Tekintse meg a rendszernaplót. Ellenőrizze, hogy előfordulhat, hogy rendelkezik zavarja a kiterjesztéssel, például egy másik alkalmazás kizárólagos package manager hozzáférés szükséges egy hosszú ideig futó telepített egyéb műveleteket.

### <a name="common-reasons-for-extension-failures"></a>Bővítmény hibák leggyakoribb okai

1. Bővítmények rendelkezik futtatásához 20 perc (kivétel a CustomScript bővítmény, a Chef és a DSC, amelyek 90 perc). Ha az üzemelő példány ez idő meghaladja, van megjelölve, időtúllépés. Ennek oka valószínűleg kevés az erőforrás-beli virtuális gépek más virtuális gép konfigurációk/indítási feladatok nagy mennyiségű erőforrást használ, miközben a bővítmény kiépítése próbál.

2. Minimális előfeltételek nem teljesülnek. Néhány bővítmény függőségekkel rendelkező VM termékváltozatokban, például a HPC-képeket. Bővítmények szükség lehet bizonyos hálózati hozzáférési követelmények, például az Azure Storage-vagy nyilvános szolgáltatásokhoz való kommunikációhoz. További példák lehet csomag adattárak, kevés a szabad lemezterület, vagy a biztonsági korlátozások a hozzáférést.

3. Kizárólagos package manager-hozzáféréssel. Bizonyos esetekben felmerülhet egy hosszú ideig futó Virtuálisgép-konfiguráció és a bővítmény telepítése ütköző, ahol mindkettő kell a package manager kizárólagos hozzáférést.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

VM-bővítmény virtuális gépek elleni futott, miután a [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) való visszatéréshez a bővítmény állapotát a következő:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A kimenet hasonlít az alábbi példa kimenetében:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Bővítmény végrehajtási állapotát az Azure Portalon is található. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális Gépet, válassza a **bővítmények**, majd válassza ki a kívánt bővítményt.

### <a name="rerun-a-vm-extension"></a>Futtassa újra a VM-bővítmény

Előfordulhat, hogy esetek, amelyben a VM-bővítmény kell újra kell futtatni. Egy bővítmény akkor távolítsa el, majd újbóli a bővítmény-végrehajtási módszerrel tetszőleges futtathatja. Egy bővítmény eltávolításához használja [vm-bővítmény törlése az](/cli/azure/vm/extension#az-vm-extension-delete) módon:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Valamint eltávolíthatja kiterjesztése az Azure Portalon a következő:

1. Válasszon egy virtuális Gépet.
2. Válasszon **bővítmények**.
3. Válassza ki a kívánt bővítményt.
4. Válasszon **eltávolítása**.

## <a name="common-vm-extension-reference"></a>Közös Virtuálisgép-bővítményre mutató hivatkozás

| Bővítmény neve | Leírás | További információ |
| --- | --- | --- |
| Egyéni parancsfájl-bővítmény linuxhoz |Szkriptek futtatása az Azure virtuális gépek ellen |[Egyéni parancsfájl-bővítmény linuxhoz](custom-script-linux.md) |
| Virtuálisgép-hozzáférési bővítmény |Az Azure virtuális gépként való hozzáférés visszaszerzéséhez |[Virtuálisgép-hozzáférési bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics bővítmény |Az Azure Diagnostics kezelése |[Az Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Az Azure Virtuálisgép-hozzáférési bővítmény |Felhasználók és a hitelesítő adatok kezelése |[Linuxos Virtuálisgép-hozzáférési bővítmény](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>További lépések

Virtuálisgép-bővítményekkel kapcsolatos további információkért lásd: [Azure-beli virtuálisgép-bővítmények és szolgáltatások áttekintése](overview.md).
