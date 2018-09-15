---
title: Az Azure DSC-bővítmény linuxhoz
description: Telepíti az OMI és a DSC-csomagokat, hogy az Azure Linux VM Desired State Configuration használatával kell konfigurálni.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 70280676453bd146102ca331daae038b947aab58
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632857"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-bővítmény linuxhoz (Microsoft.OSTCExtensions.DSCForLinux)

## <a name="overview"></a>Áttekintés

Desired State Configuration (DSC) egy felügyeleti platform, amely lehetővé teszi az informatikai RÉSZLEG felügyelheti és fejlesztési infrastruktúra mint kód konfigurációval.

DSCForLinux bővítmény közzétett és a Microsoft támogatja. A bővítmény az OMI és DSC-ügynök telepítése Azure-beli virtuális gépeken. DSC-bővítmény is elvégezheti a következő műveletek


- Annak érdekében, hogy a pull-konfigurációk az Azure Automation szolgáltatást (ExtensionAction regisztrálása) a Linux rendszerű virtuális gép az Azure Automation-fiók regisztrálása
- MOF-konfigurációk leküldése a Linux rendszerű virtuális gép (leküldéses ExtensionAction)
- A Linux rendszerű virtuális géphez lekérési kiszolgálójának beállítása annak érdekében, hogy a csomópont-konfiguráció (lekéréses ExtensionAction) lekéréses Meta MOF konfigurációjának alkalmazása
- A Linux rendszerű virtuális gépre (telepítése ExtensionAction) egyéni DSC-modulok telepítése
- Távolítsa el a Linuxos virtuális gép (ExtensionAction eltávolítása) egyéni DSC-modulok

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC Linux-bővítményt az összes támogatja a [bővítmény támogatott operációs rendszer a](https://azurewiki.cloudapp.netVMAgentExtension/extensionSupportedOSs) kivételével:

| Disztribúció | Verzió |
|---|---|
| CentOS Linux | 6.5-ös vagy újabb |
| Ubuntu| 12.04 LTS, 14.04 LTS, 16.04 LTS  |
| RHEL| 6.5-ös vagy újabb  |
| openSUSE| 13.1 és magasabb  |
| SUSE Linux Enterprise Server| 11 SP3 vagy újabb  |

  
 
### <a name="internet-connectivity"></a>Internetkapcsolat

A DSCForLinux bővítmény szükséges, hogy a céloldali virtuális gép csatlakozik az internethez. Ha például Register bővítmény kapcsolódnia kell az Automation szolgáltatás. Más műveletek, például lekéréses, lekéréses, a telepítés kapcsolódnia kell az azure ki-és github. Azt az ügyfél által megadott beállításoktól függ.

## <a name="extension-schema"></a>Bővítményséma

### <a name="11-public-configuration"></a>1.1-es nyilvános konfiguráció

A támogatott nyilvános konfiguráció-paraméterek a következők:

* `FileUri`: (nem kötelező, string) uri-ját a MOF fájl/Meta MOF fájl/egyéni erőforrás ZIP-fájlt.
* `ResourceName`: (nem kötelező, string) az egyéni erőforrás-modul neve
* `ExtensionAction`: (nem kötelező, string) megadja a bővítmény leírása. érvényes értékek: regisztrálja, leküldéses, kérje le, telepítse, eltávolítás. Ha nincs megadva, akkor számít leküldéses műveletként alapértelmezés szerint.
* `NodeConfigurationName`: (nem kötelező, string) a alkalmazni a csomópont-konfiguráció nevét.
* `RefreshFrequencyMins`: (nem kötelező, csak int) megadja, hogy milyen gyakran (percben) DSC a lekéréses kiszolgálóról szerzik be a konfiguráció. 
       Ha a lekérési kiszolgálón konfigurációja eltér a jelenlegivel célcsomóponton, a függőben lévő tárolóba másolja, és alkalmazza.
* `ConfigurationMode`: (nem kötelező, string) Itt adhatja meg, hogyan DSC kell alkalmazni a konfigurációt. Érvényes értékek a következők: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (nem kötelező, csak int) meghatározza, milyen gyakorisággal (percben) DSC biztosítja, hogy a konfiguráció a kívánt állapotban van.

> [!NOTE]
> Verzió < 2.3 használatakor mode paraméter megegyezik ExtensionAction. Mód úgy tűnik, hogy egy túlterhelt kifejezést. Ezért a félreértések elkerülése végett ExtensionAction használja a 2.3-as verzió frissítésétől kezdve. A visszamenőleges kompatibilitás érdekében a bővítmény és ExtensionAction is támogatja. 
>

### <a name="12-protected-configuration"></a>1.2 védett konfiguráció

Az alábbiakban az összes védett konfigurációs paraméterek:

* `StorageAccountName`: (nem kötelező, string) a tárfiókot, amely tartalmazza a fájl neve
* `StorageAccountKey`: (nem kötelező, csak karakterlánc), amely a fájl tartalmazza a tárfiók kulcsa
* `RegistrationUrl`: (nem kötelező, string) az Azure Automation-fiók URL-címe
* `RegistrationKey`: (nem kötelező, string) az Azure Automation-fiók hozzáférési kulcsa


## <a name="scenarios"></a>Forgatókönyvek

### <a name="register-to-azure-automation-account"></a>Az Azure Automation-fiók regisztrálása
Protected.JSON
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.JSON
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>MOF-konfigurációs fájl (az Azure Storage-fiók) alkalmazása a virtuális géphez

Protected.JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.JSON
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>MOF-konfigurációs fájl (a nyilvános tárolási) alkalmazása a virtuális géphez

public.JSON
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell-formátum
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Meta MOF-konfigurációs fájl (az Azure Storage-fiók) alkalmazása a virtuális géphez

Protected.JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.JSON
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Meta MOF-konfigurációs fájl (a nyilvános tárolási) alkalmazása a virtuális géphez
public.JSON
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell-formátum
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Egy egyéni erőforrás modul (ZIP-fájlt az Azure Storage-fiók) telepítése a virtuális géphez
Protected.JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Egy egyéni erőforrás modul (ZIP-fájlt a nyilvános tárolási) telepítése a virtuális géphez
public.JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell-formátum
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Távolítsa el a virtuális gép egy egyéni erőforrás-modul
public.JSON
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell-formátum
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Sablonok ideálisak, legalább egy üzembe helyezést követő konfiguráció például az Azure Automationhöz bevezetési igénylő virtuális gépek üzembe helyezésekor. 

A mintául szolgáló Resource Manager-sablon [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) és [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Az Azure Resource Manager-sablonnal kapcsolatos további részletekért látogasson el a [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

### <a name="21-using-azure-cliazure-cli"></a>2.1. Használatával [**Azure CLI-vel**] [azure-cli]
DSCForLinux bővítmény üzembe helyezése előtt konfigurálnia kell a `public.json` és `protected.json`, megfelelően azokat az eseteket, a 3.

#### <a name="211-classic"></a>2.1.1. Klasszikus
A Klasszikus módú Azure szolgáltatásfelügyelet módban is nevezik. Lehet váltani, futtatásával:
```
$ azure config mode asm
```

DSCForLinux bővítmény futtatásával telepítheti:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Ismerje meg, hogy a legújabb elérhető bővítmény-verzió, futtassa:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Átválthat az Azure Resource Manager módban futtatásával:
```
$ azure config mode arm
```

DSCForLinux bővítmény futtatásával telepítheti:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> Az Azure Resource Manager módban `azure vm extension list` nem érhető el a lépést.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Használatával [**Azure PowerShell-lel**] [azure-powershell]

#### <a name="221-classic"></a>2.2.1-es klasszikus

Bejelentkezhet az Azure-fiókjával (Azure szolgáltatásfelügyeleti módban) való futtatásával:

```powershell>
Add-AzureAccount
```

És üzembe helyezése DSCForLinux bővítmény futtatásával:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Meg kell változtatnia a $privateConfig és a különböző helyzetekben a fenti szakasz szerint $publicConfig tartalma 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="222resource-manager"></a>2.2.2.Resource Manager

Bejelentkezhet az Azure-fiókjával (Azure Resource Manager módban) való futtatásával:

```powershell>
Login-AzureRmAccount
```

Kattintson a [ **Itt** ](../../azure-resource-manager/powershell-azure-resource-manager.md) tudhat meg többet az Azure Resource Manager Azure PowerShell használatával.

DSCForLinux bővítmény futtatásával telepítheti:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Meg kell változtatnia a $privateConfig és a különböző helyzetekben a fenti szakasz szerint $publicConfig tartalma 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureRmVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kód chyby: 51 jelöli, vagy a disztribúció nem támogatott, vagy a művelet nem támogatott bővítményt.
Bizonyos esetekben DSC Linux-bővítmény nem sikerül telepítenie az OMI, ha OMI újabb verziója már megtalálható a gép. [hiba válasz: nem engedélyezett (000003) alacsonyabb szintű]



### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
Bővítmények kapcsolatos további információkért lásd: [virtuális gépi bővítmények és szolgáltatások Linux](features-linux.md).
