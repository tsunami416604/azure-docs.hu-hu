---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b878d54f0f52768459dbfc810e47d294b9c8d996
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097766"
---
Ez a cikk a PowerShell-parancsmagok. A parancsmagok futtatásához használhatja az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A **Másolás** gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. Néhány módszer a Cloud Shell indításához:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Try It** (Kipróbálás) lehetőségre a kódblokk jobb felső sarkában. | ![A Cloud Shell ebben a cikkben](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Nyissa meg a Cloud Shellt a böngészőben. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | [![A Cloud Shell a portálon](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Ha nem szeretné használni az Azure Cloud Shellt, telepíthet PowerShell helyileg helyette. Ha a helyi telepítése és használata PowerShell választja, mindenképpen telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. PowerShell-parancsmagok gyakran frissülnek, és általában frissítenie kell a PowerShell-parancsmagokat a legújabb szolgáltatásfunkciók eléréséhez. Ha nem frissíti a PowerShell-parancsmagokat, a megadott értékek sikertelenek lehetnek. Helyileg futó PowerShell verziója található, használja a 'Get-Module - ListAvailable AzureRM' parancsmagot. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-azurerm-ps) szakaszt. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.