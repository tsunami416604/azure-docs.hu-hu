---
title: Azure DSC-bővítmény Linux rendszerhez
description: A a következő módon telepíti a típusú és DSC-csomagokat, hogy az Azure Linux rendszerű virtuális gép a kívánt állapot-konfiguráció alapján legyen konfigurálva.
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
ms.openlocfilehash: c37b81e08e5d9f150081a9dc12af51175e3f590c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084721"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC Extension for Linux (Microsoft.OSTCExtensions.DSCForLinux)

A kívánt állapot-konfiguráció (DSC) egy olyan felügyeleti platform, amely lehetővé teszi az informatikai és fejlesztési infrastruktúra kezelését kóddal.

A DSCForLinux bővítményt a Microsoft közzétette és támogatja. A bővítmény telepíti a és a DSC-ügynököt az Azure Virtual Machines szolgáltatásban. A DSC-bővítmény a következő műveleteket is végrehajthatja


- A Linux rendszerű virtuális gép regisztrálása Azure Automation fiókba a konfigurációk Azure Automation szolgáltatásból való lekéréséhez (ExtensionAction regisztrálása)
- MOF-konfigurációk leküldése a Linux rendszerű virtuális gépre (leküldéses ExtensionAction)
- A meta MOF-konfiguráció alkalmazása a Linux rendszerű virtuális gépre a lekérési kiszolgáló konfigurálásához a csomópont-konfiguráció lekérése érdekében (lekéréses ExtensionAction)
- Egyéni DSC-modulok telepítése Linux rendszerű virtuális gépre (ExtensionAction telepítése)
- Egyéni DSC-modulok eltávolítása a Linux rendszerű virtuális gépre (ExtensionAction eltávolítása)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC Linux-bővítmény támogatja az [Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -ban támogatott összes Linux-disztribúciót, kivéve a következőket:

| Disztribúció | Version |
|---|---|
| Debian | minden verzió |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Internetkapcsolat

A DSCForLinux-bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez. A regisztrálási bővítménynek például az Automation szolgáltatáshoz kell kapcsolódnia. Más műveletek, például a lekéréses, a lekéréses telepítéshez az Azure Storage/GitHub-kapcsolat szükséges. Ez az ügyfél által megadott beállításoktól függ.

## <a name="extension-schema"></a>Bővítményséma

### <a name="11-public-configuration"></a>1,1 nyilvános konfiguráció

Az összes támogatott nyilvános konfigurációs paraméter:

* `FileUri`: (nem kötelező, karakterlánc) a MOF-fájl/meta MOF-fájl/egyéni erőforrás ZIP-fájljának URI-ja.
* `ResourceName`: (nem kötelező, karakterlánc) az egyéni erőforrás-modul neve
* `ExtensionAction`: (nem kötelező, karakterlánc) megadja a bővítményt. érvényes értékek: Regisztráció, küldés, lekérés, telepítés, eltávolítás. Ha nincs megadva, a rendszer alapértelmezés szerint leküldéses műveletnek tekinti.
* `NodeConfigurationName`: (nem kötelező, karakterlánc) az alkalmazandó csomópont-konfiguráció neve.
* `RefreshFrequencyMins`: (opcionális, int) Megadja, hogy a DSC milyen gyakran próbálja meg beolvasni a konfigurációt a lekérési kiszolgálóról. 
       Ha a lekérési kiszolgálón a konfiguráció eltér az aktuálistól, a rendszer átmásolja a függőben lévő tárolóba, és alkalmazza azokat.
* `ConfigurationMode`: (nem kötelező, karakterlánc) Megadja, hogy a DSC hogyan alkalmazza a konfigurációt. Érvényes értékek a következők: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcionális, int) Megadja, hogy milyen gyakran (percben) a DSC biztosítja, hogy a konfiguráció a kívánt állapotban legyen.

> [!NOTE]
> Ha < 2,3-es verziót használ, a mode paraméter ugyanaz, mint a ExtensionAction. A mód úgy tűnik, hogy túlterhelt kifejezés. Ezért a félreértések elkerülése érdekében a ExtensionAction a 2,3-es verziótól kezdődően használatos. A visszamenőleges kompatibilitás érdekében a bővítmény támogatja a módot és a ExtensionAction is. 
>

### <a name="12-protected-configuration"></a>1,2 védett konfiguráció

A támogatott védett konfigurációs paraméterek a következők:

* `StorageAccountName`: (nem kötelező, karakterlánc) a fájlt tartalmazó Storage-fiók neve
* `StorageAccountKey`: (nem kötelező, karakterlánc) a fájlt tartalmazó Storage-fiók kulcsa
* `RegistrationUrl`: (nem kötelező, karakterlánc) a Azure Automation-fiók URL-címe
* `RegistrationKey`: (nem kötelező, karakterlánc) a Azure Automation fiók hozzáférési kulcsa


## <a name="scenarios"></a>Forgatókönyvek

### <a name="register-to-azure-automation-account"></a>Regisztrálás Azure Automation fiókba
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>MOF konfigurációs fájl alkalmazása (Azure Storage-fiókban) a virtuális géphez

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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>MOF konfigurációs fájl (nyilvános tárolóban) alkalmazása a virtuális gépre

public.json
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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Meta MOF konfigurációs fájl alkalmazása (Azure Storage-fiókban) a virtuális géphez

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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Meta MOF konfigurációs fájl (nyilvános tárolóban) alkalmazása a virtuális gépre
public.json
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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Egyéni erőforrás-modul (ZIP-fájl az Azure Storage-fiókban) telepítése a virtuális gépre
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Egyéni erőforrás-modul (ZIP-fájl nyilvános tárolóban) telepítése a virtuális gépre
public.json
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Egyéni erőforrás-modul eltávolítása a virtuális gépről
public.json
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

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a telepítés utáni konfigurációt igénylik, például Azure Automation bevezetést. 

A minta Resource Manager-sablon a [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) és [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Azure Resource Manager sablonnal kapcsolatos további információkért látogasson el a [szerzői Azure Resource Manager sablonok](../../azure-resource-manager/resource-group-authoring-templates.md)című webhelyre.


## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

### <a name="21-using-azure-cliazure-cli"></a>2.1. Az [**Azure CLI**] használata [Azure-CLI]
A DSCForLinux-bővítmény telepítése előtt konfigurálnia kell a `public.json` és `protected.json`a, a 3. szakasz különböző forgatókönyvei alapján.

#### <a name="211-classic"></a>2.1.1. Klasszikus
A klasszikus módot Azure Service Management üzemmódnak is nevezik. A következő futtatásával válthat:
```
$ azure config mode asm
```

A DSCForLinux bővítményt a futtatásával is telepítheti:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Az elérhető legújabb bővítmény-verzió megismeréséhez futtassa a következőt:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
A Azure Resource Manager módba való váltáshoz futtassa a következőt:
```
$ azure config mode arm
```

A DSCForLinux bővítményt a futtatásával is telepítheti:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> Azure Resource Manager módban `azure vm extension list` jelenleg nem érhető el.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. A [**Azure PowerShell**] [Azure-PowerShell] használata

#### <a name="221-classic"></a>2.2.1 klasszikus

A következő futtatásával jelentkezhet be az Azure-fiókjába (Azure Service Management üzemmód):

```powershell>
Add-AzureAccount
```

És a DSCForLinux-bővítmény üzembe helyezéséhez futtassa a következőket:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Módosítania kell a $privateConfig tartalmát, és $publicConfig a fenti szakasz különböző forgatókönyvei szerint 
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

#### <a name="222resource-manager"></a>2.2.2. Resource Manager

A következő futtatásával jelentkezhet be az Azure-fiókjába (Azure Resource Manager módba):

```powershell>
Login-AzAccount
```

Kattintson [**ide**](../../azure-resource-manager/manage-resources-powershell.md) , ha többet szeretne megtudni a Azure PowerShell és a Azure Resource Manager használatáról.

A DSCForLinux bővítményt a futtatásával is telepítheti:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Módosítania kell a $privateConfig tartalmát, és $publicConfig a fenti szakasz különböző forgatókönyvei szerint 
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

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Hibakód: a 51 vagy nem támogatott disztribúciót, vagy nem támogatott bővítményi műveletet jelöl.
Bizonyos esetekben a DSC linuxos bővítmény nem tudja telepíteni a következőt, ha már létezik a-ben a következőben:. [hiba válasza: (000003) A visszalépés nem engedélyezett]



### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
