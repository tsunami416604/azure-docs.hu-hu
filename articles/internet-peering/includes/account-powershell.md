---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774223"
---
A konfigurálás megkezdése előtt telepítse és importálja a szükséges modulokat. A Modulok PowerShellben való telepítéséhez rendszergazdai jogosultságokra lesz szüksége.

1. Az modul telepítése és importálása
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az.Peering modul telepítése és importálása
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Ellenőrizze, hogy a modulok importálása finom an parancsot alább.
    ```powershell
    Get-Module
    ```
1. Jelentkezzen be Az Azure-fiókjába a következő paranccsal.
    ```powershell
    Connect-AzAccount
    ```
1. Ellenőrizze a fiók előfizetéseit, és válassza ki azt az előfizetést, amelyben társviszony-létesítést szeretne létrehozni.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Ha még nem rendelkezik erőforráscsoporttal, létre kell hoznia egyet, mielőtt társviszony-létesítést hozna létre. Ezt a következő parancs futtatásával teheti meg:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Ha még nem társította az ASN-t és az előfizetést, kövesse a [Társtárs ASN](../howto-subscription-association-powershell.md)lépéseit. Erre a társviszony-létesítés kérelmezéséhez van szükség.

> [!NOTE]
> Az erőforráscsoport helye független attól a helytől, ahol társviszony-létesítést szeretne beállítani.
&nbsp;
