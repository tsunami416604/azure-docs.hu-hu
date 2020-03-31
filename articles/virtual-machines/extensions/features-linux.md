---
title: Az Azure VM-bővítmények és -funkciók Linuxhoz
description: Ismerje meg, hogy milyen bővítmények érhetők el az Azure virtuális gépekhez, az általuk biztosított vagy javított eszközök szerint csoportosítva.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250516"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuális gép bővítmények és funkciók Linuxhoz

Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény. Az Azure virtuálisgép-bővítmények az Azure CLI, a PowerShell, az Azure Resource Manager-sablonok és az Azure Portal használatával futtathatók. A bővítmények egy kötegben hajthatók végre az új virtuális gépek üzembe helyezésével, vagy meglévő rendszereken is futtathatók.

Ez a cikk áttekintést nyújt a virtuális gép-bővítményekről, az Azure Virtuálisgép-bővítmények használatának előfeltételeiről, valamint a virtuálisgép-bővítmények észlelésével, kezelésével és eltávolításával kapcsolatos útmutatást. Ez a cikk általános információkat nyújt, mert sok virtuálisgép-bővítmény érhető el, mindegyik potenciálisan egyedi konfigurációval rendelkezik. A kiterjesztés-specifikus részletek az egyes bővítményekre jellemző dokumentumokban találhatók.

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure virtuálisgép-bővítmények érhetők el, mindegyik egy adott használati eset. Néhány példa:

- PowerShell kívánt állapotkonfigurációk alkalmazása egy virtuális gép a DSC-bővítmény Linux. További információ: [Azure Desired State configuration extension.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)
- Konfigurálja a virtuális gép figyelését a Microsoft Monitoring Agent virtuálisgép-bővítményével. További információ: [Linux virtuális gépek figyelése.](../linux/tutorial-monitoring.md)
- Konfigurálja az Azure-infrastruktúra figyelését a Chef vagy a Datadog kiterjesztéssel. További információ: [Chef docs](https://docs.chef.io/azure_portal.html) or [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

A folyamatspecifikus bővítmények mellett egy egyéni parancsfájl-bővítmény is elérhető Windows és Linux virtuális gépeken. A Linux egyéni parancsfájl-bővítménylehetővé teszi, hogy bármely Bash-parancsfájl futjon a virtuális számítógépen. Az egyéni parancsfájlok hasznosak az Azure-telepítések tervezéséhez, amelyek a natív Azure-eszközök által biztosított konfiguráción túl igényelnek. További információ: [Linux VM Custom Script extension](custom-script-linux.md).

## <a name="prerequisites"></a>Előfeltételek

A bővítmény kezeléséhez a virtuális gép, telepítenie kell az Azure Linux-ügynök. Egyes egyes bővítmények előfeltételei, például az erőforrásokhoz vagy a függőségekhez való hozzáférés.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric vezérlő közötti interakciókat. A virtuálisgép-ügynök az Azure virtuális gépek üzembe helyezésének és kezelésének számos funkcionális szempontjáért felelős, beleértve a virtuálisgép-bővítmények futtatását is. Az Azure Virtuálisgép-ügynök előre telepítve van az Azure Marketplace-rendszerképeken, és manuálisan telepíthető a támogatott operációs rendszereken. Az Azure VM Agent for Linux linuxos linuxos ügynöknéven ismert.

A támogatott operációs rendszerekről és a telepítési utasításokról az [Azure virtuálisgép-ügynök](agent-linux.md)című témakörben talál tájékoztatást.

#### <a name="supported-agent-versions"></a>Támogatott ügynökverziók

Annak érdekében, hogy a lehető legjobb élményt, vannak minimális verziói az ügynök. További információt [ebben a cikkben](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)talál.

#### <a name="supported-oses"></a>Támogatott oses

A Linux-ügynök több operációs rendszeren fut, azonban a bővítmények keretrendszer rendelkezik egy korlátot az operációs rendszer, amely a kiterjesztések. További információt [ebben a cikkben](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
)talál.

Egyes bővítmények nem támogatottak az összes operációs rendszer ben, és *az 51-es, "Nem támogatott operációs rendszer" hibakódot*bocsáthatnak ki. Ellenőrizze az egyes bővítmények dokumentációjában a támogathatóságot.

#### <a name="network-access"></a>Hálózati hozzáférés

A bővítménycsomagok az Azure Storage-bővítménytárházból töltődnek le, és a bővítmény állapotfeltöltései az Azure Storage-ba kerülnek. Ha az ügynökök [támogatott](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzióját használja, nem kell engedélyeznie az Azure Storage-hoz való hozzáférést a virtuális gép régióban, mivel az ügynök segítségével átirányíthatja a kommunikációt az Azure fabric vezérlő ügynökkommunikációhoz. Ha az ügynök nem támogatott verzióját használja, engedélyeznie kell a kimenő hozzáférést az Azure storage-hoz az adott régióban a virtuális gépről.

> [!IMPORTANT]
> Ha a vendégtűzfal használatával letiltotta a hozzáférést a *168.63.129.16-hoz,* akkor a bővítmények a fentiektől függetlenül sikertelenek.

Az ügyintézők csak bővítménycsomagok és jelentési állapot letöltésére használhatók. Ha például egy bővítmény telepítést kell letölteni egy parancsfájlt a GitHubról (Egyéni parancsfájl), vagy hozzáférést kell biztosítania az Azure Storage -hoz (Azure Backup), akkor további tűzfal/hálózati biztonsági csoport portokat kell megnyitni. A különböző kiterjesztések különböző követelményekkel rendelkeznek, mivel önmagukban is alkalmazások. Az Azure Storage-hoz való hozzáférést igénylő bővítmények esetében engedélyezheti a hozzáférést az Azure NSG szolgáltatáscímkék [tárolóhoz](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)használatával.

Ügynökforgalmi kérelmek átirányításához a Linux-ügynök proxykiszolgáló-támogatással rendelkezik. Ez a proxykiszolgáló-támogatás azonban nem alkalmaz bővítményeket. Minden egyes bővítményt úgy kell konfigurálnia, hogy proxyval működjön.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felfedezése

Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. A teljes lista megtekintéséhez használja [az vm bővítményképlistát](/cli/azure/vm/extension/image#az-vm-extension-image-list). A következő példa a *Westus-helyen* elérhető összes bővítményt sorolja fel:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Virtuálisgép-bővítmények futtatása

Az Azure virtuálisgép-bővítmények meglévő virtuális gépeken futnak, ami akkor hasznos, ha konfigurációs módosításokat kell végrehajtania, vagy helyre kell állítania a kapcsolatot egy már üzembe helyezett virtuális számítógépen. Virtuálisgép-bővítmények is csomagban az Azure Resource Manager-sablon központi telepítések. Bővítmények használatával A Resource Manager-sablonok, Az Azure virtuális gépek üzembe helyezhetők és konfigurálhatók a telepítés utáni beavatkozás nélkül.

A következő módszerekkel egy meglévő virtuális gép egy bővítmény futtatásához használható.

### <a name="azure-cli"></a>Azure CLI

Az Azure VM-bővítmények futtathatók egy meglévő virtuális gép az [az vm-bővítmény készlet](/cli/azure/vm/extension#az-vm-extension-set) parancsával. A következő példa az egyéni parancsfájl-bővítményt egy *myResourceGroup* nevű erőforráscsoport myVM nevű virtuális gépén *futtatja.* Cserélje le a példa erőforráscsoport nevét, a virtuális\/gép nevét és a futtatandó parancsfájlt (https: /raw.githubusercontent.com/me/project/hello.sh) a saját adataira. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Ha a bővítmény megfelelően fut, a kimenet hasonló a következő példához:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure portál

Virtuálisgép-bővítmények egy meglévő virtuális gép az Azure Portalon keresztül alkalmazható. Jelölje ki a virtuális gép a portálon, válassza **a Bővítmények**, majd a **Hozzáadás**lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait.

Az alábbi képen a Linux egyéni parancsfájl-bővítmény telepítése látható az Azure Portalról:

![Egyéni parancsfájlkiterjesztés telepítése](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager-sablonhoz, és a sablon központi telepítésével hajthatók végre. Ha egy kiterjesztést egy sablonnal telepít, teljes mértékben konfigurált Azure-központi telepítések hozhat létre. Például a következő JSON egy Erőforrás-kezelő sablonból származik, amely egy terheléselosztással ellátott virtuális gépeket és egy Azure SQL-adatbázist telepít, majd telepít i . A virtuális gép bővítmény gondoskodik a szoftver telepítéséről.

További információt a teljes [Erőforrás-kezelő sablonban](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)talál.

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

Az Erőforrás-kezelő sablonok létrehozásáról az [Azure Resource Manager-sablonok létrehozása című témakörben olvashat bővebben.](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatainak védelme

Virtuálisgép-bővítmény futtatásakor szükség lehet bizalmas adatokat, például hitelesítő adatokat, tárfiók neveket és tárfiók hozzáférési kulcsokat. Számos virtuálisgép-bővítmény tartalmaz egy védett konfigurációt, amely titkosítja az adatokat, és csak a cél virtuális gépbelsejében fejti ki. Minden bővítmény rendelkezik egy adott védett konfigurációs sémával, és mindegyik részletesen a bővítmény-specifikus dokumentációban.

A következő példa a Linux egyéni parancsfájl-bővítményének egy példányát mutatja be. A végrehajtandó parancs hitelesítő adatokat tartalmaz. Ebben a példában a végrehajtandó parancs nincs titkosítva:

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

Ha a parancsot a **védett** konfigurációra helyezi át a tulajdonság **végrehajtásához,** az a végrehajtási karakterláncot biztosítja, ahogy az a következő példában látható:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan frissülnek az ügynökök és a bővítmények?

Az ügyintézők és bővítmények ugyanazt a frissítési mechanizmust. Egyes frissítések nem igényelnek további tűzfalszabályokat.

Ha egy frissítés elérhető, csak akkor lesz telepítve a virtuális gépre, ha módosulnak a bővítmények, és más virtuálisgép-modell is módosul, például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztikai tároló
- Vendég operációs rendszer titkai
- Virtuális gép mérete
- Hálózati profil

A közzétevők különböző időpontokban teszik elérhetővé a frissítéseket a régiók számára, így lehetséges, hogy különböző régiókban lévő virtuális gépek különböző verziókban vannak.

#### <a name="agent-updates"></a>Ügynök frissítések

A Linux virtuálisgép-ügynök egy csomagban tartalmazza *a kiépítési ügynök kódját* és *az extension handling kódot,* amely nem választható el egymástól. Letilthatja a *kiépítési ügynök,* ha azt szeretné, hogy az Azure-ban a felhő-init használatával. Ehhez lásd: [a cloud-init használata](../linux/using-cloud-init.md).

Az ügyintézők támogatott verziói automatikus frissítéseket használhatnak. Az egyetlen frissíthető kód a *bővítménykezelés kód,* nem a létesítési kód. A *kiépítési ügynök kód* egy-egy kód.

Az *Extension Handling kód* felelős az Azure-hálóval való kommunikációért és a virtuálisgép-bővítmények műveleteinek, például a telepítések, a jelentési állapot, az egyes bővítmények frissítésének és eltávolításának kezeléséért. A frissítések biztonsági javításokat, hibajavításokat és a *bővítménykezelési kód*továbbfejlesztéseit tartalmazzák.

Az ügynök telepítésekor létrejön egy szülődémon. Ez a szülő ezután létrehoz egy alárendelt folyamatot, amely a bővítmények kezelésére szolgál. Ha az ügynök számára elérhető egy frissítés, a rendszer letölti, a szülő leállítja a gyermekfolyamatot, frissíti, majd újraindítja azt. Ha probléma merül fel a frissítéssel kapcsolatban, a szülőfolyamat visszaáll az előző gyermekverzióra.

A szülőfolyamat nem frissíthető automatikusan. A szülő csak a distro csomag frissítésével frissíthető.

A futtatott verzió ellenőrzéséhez `waagent` ellenőrizze az alábbiak szerint:

```bash
waagent --version
```

A kimenet a következő példához hasonló:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Az előző példa kimenetében a szülő vagy a "csomag telepített verziója" *waLinuxAgent-2.2.17*

A "Cél állapotügynök" az automatikus frissítés verziója.

Erősen ajánlott, hogy mindig legyen automatikus frissítés az ügynökhöz, [az AutoUpdate.Enabled=y.](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) Ha ezt nem engedélyezi, akkor manuálisan kell frissítenie az ügynököt, és nem kell hiba- és biztonsági javításokat kapnia.

#### <a name="extension-updates"></a>Bővítmény frissítései

Ha elérhető egy bővítményfrissítés, a Linux-ügynök letölti és frissíti a bővítményt. Az automatikus bővítményfrissítések *következők: Minor* vagy *Gyorsjavítás.* A bővítmények kisebb *frissítéseket* a bővítmény kiépítésekor engedélyezheti vagy letilthatja. A következő példa bemutatja, hogyan lehet automatikusan frissíteni az alverziókat egy Erőforrás-kezelő sablonban *az autoUpgradeMinorVersion" parancával:':*

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

A legújabb kisebb kiadási hibajavítások lehívásához erősen ajánlott, hogy mindig válassza ki az automatikus frissítést a bővítmény központi telepítésekben. A biztonsági vagy kulcsfontosságú hibajavításokat tartalmazó gyorsjavítások frissítései ről nem lehet leiratkozni.

### <a name="how-to-identify-extension-updates"></a>A bővítményfrissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Annak megállapítása, hogy a bővítmény be van-e állítva az autoUpgradeMinorVersion funkcióval egy virtuális gépen

A virtuális gép modelljéből láthatja, hogy a bővítmény ki lett-e építve az "autoUpgradeMinorVersion" segítségével. Az ellenőrzéshez használja [az vm show](/cli/azure/vm#az-vm-show) és adja meg az erőforráscsoport és a virtuális gép nevét az alábbiak szerint:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

A következő példa kimenet azt mutatja, hogy *az autoUpgradeMinorVersion* *értéke true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Annak azonosítása, hogy mikor történt autoUpgradeMinorVersion

A bővítmény frissítésének megtekintéséhez tekintse át a virtuális gép ügynöknaplóit a */var/log/waagent.log .*

Az alábbi példában a virtuális gép *microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* telepítve. A *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

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

## <a name="agent-permissions"></a>Ügynöki engedélyek

A feladatok végrehajtásához az ügynöknek gyökérként kell *futnia.*

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények – problémamegoldás

Minden virtuálisgép-bővítmény hibaelhárítási lépésekkel rendelkezhet a bővítményre jellemzően. Például az egyéni parancsfájl-bővítmény használatakor a parancsfájl-végrehajtási részletek helyileg találhatók a virtuális gépen, ahol a bővítmény futott. A bővítményspecifikus hibaelhárítási lépéseket a bővítményspecifikus dokumentáció részletezi.

A következő hibaelhárítási lépések az összes virtuálisgép-bővítményre vonatkoznak.

1. A Linux-ügynöknapló ellenőrzéséhez tekintse meg azt a tevékenységet, amikor a bővítmény kiépítése folyamatban volt a */var/log/waagent.log fájlban.*

2. További részletekért tekintse meg a */var/log/azure/\<extensionName>*

3. Ellenőrizze a bővítményspecifikus dokumentációhiba-elhárítási szakaszokat a hibakódok, az ismert problémák stb.

3. Nézd meg a rendszernaplókat. Ellenőrizze, hogy vannak-e olyan műveletek, amelyek esetleg megzavarták a bővítményt, például egy másik alkalmazás hosszú ideig futó telepítése, amely kizárólagos csomagkezelői hozzáférést igényelt.

### <a name="common-reasons-for-extension-failures"></a>A kiterjesztési hibák gyakori okai

1. A bővítmények futtatása 20 min (kivételt képeznek a CustomScript-bővítmények, a Chef és a DSC, amelyek 90 mins-t használnak). Ha a központi telepítés meghaladja ezt az időt, időtúllépésként van megjelölve. Ennek oka lehet az alacsony erőforrás-virtuális gépek, más virtuálisgép-konfigurációk/indítási feladatok, amelyek nagy mennyiségű erőforrást fogyasztanak, miközben a bővítmény próbál kiépíteni.

2. A minimális előfeltételek nem teljesülnek. Egyes bővítmények a virtuális gép termékkészleteitől függnek, például a HPC-lemezképektől. A bővítmények bizonyos hálózati hozzáférési követelményeket igényelhetnek, például az Azure Storage-nak vagy a nyilvános szolgáltatásoknak való kommunikációt. További példák lehetnek a csomagtárolókhoz való hozzáférés, a lemezterület kifutása vagy a biztonsági korlátozások.

3. Exkluzív csomagkezelői hozzáférés. Bizonyos esetekben előfordulhat, hogy egy hosszú ideig futó virtuális gép konfigurációés a bővítmény telepítése ütközik, ahol mindketten kizárólagos hozzáférést igényelnek a csomagkezelőhöz.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Miután egy virtuálisgép-bővítményt futtattak egy virtuális gépellen, használja [az az vm get-instance-view használatával](/cli/azure/vm#az-vm-get-instance-view) a bővítmény állapotát az alábbiak szerint:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A kimenet hasonló a következő példakimenethez:

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

A bővítmény végrehajtási állapota az Azure Portalon is megtalálható. A bővítmény állapotának megtekintéséhez válassza a virtuális gép, válassza **a Bővítmények**lehetőséget, majd válassza ki a kívánt bővítményt.

### <a name="rerun-a-vm-extension"></a>Virtuálisgép-bővítmény ismétlése

Előfordulhatnak olyan esetek, amikor a virtuális gép bővítményt újra kell futtatni. A bővítmény taszthatja újra, távolítsa el, majd futtassa újra a bővítményt egy ön által választott végrehajtási módszerrel. Bővítmény eltávolításához használja [az vm kiterjesztés törlését](/cli/azure/vm/extension#az-vm-extension-delete) az alábbiak szerint:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Az Azure Portalon az alábbiak szerint is eltávolíthat egy bővítményt:

1. Válasszon ki egy virtuális gép.
2. Válassza **a Bővítmények lehetőséget**.
3. Válassza ki a kívánt kiterjesztést.
4. Válassza **az Eltávolítás lehetőséget.**

## <a name="common-vm-extension-reference"></a>Gyakori virtuálisgép-bővítmény-referencia

| Kiterjesztés neve | Leírás | További információ |
| --- | --- | --- |
| Egyéni parancsfájl-bővítmény Linuxhoz |Parancsfájlok futtatása egy Azure-beli virtuális gépen |[Egyéni parancsfájl-bővítmény Linuxhoz](custom-script-linux.md) |
| Virtuálisgép-hozzáférési bővítmény |Hozzáférés visszaszerzése egy Azure virtuális géphez |[Virtuálisgép-hozzáférési bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics bővítmény |Az Azure diagnosztika kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Az Azure VM Access bővítmény |Felhasználók és hitelesítő adatok kezelése |[VM Access-bővítmény Linuxhoz](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>További lépések

A virtuálisgép-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások áttekintése című témakörben olvashat bővebben.](overview.md)
