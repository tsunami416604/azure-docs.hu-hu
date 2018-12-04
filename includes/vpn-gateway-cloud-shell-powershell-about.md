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
ms.openlocfilehash: 6a0ea318f2e9b8f392ac7c0a1f1091c062c59d41
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852356"
---
Ez a cikk a PowerShell-parancsmagok. A parancsmagok futtatásához használhatja az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A **Másolás** gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. Néhány módszer a Cloud Shell indításához:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Try It** (Kipróbálás) lehetőségre a kódblokk jobb felső sarkában. | ![A Cloud Shell ebben a cikkben](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Nyissa meg a Cloud Shellt a böngészőben. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | [![A Cloud Shell a portálon](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Ha nem szeretné használni az Azure Cloud Shellt, telepíthet PowerShell helyileg helyette. Ha a helyi telepítése és használata PowerShell választja, mindenképpen telepítse a legújabb verzióját, az Azure Resource Manager PowerShell-parancsmagokat a legújabb szolgáltatásfunkciók eléréséhez.

Helyileg futó PowerShell verziója található, használja a 'Get-Module - ListAvailable AzureRM' parancsmagot. Frissítéséhez lásd [Azure PowerShell-modul telepítéséhez](/powershell/azure/install-azurerm-ps). További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.