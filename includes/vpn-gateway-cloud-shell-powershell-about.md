---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133785"
---
Ez a cikk a PowerShell-parancsmagok. A parancsmagok futtatásához használhatja az Azure Cloud Shell egy interaktív rendszerhéj-környezetet az Azure-ban üzemeltetett és a böngészőn keresztül használja. Az Azure Cloud Shell az Azure PowerShell-parancsmagok, előtelepített tartalmaz.

Ez a cikk az Azure Cloud Shellben lévő kód futtatásához nyissa meg a Cloud Shell-munkamenetet, használja a **másolási** kimásolhatja a kódokat, és illessze be a Cloud Shell-munkamenetet a kódblokk gombjára __Ctrl + Shift + V__ a Windows és Linux rendszeren, vagy __Cmd + Shift + V__ macOS rendszeren. A beillesztett szöveg végrehajtása nem automatikus, így nyomja meg az **Enter** kód futtatásához.

Az Azure Cloud Shell indíthatja el:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában. Ez __nem__ automatikusan szöveget másol a Cloud Shellben. | ![Példa próbálja ki az Azure Cloud Shell](./media/cloud-shell-try-it/cli-try-it.png) |
| Nyissa meg [shell.azure.com](https://shell.azure.com) a böngészőben. | [![Indítsa el az Azure Cloud Shell gombra](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| Az [Azure Portal](https://portal.azure.com) jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | ![Cloud Shell gomb a Microsoft Azure Portal-on](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**Helyileg futtatja a Powershellt**

Ugyanígy telepítenie és az Azure PowerShell-parancsmagok helyileg futtathatja a számítógépen. PowerShell-parancsmagok gyakran frissülnek. Ha nem a legújabb verziót futtatja, az útmutatóban megadott értékek sikertelenek lehetnek. Azure PowerShell-lel a számítógépre telepített verziók megkereséséhez használja a `Get-Module -ListAvailable Az` parancsmagot. Tekintse meg telepíteni vagy frissíteni, [Azure PowerShell-modul telepítéséhez](/powershell/azure/install-az-ps).
