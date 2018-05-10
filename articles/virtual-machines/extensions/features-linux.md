---
title: Az Azure Virtuálisgép-bővítmények és a Linux funkcióit |} Microsoft Docs
description: Ismerje meg, milyen bővítmények érhetők el, mi akkor adja meg, vagy javítania szerint csoportosítva, az Azure virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 760f832bc12bccbf1cce77db25bf60413ad9a36b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuálisgép-bővítmények és a Linux funkcióit

Az Azure virtuális gép (VM) a program kis alkalmazásokat, amelyek telepítés utáni konfigurációs és automatizálási feladatok Azure virtuális gépeken. Ha például egy virtuális géphez szükséges Szoftvertelepítés, a víruskereső védelmet, vagy saját parancsfájl futtatása, a Virtuálisgép-bővítmény is használható. Az Azure Virtuálisgép-bővítmények az Azure parancssori felület, PowerShell, az Azure Resource Manager-sablonok és az Azure-portálon is futtatható. Bővítmények mellékelhető egy új virtuális gép üzembe helyezéséhez, vagy bármely létező rendszert futtathat.

Ez a cikk áttekintést Virtuálisgép-bővítmények, Azure Virtuálisgép-bővítmények használatára vonatkozó Előfeltételek és útmutatás észleléséhez, kezeléséhez, és távolítsa el a Virtuálisgép-bővítmények. Ez a cikk általános információkat nyújt, sok Virtuálisgép-bővítmények érhetők el, mert egyes potenciálisan egyedi konfigurációval. Bővítmény felhasználóspecifikus adatainak található minden a dokumentumban a egyéni bővítmény egyedi.

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure Virtuálisgép-bővítmények érhetők el, az adott használati eset. Néhány példa:

- A DSC-bővítményt a virtuális gép Linux PowerShell kívánt állapot konfiguráció alkalmazása. További információkért lásd: [Azure kívánt állapot konfigurációs kiterjesztése](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurálhatja a virtuális gép és a Microsoft Monitoring Agent Virtuálisgép-bővítmény figyelését. További információkért lásd: [a Linux virtuális gépek figyelése](../linux/tutorial-monitoring.md).
- Az Azure infrastruktúra Chef vagy Datadog kiterjesztésű figyelés konfigurálása. További információkért lásd: a [Chef docs](https://docs.chef.io/azure_portal.html) vagy [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Folyamatokra vonatkozó bővítmények mellett egy egyéni parancsprogramok futtatására szolgáló bővítmény érhető el a Windows és a Linux virtuális gépekhez. Az egyéni parancsprogramok futtatására szolgáló bővítmény Linux lehetővé teszi, hogy a virtuális gép futtatásához Bash parancsfájlok. Egyéni parancsfájlok túl milyen natív Azure tooling biztosíthat beállításokra van szükség Azure központi telepítések tervezése során hasznosak. További információkért lásd: [Linux virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény](custom-script-linux.md).

## <a name="prerequisites"></a>Előfeltételek

Kezeli a bővítményt a virtuális Gépre, az Azure Linux-ügynök telepítve van szükség. Néhány egyéni bővítményt rendelkezik Előfeltételek, például az erőforrások és a függőségek elérését.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure Virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric controller közötti kommunikációt. A Virtuálisgép-ügynök telepítése és kezelése az Azure virtuális gépeken, beleértve a Virtuálisgép-bővítmények futtató sok működési jellemzői felelős. Az Azure Virtuálisgép-ügynök telepítve van az Azure piactéren elérhető rendszerkép, és támogatott operációs rendszeren manuálisan telepíthető. Az Azure Linux Virtuálisgép-ügynök a Linux-ügynök néven ismert.

Információ a támogatott operációs rendszerek és a telepítési utasításokat: [Azure virtuális gépi ügynöke](agent-linux.md).

#### <a name="supported-agent-versions"></a>Támogatott ügynökverziók

Így biztosíthatja a legjobb élményben legyen részük, az ügynök minimális verziója van. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Támogatott operációs rendszerei között

A Linux-ügynök fut több operációs azonban a bővítmények keretrendszer van korlátozva az operációs rendszer számára, hogy a bővítmények. További információ: [Ez a cikk] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Néhány bővítmény nem támogatottak minden operációs rendszer között, és bocsáthat *hiba kód 51, a "Nem támogatott az operációs rendszer"*. Támogathatóság egyedi bővítmény dokumentációjában.

#### <a name="network-access"></a>Hálózati hozzáférés

Bővítménycsomagok letölteni az Azure Storage bővítménytárban, és Azure Storage feladott bővítmény állapota feltöltések. Ha [támogatott](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) az ügynökök verziója, nem kell hozzáférést a virtuális gép régióban Azure Storage hasonlóan az ügynök a kommunikációs átirányítja az Azure fabric controller ügynök-kommunikációhoz. Ha az ügynök egy nem támogatott verzióját, szüksége az Azure storage régió kimenő hozzáférés engedélyezése a virtuális gépről.

> [!IMPORTANT]
> Ha a letiltott hozzáférés *168.63.129.1* a Vendég tűzfalat használ, akkor bővítmények sikertelen függetlenül a fent.

Ügynökök csak bővítménycsomagok és állapotjelentést letöltésére használható. Például ha egy bővítmény telepítési le kell töltenie egy parancsfájlt a Githubról (egyéni parancsfájl), vagy meg kell majd elérni az Azure Storage (az Azure Backup), további tűzfal és a hálózati biztonsági csoport portokat kell megnyitni. Egyes bővítmények különféle eltérő követelmények vonatkoznak, lehet, mert a saját jobbra lévő alkalmazások. Azure Storage hozzáférést igénylő bővítményekhez, engedélyezheti az Azure NSG szolgáltatás címkék használatával [tárolási](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Proxykiszolgáló-támogatás számára ügynök forgalom kérelmek átirányítása a Linux-ügynök. A proxykiszolgáló-támogatás azonban nem alkalmazza a bővítmények. Minden egyes bővítmény proxy működéséhez be kell állítania.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése

Azure virtuális gépekhez való használatra számos különböző Virtuálisgép-bővítmények érhetők el. Teljes listájának megtekintéséhez használja [az virtuálisgép-bővítmény képlistában](/cli/azure/vm/extension/image#az-vm-extension-image-list). Az alábbi példa felsorolja az összes rendelkezésre álló bővítményeket a *westus* helye:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Futtassa a Virtuálisgép-bővítmények

Azure Virtuálisgép-bővítmények futtassa a meglévő virtuális gépeken, amelyek akkor hasznos, ha a konfigurációs módosításokat, vagy állítsa helyre a kapcsolatot egy már telepített virtuális gépen kell. Virtuálisgép-bővítmények is Azure Resource Manager sablon központi telepítéseket is telepíthet. Bővítmények a Resource Manager-sablonok segítségével Azure virtuális gépeken is telepítését és konfigurálását telepítés utáni beavatkozás nélkül.

Az alábbi módszerek segítségével egy bővítmény egy meglévő virtuális gép futtatásához.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az Azure Virtuálisgép-bővítmények is futtathatók a meglévő virtuális a [az virtuálisgép-bővítmény készlet](/cli/azure/vm/extension#az-vm-extension-set) parancsot. A következő példa egy nevű virtuális gép fut az egyéni parancsprogramok futtatására szolgáló bővítmény *myVM* erőforráscsoportban nevű *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Ha a kiterjesztés megfelelően fut, a kimenete a következőhöz hasonló:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Virtuálisgép-bővítmények egy meglévő virtuális gépre az Azure portálon keresztül is alkalmazható. Válassza ki a virtuális Gépet a portálon, válassza a **bővítmények**, majd jelölje be **Hozzáadás**. Válassza ki a bővítmény szeretné, hogy a rendelkezésre álló bővítmények a listából, és kövesse a varázsló utasításait.

A következő kép bemutatja az Azure-portálról Linux egyéni parancsprogramok futtatására szolgáló bővítmény telepítése:

![Egyéni parancsfájl-kiterjesztés telepítése](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények felvehető az Azure Resource Manager-sablon és a sablon a központi telepítés végrehajtása. Egy bővítmény a sablon telepítésekor hozhat létre teljesen konfigurált Azure-környezetekhez. Például a következő JSON származik a Resource Manager-sablon, amely telepíti az elosztott terhelésű virtuális gépek és az Azure SQL-adatbázis, majd telepíti a .NET Core alkalmazást minden egyes virtuális gépen. A Virtuálisgép-bővítmény gondoskodik a szoftver telepítését.

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

Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatvédelem

Amikor futtat egy Virtuálisgép-bővítmény, akkor lehet szükség például a hitelesítő adatokat, a tárfiókok neve és a fiók tárelérési kulcsok bizalmas információkat szeretne. Több Virtuálisgép-bővítmények közé tartozik a védett konfigurációkat, amelyek titkosítja az adatokat, és csak visszafejti a cél virtuális gép belül. Mindegyik bővítményt egy adott védett konfigurációs séma van, és az egyes részleteit a bővítmény-specifikus dokumentációját.

A következő példa bemutatja az egyéni parancsprogramok futtatására szolgáló bővítmény példányának Linux. A parancs végrehajtásának olyan hitelesítő adatokat tartalmaz. Ebben a példában a parancs végrehajtása nem titkosított:

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

Helyezze át a **végrehajtandó parancs** tulajdonságot a **védett** konfigurációs titkosítja a végrehajtási karakterlánc a következő példában látható módon:

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

Az ügynökök és a bővítmények ossza meg az azonos frissítési mechanizmus. Néhány frissítést nem szükséges további tűzfalszabályokat.

Frissítés érhető el, ha csak telepíti a rendszer a virtuális Gépre van bővítmények módosítva lett, és más virtuális gép modellmódosításokkal többek között:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztika tároló
- Vendég operációs rendszer titkos kulcsok
- Virtuális gép mérete
- Hálózati profil

Közzétevők frissítések számára elérhetővé tenni régiók különböző időpontokban, így a virtuális gépek különböző verzióin különböző régiókban is lehet.

#### <a name="agent-updates"></a>Frissítések

A Linux Virtuálisgép-ügynök tartalmazza *kiépítési ügynök kód* és *kiterjesztés kezelése kód* egy csomag, amely nem választhatók. Letilthatja a *kiépítési ügynök* kívánt Azure-ban felhő inicializálás kiépítését. Ehhez tekintse meg a [használatával a felhő inicializálás](../linux/using-cloud-init.md).

Az ügynökök támogatott verzióit használhatja az automatikus frissítések. A következő frissíthető csak: a *kiterjesztés kezelése kód*, nem a kiépítési kódot. A *kiépítési ügynök kód* egyszeri futtatás kód.

A *kiterjesztés kezelése kód* kommunikál az Azure-hálót, és kezelése, mint a virtuális gép bővítményei műveletek telepíti, jelentéskészítési állapot, az egyes bővítmények frissítése, és eltávolítja azokat a felelős. Biztonsági javítások, hibajavításokat tartalmaz, és továbbfejlesztett szerepelnek a *kiterjesztés kezelése kód*.

Ha az ügynök telepítve van, a szülő démon jön létre. A szülő majd indít egyik gyermekfolyamata, amely bővítmények kezelésére szolgál. Ha egy frissítés érhető el az ügynököt, letöltés, a szülő a gyermekfolyamat leállítása, akkor frissíti, majd újraindítja. Kell a frissítés problémáját, a szülő folyamat visszaállítja a gyermek előző verziójára.

A szülő folyamat nem lehet automatikusan frissíteni. A szülő csak distro Csomagfrissítés frissítheti.

Annak ellenőrzése, mely verzióját futtatja, ellenőrizze, hogy a `waagent` az alábbiak szerint:

```bash
waagent --version
```

A kimenet a következő példához hasonló:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Az előző példa a kimenetre, a szülő vagy a "csomag telepített verziója" van *WALinuxAgent-2.2.17*

A "cél állapot ügynök" az automatikus frissítési verziója.

Javasoljuk, hogy mindig legyen automatikus frissítés az ügynökhöz [AutoUpdate.Enabled=y](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent). Nem a szeretne rögzíteni, az ügynök manuális frissítése engedélyezett azt jelenti, és nem kérdezhető le a hibát, és a biztonsági javítások.

#### <a name="extension-updates"></a>Bővítmény frissítések

Egy bővítmény frissítése nem érhető el, a Linux-ügynök tölti le, és frissíti a bővítményt. Automatikus bővítmény frissítései vagy *kisebb* vagy *gyorsjavítás*. Részt vevő, vagy kikapcsolja a bővítmények *kisebb* frissíti, amikor a bővítményt. A következő példa bemutatja, hogyan automatikus frissítése a Resource Manager sablonokban alverziót *autoupgrademinorversion elemének értéke pedig ": true,"*:

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

Ahhoz, hogy a legújabb kiadási kisebb hibák javításával, javasoljuk, hogy mindig választotta automatikus frissítés a bővítmény telepítések esetén. Gyorsjavítás-frissítések, biztonsági vagy kulcs hibajavításokat tartalmaz, amelyben nem kapcsolható.

### <a name="how-to-identify-extension-updates"></a>Bővítmény frissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Ha a kiterjesztés van állítva az autoupgrademinorversion elemének értéke pedig a virtuális gép azonosítása

Ha a bővítmény ki volt építve "autoUpgradeMinorVersion" tekintheti meg a virtuális gép modellből. Ellenőrzéséhez használja [az vm megjelenítése](/cli/azure/vm#az-vm-show) , és adja meg az erőforráscsoportot és a virtuális gép neve az alábbiak szerint:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Az alábbi példa kimenetben látható, melyet *autoUpgradeMinorVersion* értéke *igaz*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Amikor egy autoUpgradeMinorVersion történt azonosítása

Ha egy frissítést adunk ki a bővítmény történt megtekintéséhez tekintse át az ügynököt a virtuális gép egyidejű bejelentkezik */var/log/waagent.log*.

Az alábbi példában a virtuális gép kellett *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* telepítve. A gyorsjavítás volt elérhető *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027*:

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

## <a name="agent-permissions"></a>Ügynök engedélyek

A feladatok végrehajtásához az futnia kell az ügynököt *legfelső szintű*.

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibaelhárítása

Minden egyes Virtuálisgép-bővítmény hibaelhárítási lépések az adott a kiterjesztést is rendelkezhet. Például az egyéni parancsprogramok futtatására szolgáló bővítmény használatakor parancsfájl végrehajtásának részletes adatai található helyileg a virtuális gép, amelyen a bővítmény futott. Bővítmény-specifikus hibaelhárítási lépéseket részletes leírást talál bővítmény vonatkozó dokumentációt.

A következő hibaelhárítási lépéseket az összes Virtuálisgép-bővítmények vonatkozik.

1. A Linux-ügynök naplóját ellenőrzéséhez tekintse meg a tevékenység, ha a kiterjesztés lett létre a */var/log/waagent.log*

2. A tényleges bővítmény további részleteket a naplókban   */var/jelentkezzen/azure /<extensionName>*

3. Ellenőrizze a bővítmény-specifikus dokumentációját hibaelhárítási szakaszok kódok, ismert problémák stb.

3. Tekintse meg a rendszernaplót. Ellenőrizze, hogy előfordulhat, hogy rendelkezik befolyásolható, a kiterjesztéssel, például egy másik alkalmazás kizárólagos package manager hozzáférés szükséges egy hosszú ideig tartó telepített egyéb műveleteket.

### <a name="common-reasons-for-extension-failures"></a>A bővítmény hibák leggyakoribb okai

1. Bővítmények rendelkezik futtatásához 20 perc (Kivételek a következők a CustomScript bővítmények, Chef, és 90 perc rendelkező DSC). Ha a központi telepítés meghaladja a megadott idő, azt egy időtúllépés van megjelölve. A ennek oka az okozhatja, hogy kevés az erőforrás virtuális gépeket, más virtuális gép konfigurációk/kezdő feladatok nagy mennyiségű erőforrás fel, megőrizve a bővítmény próbál kiépítéséhez.

2. Minimális előfeltételek nem teljesültek. Néhány bővítmény tartalmazhat függőségeket VM SKU, például a HPC-lemezképeket. Bővítmények szükség lehet egyes hálózati hozzáférési követelmények, például az Azure Storage- vagy nyilvános szolgáltatásokhoz való kommunikáció során. További példák lehet csomag tárházak találhatók, kevés a szabad lemezterület, vagy a biztonsági korlátozások a hozzáférést.

3. Kizárólagos package manager hozzáférést. Bizonyos esetekben felmerülhet a hosszú ideig futó Virtuálisgép-konfiguráció és a bővítmény telepítése ütköző, ahol mindkét van szükségük a Csomagkezelő kizárólagos hozzáférést.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Miután egy Virtuálisgép-bővítmény futtatása a virtuális gépek elleni, a [get-példány-nézet az vm](/cli/azure/vm#az-vm-get-instance-view) vissza a bővítmény állapotát az alábbiak szerint:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A kimeneti hasonlít a következő egy példa a kimenetre:

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

Bővítmény végrehajtási állapotát az Azure portálon is található. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális Gépet, kattintson a **bővítmények**, majd jelölje ki a kívánt bővítményt.

### <a name="rerun-a-vm-extension"></a>Futtassa újra a Virtuálisgép-bővítmény

Előfordulhatnak olyan esetekben, ahol egy Virtuálisgép-bővítmény futtatható kell. Egy bővítmény akkor távolítsa el, majd futtassa újból a kiterjesztés egy végrehajtási módszer az Ön által választott újrafuttathatja. Egy bővítmény eltávolításához használja [az virtuálisgép-bővítmény törlése](/cli/azure/vm/extension#az-vm-extension-delete) az alábbiak szerint:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Is eltávolíthatja kiterjesztése az Azure-portálon az alábbiak szerint:

1. Jelöljön ki egy virtuális Gépet.
2. Válasszon **bővítmények**.
3. Jelölje ki a kívánt bővítményt.
4. Válasszon **eltávolítása**.

## <a name="common-vm-extension-reference"></a>Virtuálisgép-bővítmény közös hivatkozása

| Bővítmény neve | Leírás | További információ |
| --- | --- | --- |
| Linux rendszeren egyéni parancsprogramok futtatására szolgáló bővítmény |Parancsfájlok futtatásához egy Azure virtuális gépen |[Linux rendszeren egyéni parancsprogramok futtatására szolgáló bővítmény](custom-script-linux.md) |
| Virtuálisgép-hozzáférési bővítmény |Újra hozzáférést nyerni egy Azure virtuális gépen |[Virtuálisgép-hozzáférési bővítmény](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-bővítményt |Az Azure Diagnostics kezelése |[Azure Diagnostics-bővítményt](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Az Azure virtuális gép hozzáférési bővítmény |Felhasználók és a hitelesítő adatok kezelése |[Linux virtuális gép hozzáférési bővítmény](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>További lépések

További információ a Virtuálisgép-bővítmények: [Azure virtuális gép bővítményeket és szolgáltatásokat – áttekintés](overview.md).
