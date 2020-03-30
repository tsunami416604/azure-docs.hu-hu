---
title: Azure DSC-bővítmény Linuxhoz
description: Omi- és DSC-csomagok telepítése, hogy egy Azure Linux virtuális gép konfigurálható a kívánt állapot konfigurációhasználatával.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250620"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC kiterjesztés Linuxra (Microsoft.OSTCExtensions.DSCForLinux)

A kívánt állapotkonfiguráció (DSC) egy felügyeleti platform, amely segítségével kezelheti az informatikai és fejlesztési infrastruktúra konfigurációkódként.

> [!NOTE]
> A Linux dsc-bővítmény és az [Azure Monitor Linuxhoz való virtuálisgép-bővítménye](/azure/virtual-machines/extensions/oms-linux) jelenleg ütközik, és nem támogatott egymás melletti konfigurációban. Ne használja együtt a két megoldást ugyanazon a virtuális gépen.

A DSCForLinux bővítményt a Microsoft közzéteszi és támogatja. A bővítmény telepíti az OMI- és DSC-ügynököt az Azure virtuális gépeken. A DSC-bővítmény a következő műveleteket is végrehajthatja:


- Regisztrálja a Linux virtuális gépet egy Azure Automation-fiókba konfigurációk lekérése az Azure Automation szolgáltatásból (Register ExtensionAction).
- MOF-konfigurációk leküldéses a Linux vm (Push ExtensionAction).
- Meta MOF-konfiguráció alkalmazása a Linux virtuális gépre a lekéréses kiszolgáló konfigurálásához csomópontkonfiguráció lekérése (Pull ExtensionAction).
- Telepítse az egyéni DSC-modulokat a Linux virtuális gépre (Install ExtensionAction).
- Távolítsa el az egyéni DSC-modulokat a Linux virtuális gépről (ExtensionAction eltávolítása).

 

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC Linux-bővítmény támogatja az [Azure-ban jóváhagyott összes Linux-disztribúciót,](/azure/virtual-machines/linux/endorsed-distros) kivéve:

| Disztribúció | Verzió |
|---|---|
| Debian | Az összes verzió |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Internetkapcsolat

A DSCForLinux bővítmény megköveteli, hogy a cél virtuális gép csatlakozzon az internethez. A Register bővítmény például az Automation szolgáltatással való kapcsolatot igényel. Más műveletek, például a lekéréses, lekéréses, telepítési igényel kapcsolatot az Azure Storage és a GitHub. Ez az ügyfél által biztosított beállításoktól függ.

## <a name="extension-schema"></a>Bővítményséma

### <a name="public-configuration"></a>Nyilvános konfiguráció

Itt van az összes támogatott nyilvános konfigurációs paraméter:

* `FileUri`: (nem kötelező, karakterlánc) A MOF-fájl, a meta MOF-fájl vagy az egyéni erőforrás-zip fájl urija.
* `ResourceName`: (nem kötelező, karakterlánc) Az egyéni erőforrásmodul neve.
* `ExtensionAction`: (nem kötelező, karakterlánc) Megadja, hogy mit csinál egy bővítmény. Érvényes értékek: Regiszter, Leküldéses, Lekéréses, Telepítés és Eltávolítás. Ha nincs megadva, akkor alapértelmezés szerint leküldéses műveletnek minősül.
* `NodeConfigurationName`: (nem kötelező, karakterlánc) Az alkalmazandó csomópontkonfiguráció neve.
* `RefreshFrequencyMins`: (nem kötelező, int) Megadja, hogy a DSC milyen gyakran (percben) próbálja meg beszerezni a konfigurációt a lekéréses kiszolgálóról. 
       Ha a lekéréses kiszolgáló konfigurációja eltér a célcsomóponton lévő aktuálistól, a rendszer átmásolja a függőben lévő tárolóba, és alkalmazza.
* `ConfigurationMode`: (nem kötelező, karakterlánc) Megadja, hogyan kell alkalmazni a DSC-t a konfiguráció. Az érvényes értékek: ApplyOnly, ApplyAndMonitor és ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (nem kötelező, int) Megadja, hogy a DSC milyen gyakran (percben) biztosítsa, hogy a konfiguráció a kívánt állapotban legyen.

> [!NOTE]
> Ha a 2.3-nál korábbi verziót használja, a mód paraméter e-művelet megegyezik az ExtensionAction paraméterrel. Mód úgy tűnik, hogy egy túlterhelt kifejezés. A félreértések elkerülése érdekében az ExtensionAction a 2.3-as verziótól kezdve használatos. A visszamenőleges kompatibilitás érdekében a bővítmény támogatja a mode és az ExtensionAction műveletet is. 
>

### <a name="protected-configuration"></a>Védett konfiguráció

Az alábbiakban az összes támogatott védett konfigurációs paramétert itt található:

* `StorageAccountName`: (nem kötelező, karakterlánc) A fájlt tartalmazó tárfiók neve
* `StorageAccountKey`: (nem kötelező, karakterlánc) A fájlt tartalmazó tárfiók kulcsa
* `RegistrationUrl`: (nem kötelező, karakterlánc) Az Azure Automation-fiók URL-címe
* `RegistrationKey`: (nem kötelező, karakterlánc) Az Azure Automation-fiók hozzáférési kulcsa


## <a name="scenarios"></a>Forgatókönyvek

### <a name="register-an-azure-automation-account"></a>Azure Automation-fiók regisztrálása
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell formátum
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>MOF-konfigurációs fájl alkalmazása (Egy Azure-tárfiókban) a virtuális gépre

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell formátum
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>MOF-konfigurációs fájl alkalmazása (nyilvános tárolóban) a virtuális gépre

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell formátum
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Meta MOF konfigurációs fájl alkalmazása (Egy Azure-tárfiókban) a virtuális gépre

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell formátum
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Meta MOF konfigurációs fájl alkalmazása (nyilvános tárolóban) a virtuális gépre
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell formátum
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Egyéni erőforrásmodul (egy Azure-tárfiókban lévő zip-fájl) telepítése a virtuális gépre
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell formátum
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Egyéni erőforrásmodul (nyilvános tárolóban lévő zip-fájl) telepítése a virtuális gépre
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell formátum
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Egyéni erőforrásmodul eltávolítása a virtuális gépből
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell formátum
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. A sablonok ideálisak, ha egy vagy több olyan virtuális gépet telepít, amelyek üzembe helyezés utáni konfigurációt igényelnek, például az Azure Automation-be való bevezetést. 

A minta Resource Manager sablon [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) és [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Az Azure Resource Manager sablonról további információt az [Azure Resource Manager-sablonok készítése című témakörben talál.](../../azure-resource-manager/templates/template-syntax.md)


## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

### <a name="use-azure-cliazure-cli"></a>[Azure CLI][azure-cli] használata
A DSCForLinux bővítmény telepítése előtt `public.json` `protected.json` konfigurálja a 3.

#### <a name="classic"></a>Klasszikus

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A klasszikus üzembe helyezési módot Azure Service Management mode-nak is nevezik. A következő futással válthat a következő futással:
```
$ azure config mode asm
```

A DSCForLinux bővítményt a következő futtatásával telepítheti:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

A legújabb elérhető bővítményverzió megismeréséhez futtassa a következőt:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Az Azure Resource Manager módra a következő futtatásával válthat:
```
$ azure config mode arm
```

A DSCForLinux bővítményt a következő futtatásával telepítheti:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> Az Azure Resource `azure vm extension list` Manager módban egyelőre nem érhető el.
>

### <a name="use-azure-powershellazure-powershell"></a>[Azure PowerShell][azure-powershell] használata

#### <a name="classic"></a>Klasszikus

Az Azure-fiókba azure-szolgáltatáskezelési módban a következő futtatásával jelentkezhet be:

```powershell>
Add-AzureAccount
```

És telepítse a DSCForLinux kiterjesztést a következő futtatásával:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Módosíthatja a $privateConfig és $publicConfig tartalmát az előző szakasz különböző forgatókönyvei szerint.
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

#### <a name="resource-manager"></a>Resource Manager

Az Azure Resource Manager módban az Azure Resource Manager módban is bejelentkezhet az Azure Resource Manager-fiókba:

```powershell>
Login-AzAccount
```

Ha többet szeretne tudni arról, hogyan használhatja az Azure PowerShellt az Azure Resource Managerrel, olvassa [el az Azure-erőforrások kezelése az Azure PowerShell használatával.](../../azure-resource-manager/management/manage-resources-powershell.md)

A DSCForLinux bővítményt a következő futtatásával telepítheti:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Módosíthatja a $privateConfig és $publicConfig tartalmát az előző szakasz különböző forgatókönyvei szerint.
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
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény-végrehajtási kimenet a következő fájlba kerül:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Hibakód: Az 51 nem támogatott terjesztési vagy nem támogatott bővítményműveletet jelöl.
Bizonyos esetekben a DSC Linux kiterjesztés nem telepíti az OMI-t, ha az OMI egy magasabb verziója már létezik a gépen. [hibaválasz: (000003)Visszalépés nem engedélyezett]



### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, forduljon az Azure szakértőihez az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/community/) Másik lehetőségként egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/)és válassza **a Támogatás beszereznie**lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="next-steps"></a>További lépések
A bővítményekről további információt a [Virtuálisgép-bővítmények és -szolgáltatások Linuxhoz](features-linux.md)című témakörben talál.
