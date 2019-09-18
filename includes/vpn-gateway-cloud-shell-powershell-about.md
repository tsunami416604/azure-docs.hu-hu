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
ms.openlocfilehash: fe7e617ae2263f1388feef3754f6cd16130ed436
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059687"
---
Ez a cikk a PowerShell-parancsmagokat használja. A parancsmagok futtatásához használhatja a Azure Cloud Shell, egy Azure-ban üzemeltetett interaktív rendszerhéj-környezetet, amelyet a böngészőben használhat. Azure Cloud Shell a Azure PowerShell-parancsmagokat előre telepítette.

Ha az ebben a cikkben szereplő kódot szeretné futtatni Azure Cloud Shellon, nyisson meg egy Cloud Shell-munkamenetet, majd a kód másolásához használja a **Másolás** gombot a kód másolásához, majd illessze be a Cloud Shell-munkamenetbe a __CTRL + SHIFT + v billentyűkombinációval__ Windows és Linux rendszeren, vagy a __cmd + SHIFT + v__ macOS rendszeren. A beillesztett szöveg nem lesz automatikusan végrehajtva, ezért a kód futtatásához nyomja le az **ENTER** billentyűt.

Azure Cloud Shell a használatával indítható el:

|  |   |
|-----------------------------------------------|---|
| Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában. Ez a szöveg __nem__ másolja automatikusan Cloud Shellba. | ![Példa a Azure Cloud Shell kipróbálására](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Nyissa meg a [shell.Azure.com](https://shell.azure.com) a böngészőben. | [![Azure Cloud Shell indítása gomb](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Az [Azure Portal](https://portal.azure.com) jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra. | ![Cloud Shell gomb a Microsoft Azure Portal-on](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**A PowerShell helyi futtatása**

A Azure PowerShell-parancsmagokat helyileg is telepítheti és futtathatja a számítógépen. A PowerShell-parancsmagok gyakran frissülnek. Ha nem a legújabb verziót futtatja, előfordulhat, hogy az utasításokban megadott értékek sikertelenek lesznek. A számítógépen telepített Azure PowerShell verzióinak megkereséséhez használja a `Get-Module -ListAvailable Az` parancsmagot. A telepítéséhez vagy frissítéséhez tekintse meg [a Azure PowerShell modul telepítését](/powershell/azure/install-az-ps)ismertető témakört.
