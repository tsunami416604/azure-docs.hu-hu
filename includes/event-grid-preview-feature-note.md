---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 644669ea27938e385e11f3b1911a23ab30829a95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026090"
---
Ez a funkció előzetes verzióban érhető el. A használatához előzetes verziójú bővítményt vagy modult kell telepítenie.

### <a name="install-extension-for-azure-cli"></a>Az Azure CLI-bővítmény telepítése

Az Azure CLI esetén a [Event Grid bővítményre](/cli/azure/azure-cli-extensions-list)van szükség.

A [cloudshellben](../articles/cloud-shell/quickstart.md)-ben:

* Ha korábban már telepítette a bővítményt, frissítse `az extension update -n eventgrid`
* Ha korábban még nem telepítette a bővítményt, telepítse `az extension add -n eventgrid`

Helyi telepítés esetén:

1. [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t. Ellenőrizze, hogy rendelkezik-e a legújabb verzióval a-nal való ellenőrzéssel `az --version` .
1. A bővítmény korábbi verzióinak eltávolítása `az extension remove -n eventgrid`
1. A `eventgrid` bővítmény telepítése a `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>A PowerShell modul telepítése

A PowerShell esetében a [AzureRM. EventGrid modulra](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)van szükség.

A [cloudshellben](../articles/cloud-shell/quickstart-powershell.md)-ben:

* A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Helyi telepítés esetén:

1. A PowerShell-konzol megnyitása rendszergazdaként
1. A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Ha a `-AllowPrerelease` paraméter nem érhető el, kövesse az alábbi lépéseket:

1. Az `Install-Module PowerShellGet -Force` parancs futtatása
1. Az `Update-Module PowerShellGet` parancs futtatása
1. A PowerShell-konzol lezárása
1. A PowerShell újraindítása rendszergazdaként
1. A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`