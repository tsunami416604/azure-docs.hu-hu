---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4d9f05ad1913646e7ca09690af965042817ef7ec
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193393"
---
Ez a cikk a PowerShell-parancsmagok. A parancsmagok futtatásához használhatja az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A **Másolás** gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. Néhány módszer a Cloud Shell indításához:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Try It** (Kipróbálás) lehetőségre a kódblokk jobb felső sarkában. | ![A Cloud Shell ebben a cikkben](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Nyissa meg a Cloud Shellt a böngészőben. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | [![A Cloud Shell a portálon](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Ha nem szeretné használni az Azure Cloud Shellt, telepíthet PowerShell helyileg helyette. Ha a helyi telepítése és használata PowerShell választja, mindenképpen telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. PowerShell-parancsmagok gyakran frissülnek, és általában frissítenie kell a PowerShell-parancsmagokat a legújabb szolgáltatásfunkciók eléréséhez. Ha nem frissíti a PowerShell-parancsmagokat, a megadott értékek sikertelenek lehetnek. 

Helyileg futó PowerShell verziója található, használja a 'Get-Module - ListAvailable AzureRM' parancsmagot. Frissítéséhez lásd [Azure PowerShell-modul telepítéséhez](/powershell/azure/azurerm/install-azurerm-ps). További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/azurerm/overview) foglalkozó témakörben talál.
