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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774223"
---
A konfigurálás megkezdése előtt telepítse és importálja a szükséges modulokat. Rendszergazdai jogosultságokkal kell rendelkeznie a modulok PowerShellben való telepítéséhez.

1. Telepítés és Importálás az modul
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az az. peering modul telepítése és importálása
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Ellenőrizze, hogy a modulok importálása az alábbi parancs használatával történik-e.
    ```powershell
    Get-Module
    ```
1. Jelentkezzen be az Azure-fiókjába a következő parancs használatával.
    ```powershell
    Connect-AzAccount
    ```
1. Tekintse át a fiók előfizetéseit, és válassza ki azt az előfizetést, amelyben létre szeretné hozni a társítást.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Ha még nem rendelkezik erőforráscsoporthoz, létre kell hoznia egyet a társítás létrehozása előtt. Ezt a következő parancs futtatásával teheti meg:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Ha még nem társította az ASN-t és az előfizetést, kövesse a [peer ASN társításához](../howto-subscription-association-powershell.md)tartozó lépéseket. A társítás igényléséhez ez szükséges.

> [!NOTE]
> Az erőforráscsoport helye független attól a helytől, ahol a társítást beállítja.
&nbsp;
