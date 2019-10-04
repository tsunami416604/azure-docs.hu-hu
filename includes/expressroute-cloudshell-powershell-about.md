---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ba6673b5fda6af8c9bd3ef53dff33fe63f41e26d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133145"
---
Ez a cikk a PowerShell-parancsmagok. A parancsmagok futtatásához használhatja az Azure Cloud Shellben. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület előtelepített közös Azure-eszközök és a fiókjához használatára van konfigurálva. A **Másolás** gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. Néhány módszer a Cloud Shell indításához:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Try It** (Kipróbálás) lehetőségre a kódblokk jobb felső sarkában. | ![A Cloud Shell ebben a cikkben](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Nyissa meg a Cloud Shellt a böngészőben. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | [![A Cloud Shell a portálon](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**Azure PowerShell helyi futtatása**

Ugyanígy telepítenie és az Azure PowerShell-parancsmagok helyileg futtathatja a számítógépen. PowerShell-parancsmagok gyakran frissülnek. Ha nem a legújabb verziót futtatja, az útmutatóban megadott értékek sikertelenek lehetnek. A rendszer PowerShell telepített verziója megkereséséhez használja a `Get-Module -ListAvailable Az` parancsmagot. Tekintse meg telepíteni vagy frissíteni, [Azure PowerShell-modul telepítéséhez](/powershell/azure/install-az-ps).
