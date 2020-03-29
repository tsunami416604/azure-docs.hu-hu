---
title: Azure Cloud Shell Windows-felhasználóknak | Microsoft dokumentumok
description: Útmutató a Linux rendszereket nem ismerő felhasználók számára
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204139"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell a Cloud Shellben Windows-felhasználóknak

2018 májusában [módosításokat jelentettek be](https://azure.microsoft.com/blog/pscloudshellrefresh/) a PowerShellre az Azure Cloud Shellben.
Az Azure Cloud Shell PowerShell-élménye mostantól linuxos környezetben futtatja a [PowerShell Core](https://github.com/powershell/powershell) 6-ot.
Ezzel a módosítással előfordulhat, hogy a PowerShell-élmény a Cloud Shellben a Windows PowerShell-környezetben várhatóhoz képest némi különbséget mutat.

## <a name="file-system-case-sensitivity"></a>Fájlrendszer kis- és nagybetűk megkülönböztetése

A fájlrendszer nem érzékeny a kis- és nagybetűkre a Windows rendszerben, míg Linux on a fájlrendszer a kis- és nagybetűket.
Korábban, `file.txt` és `FILE.txt` úgy ítélték meg, hogy ugyanazt a fájlt, de most már úgy kell tekinteni, hogy a különböző fájlokat.
A fájlrendszerben megfelelő `tab-completing` burkolatot kell használni.
A PowerShell-specifikus `tab-completing` tapasztalatok, például a parancsmag nevei, paraméterei és értékei nem tartalmaznak kis- és nagybetűket.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>A Windows PowerShell aliasok és Linux-segédprogramok

Egyes meglévő PowerShell-aliasok neve megegyezik a beépített `cat`Linux-parancsokkal, például ,`ls`, `sort`, `sleep`, stb. A PowerShell Core 6-ban a beépített Linux-parancsokkal ütköző aliasokat eltávolították.
Az alábbiakban az eltávolított általános aliasokat, valamint azok megfelelő parancsait találjuk meg:  

|Eltávolított alias   |Egyenértékű parancs   |
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

## <a name="persisting-home"></a>A $HOME továbbra is fennáll

A korábbi felhasználók csak parancsfájlokat és más fájlokat tudtak megtartani a Cloud Drive-on.
Most a felhasználó $HOME könyvtára is megmarad a munkamenetek között.

## <a name="powershell-profile"></a>PowerShell-profil

Alapértelmezés szerint a felhasználó PowerShell-profilja nem jön létre.
A profil létrehozásához `PowerShell` hozzon `$HOME/.config`létre egy könyvtárat a területen.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

A `$HOME/.config/PowerShell`területen létrehozhatja profilfájljait `profile.ps1` `Microsoft.PowerShell_profile.ps1`- és/vagy .

## <a name="whats-new-in-powershell-core-6"></a>A PowerShell Core 6 újdonságai

A PowerShell Core 6 újdonságairól a [PowerShell-dokumentumok](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) és a [PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) első lépései blogbejegyzést.
