---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884032"
---
Ez a cikk a PowerShell-parancsmagok. A parancsmagok futtatásához használhatja az Azure Cloud Shellben. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület rendelkező közös Azure-eszközök, előre telepítve és konfigurálva a fiókjával való használat. Ehhez egyszerűen kattintson **másolási** másolni a kódot, illessze be a Cloud Shellben, és nyomja meg az enter a futtatáshoz. Néhány módszer a Cloud Shell indításához:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Try It** (Kipróbálás) lehetőségre a kódblokk jobb felső sarkában. | ![A Cloud Shell ebben a cikkben](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Nyissa meg a Cloud Shellt a böngészőben. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | [![A Cloud Shell a portálon](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**Helyileg futtatja a Powershellt**

Ugyanígy telepítenie és az Azure PowerShell-parancsmagok helyileg futtathatja a számítógépen. PowerShell-parancsmagok gyakran frissülnek. Ha nem a legújabb verziót futtatja, az útmutatóban megadott értékek sikertelenek lehetnek. Helyileg futtatja a PowerShell verziójának megkereséséhez használja a `Get-Module -ListAvailable Az` parancsmagot. Tekintse meg telepíteni vagy frissíteni, [Azure PowerShell-modul telepítéséhez](/powershell/azure/install-az-ps).