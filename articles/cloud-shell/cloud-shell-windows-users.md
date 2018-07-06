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
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861768"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell az Azure Cloud Shell a Windows-felhasználók

A 2018 május, a módosításra [bejelentett](https://azure.microsoft.com/blog/pscloudshellrefresh/) PowerShell az Azure Cloud Shellben.  A PowerShell-tapasztalata az Azure Cloud Shellben már PowerShell Core 6-os Linux rendszeren.
Ezzel vannak a PowerShell Cloud Shell, amelyek a Windows PowerShell 5.1 várt eltérőek lehetnek az egyes funkcióit.

## <a name="case-sensitivity"></a>Kis-és nagybetűk

A Windows a fájlrendszer az kis-és nagybetűket.  A Linux a fájlrendszer az kis-és nagybetűket.
Ez azt jelenti, hogy korábban `file.txt` és `FILE.txt` ugyanebben a fájlban tekinthető, most kell a különböző fájlok minősülnek.
Kell használni a megfelelő kis-és közben `tab` befejezése a fájlrendszerben.  PowerShell-specifikus során lép fel, mint például `tab` parancsmagok nem különböznek. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Windows PowerShell alias vs Linux segédprogramok

Meglévő parancsok, a Linux, mint `ls`, `sort`, és `sleep` PowerShell aliasaikat élvez elsőbbséget.  Az alábbiakban gyakori eltávolított aliasok, valamint a megfelelő parancsokat:  

|Alias eltávolítva   |Egyenértékű parancs   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>$Home vs $home\clouddrive megőrzése

A felhasználók számára, akik megőrzött szkriptek és a felhőalapú Meghajtójukra egyéb fájlokat a $HOME könyvtár konzisztenciája mostantól állandó munkamenetek között.

## <a name="powershell-profile"></a>PowerShell-profilt

Alapértelmezés szerint egy PowerShell-profilt nem jön létre.  A profil, hozzon létre egy `PowerShell` könyvtár alatt `$HOME/.config`.  A `$HOME/.config/PowerShell`, a profil neve alatt hozhat létre `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Újdonságok a PowerShell Core 6-os

Újdonságok a PowerShell Core 6-os kapcsolatos további információkért tekintse a [PowerShell docs](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) és a [első lépései a PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogbejegyzés
