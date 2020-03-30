---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814831"
---
Ez a funkció előzetes verzióban érhető el. Használatához telepítenie kell egy előnézeti bővítményt vagy modult.

### <a name="install-extension-for-azure-cli"></a>Bővítmény telepítése az Azure CLI-hez

Az Azure CLI esetében szüksége van az [Event Grid bővítményre.](/cli/azure/azure-cli-extensions-list)

A [CloudShell ben:](/azure/cloud-shell/quickstart)

* Ha korábban már telepítette a bővítményt, frissítse azt`az extension update -n eventgrid`
* Ha korábban még nem telepítette a bővítményt, telepítse`az extension add -n eventgrid`

Helyi telepítés esetén:

1. [Telepítse az Azure CLI](/cli/azure/install-azure-cli). Győződjön meg arról, hogy a `az --version`legújabb verzióval rendelkezik, a.
1. A bővítmény korábbi verzióinak eltávolítása`az extension remove -n eventgrid`
1. Telepítse `eventgrid` a bővítményt`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>A PowerShell moduljának telepítése

A PowerShell esetében szüksége van az [AzureRM.EventGrid modulra.](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)

A [CloudShell ben:](/azure/cloud-shell/quickstart-powershell)

* A modul telepítése`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Helyi telepítés esetén:

1. A PowerShell-konzol megnyitása rendszergazdaként
1. A modul telepítése`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Ha `-AllowPrerelease` a paraméter nem érhető el, kövesse az alábbi lépéseket:

1. Futtassa a `Install-Module PowerShellGet -Force` parancsot.
1. Futtassa a `Update-Module PowerShellGet` parancsot.
1. A PowerShell-konzol bezárása
1. A PowerShell újraindítása rendszergazdaként
1. A modul telepítése`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
