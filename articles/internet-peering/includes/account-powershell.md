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
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678493"
---
A konfigurálás megkezdése előtt telepítse és importálja a szükséges modulokat. A PowerShell ben a modulok telepítéséhez rendszergazdai jogosultságokra van szükség.

1. Telepítse és importálja az Az modult.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Telepítse és importálja az Az.Peering modult.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Ellenőrizze, hogy az importált modulok megfelelően importálva ezzel a paranccsal:
    ```powershell
    Get-Module
    ```
1. Jelentkezzen be Azure-fiókjába ezzel a paranccsal:
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
> Ha még nem társította az ASN-t és az előfizetést, kövesse a [Társasa ASN társítása](../howto-subscription-association-powershell.md)című részben leírt lépéseket. Ez a művelet szükséges társviszony-létesítés kéréséhez.

> [!NOTE]
> Az erőforráscsoport helye független attól a helytől, ahol társviszony-létesítést szeretne beállítani.
&nbsp;
