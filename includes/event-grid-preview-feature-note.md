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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814831"
---
Ez a funkció előzetes verzióban érhető el. A használatához telepítenie kell egy minta-kiterjesztés vagy a modul.

### <a name="install-extension-for-azure-cli"></a>Azure CLI-hez a bővítmény telepítése

Azure CLI-vel, szüksége lesz a [Event Grid-bővítmény](/cli/azure/azure-cli-extensions-list).

A [cloud Shell](/azure/cloud-shell/quickstart):

* Ha korábban telepítette a bővítményt, frissítése `az extension update -n eventgrid`
* Ha korábban még nem telepítette a bővítményt, telepítse azt `az extension add -n eventgrid`

Helyi telepítéséhez:

1. [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Győződjön meg arról, hogy rendelkezik-e a legújabb verziót az ellenőrzésével `az --version`.
1. A bővítmény korábbi verzióinak eltávolítása `az extension remove -n eventgrid`
1. Telepítse a `eventgrid` bővítmény `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>PowerShell-modul telepítése

PowerShell esetén van szükség a [AzureRM.EventGrid modul](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

A [cloud Shell](/azure/cloud-shell/quickstart-powershell):

* A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Helyi telepítéséhez:

1. Nyissa meg a PowerShell-konzolt rendszergazdaként
1. A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Ha a `-AllowPrerelease` paraméter nem érhető el, kövesse az alábbi lépéseket:

1. Futtassa a `Install-Module PowerShellGet -Force` parancsot.
1. Futtassa a `Update-Module PowerShellGet` parancsot.
1. Zárja be a PowerShell-konzol
1. Indítsa újra a PowerShell rendszergazdaként
1. A modul telepítése `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
