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
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "66814831"
---
Ez a funkció előzetes verzióban érhető el. A használatához előzetes verziójú bővítményt vagy modult kell telepítenie.

### <a name="install-extension-for-azure-cli"></a>Az Azure CLI-bővítmény telepítése

Az Azure CLI esetén a [Event Grid bővítményre](/cli/azure/azure-cli-extensions-list)van szükség.

A [cloudshellben](/azure/cloud-shell/quickstart)-ben:

* Ha korábban már telepítette a bővítményt, frissítse `az extension update -n eventgrid`
* Ha korábban még nem telepítette a bővítményt, telepítse `az extension add -n eventgrid`

Helyi telepítés esetén:

1. [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t. Ellenőrizze, hogy rendelkezik-e a legújabb verzióval a-nal való ellenőrzéssel `az --version` .
1. A bővítmény korábbi verzióinak eltávolítása `az extension remove -n eventgrid`
1. A `eventgrid` bővítmény telepítése a `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>A PowerShell modul telepítése

A PowerShell esetében a [AzureRM. EventGrid modulra](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)van szükség.

A [cloudshellben](/azure/cloud-shell/quickstart-powershell)-ben:

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
