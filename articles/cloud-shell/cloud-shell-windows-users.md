---
title: Az Azure Cloud Shell a Windows-felhasználók |} A Microsoft Docs
description: Útmutató a felhasználók számára, akik nem ismeri a Linux rendszereket
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645036"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell az Azure Cloud Shell a Windows-felhasználók

A 2018 május, a módosításra [bejelentett](https://azure.microsoft.com/blog/pscloudshellrefresh/) PowerShell az Azure Cloud Shellben.
A PowerShell-tapasztalata az Azure Cloud Shell most futtatások [PowerShell Core 6-os](https://github.com/powershell/powershell) Linux-környezetben.
Ezzel lehet a PowerShell-tapasztalata a Cloud Shellben egy Windows PowerShell által várt képest néhány eltérés tapasztalható.

## <a name="file-system-case-sensitivity"></a>Fájl rendszer kis-és nagybetűk

A fájlrendszer az Windows, a kis-és, mivel a Linux, a fájlrendszer kis-és nagybetűket.
Korábban `file.txt` és `FILE.txt` tekintették ugyanazt a fájlt, de most kell a különböző fájlok minősülnek.
Kell használni a megfelelő kis-és közben `tab-completing` a fájlrendszerben.
PowerShell-specifikus során lép fel, mint például `tab-completing` parancsmagok neveivel, paraméterek és értékek, nem különböznek.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell-aliasok és Linux-segédeszközöket

Néhány meglévő PowerShell-aliasok rendelkezik az azonos nevű beépített Linux-parancsok, például `cat`,`ls`, `sort`, `sleep`stb. A PowerShell Core 6-os aliasról, amelyek ütköznek a beépített Linux-parancsok el lettek távolítva.
Az alábbiakban gyakori aliasról, amelyek el lettek távolítva, valamint a hasonló parancsok:  

|Alias eltávolítva   |Egyenértékű parancs   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Persisting $HOME

Korábbi felhasználók csak sikerült megőrizni, parancsprogramokat és egyéb felhőalapú Meghajtójukra fájlokat.
Most a felhasználó $HOME könyvtár konzisztenciája is állandó munkamenetek között.

## <a name="powershell-profile"></a>PowerShell-profilt

Alapértelmezés szerint egy profil PowerShell nem jön létre.
A profil, hozzon létre egy `PowerShell` könyvtár alatt `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

A `$HOME/.config/PowerShell`, hozhat létre a profilfájlokat - `profile.ps1` és/vagy `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Újdonságok a PowerShell Core 6-os

Újdonságok a PowerShell Core 6-os kapcsolatos további információkért tekintse a [PowerShell docs](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) és a [első lépései a PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogbejegyzést.
