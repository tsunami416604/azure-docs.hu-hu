---
title: "Az Azure felhőalapú rendszerhéj (előzetes verzió) szolgáltatások PowerShell |} Microsoft Docs"
description: "Azure Cloud rendszerhéj PowerShell funkcióinak áttekintése"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: damaerte
ms.openlocfilehash: ffe5298678de6cc16af0ea29c7d887330f77589d
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell"></a>Szolgáltatások & eszközök PowerShell Azure Cloud rendszerhéj

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Szolgáltatások és az eszközök [Bash](features.md) is rendelkezésre áll.

A felhő rendszerhéj PowerShell futó `Windows Server 2016`.

## <a name="features"></a>Szolgáltatások

### <a name="secure-automatic-authentication"></a>Biztonságos automatikus hitelesítés

A felhő rendszerhéj PowerShell biztonságosan és automatikusan hitelesíti felhasználóifiók-hozzáférés az Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Adatmegőrzési fájlok munkamenetei között

A munkamenetek között a fájlok továbbra is fennáll, felhőalapú rendszerhéj bemutatja, hogyan csatlakoztatása az Azure fájlmegosztások első indításkor.
Ezt követően felhő rendszerhéj automatikusan elvégzi a tároló (mint csatlakoztatott `$home\clouddrive`) minden későbbi.
Mivel minden kérelmet a felhő rendszerhéj lefoglalni egy ideiglenes gép, fájlok kívül a `$home\clouddrive` és a gép állapota nem maradnak meg a munkamenetek között.

[További tudnivalók az Azure fájlmegosztások csatolása felhő rendszerhéj.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Az Azure meghajtó (Azure:)

A felhő rendszerhéj PowerShell elindul Azure meghajtón (`Azure:`).
Az Azure meghajtó lehetővé teszi, hogy könnyen felderítés és az Azure-erőforrások, például a számítási, hálózati, tárolási stb filesystem navigációs hasonló navigációs.
Továbbra is a megszokott [Azure PowerShell-parancsmagok](https://docs.microsoft.com/en-us/powershell/azure) kezelheti ezeket az erőforrásokat.
Az Azure-erőforrások, vagy közvetlenül az Azure portálon vagy az Azure PowerShell-parancsmagokkal végzett módosítások azonnal megjelennek az Azure meghajtó.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Környezetfüggő figyelése

- **Erőforrás gazdagépcsoport hatókörének beállítása**: Ha egy erőforrás-csoport elérési útja az Azure meghajtó kontextusában (`Azure:`), az erőforráscsoport neve automatikusan átkerül az Azure PowerShell-parancsmagokat.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: Ez a parancsmag listáját adja vissza a parancsok a környezetében az Azure meghajtó helyére vonatkozó (`Azure:`). Például azt illusztrálja, csak a tárolással kapcsolatos parancsok a felhasználó esetén`Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Gazdag PowerShell parancsfájl szerkesztése

PowerShell fájlok szerkesztésére VIM használatos (`.ps1,.psm1,.psd1`), automatikusan kapott szintaxis kiemelését és IntelliSense támogatás.
IntelliSense támogatási vim-beépülő modul, amely egy helyi példányával együttműködik keresztül van megvalósítva [PowerShell szerkesztő szolgáltatások](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Használjon `TAB` és befejezési (IntelliSense) kérjen a parancs nevét, a paraméterek nevei és a paraméterértékek, (adott esetben).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Bővíthető modell

Használatával [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), egyszerűen telepítse (és frissítése) az egyéni modulok és a parancsfájlok a [PowerShell-galériában](https://www.powershellgallery.com).
A telepítés után a modulok automatikusan megmaradnak felhő rendszerhéj-munkamenetek között.

> [!TIP]
> Felhasználók által telepített modulok lesznek mentve a `$Home\CloudDrive\.pscloudshell\WindowsPowerShell` mappát. Ez a mappa a szimbolikus hivatkozást a felhasználó Dokumentumok mappában jön létre (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>A Vendég virtuális gépek kezelése

Két beépített parancsok - használatával `Enter-AzureRmVM` és `Invoke-AzureRmVMCommand`, kezelhető távolról, az Azure virtuális gépeken.
Ezek a parancsok PowerShell távoli eljáráshívás épülnek, és az Azure virtuális gépek PowerShell kapcsolatot igényelnek.

## <a name="tools"></a>Eszközök

|**Kategória**    |**Name (Név)**                                 |
|----------------|-----------------------------------------|
|Azure-eszközök     |[Az Azure PowerShell (4.4.1.)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1)<br> [Az Azure CLI (2.0.20)](https://docs.microsoft.com/en-us/cli/azure/overview)|
|A szerkesztő szövege    |VIM<br> nano                             |
|A Package Manager |PowerShellGet<br> PackageManagement<br> npm<br> a pip |
|A verziókövetési rendszerrel  |git                                      |
|Adatbázisok       |[SQL Server-modul](https://www.powershellgallery.com/packages/SqlServer)<br> [Az Sqlcmd segédprogram használatával](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Az eszközök tesztelése      |Pester                                   |

## <a name="language-support"></a>Nyelvi támogatás

|**Nyelv**|**Verzió**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 és [6.0 (béta)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Következő lépések

[A PowerShell használatával a felhő rendszerhéj gyors üzembe helyezés](quickstart-powershell.md)

[További tudnivalók az Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
