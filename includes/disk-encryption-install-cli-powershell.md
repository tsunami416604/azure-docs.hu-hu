---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511482"
---
Az Azure Disk Encryption az [Azure CLI-n](/cli/azure) és az [Azure PowerShellen](/powershell/azure/new-azureps-module-az)keresztül engedélyezhető és kezelhető. Ehhez helyileg kell telepítenie az eszközöket, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2.0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A CLI-t úgy tervezték, hogy rugalmasan lekérdezze az adatokat, támogassa a hosszú ideig futó műveleteket nem blokkoló folyamatként, és megkönnyítse a parancsfájlok futtatását. Helyileg telepítheti az [Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest)című témakör lépéseit követve.

Jelentkezzen [be az Azure-fiókjába az Azure CLI-vel,](/cli/azure/authenticate-azure-cli)használja az [az bejelentkezési](/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli
az login
```

Ha ki szeretne választani egy bérlőt, aki be szeretne jelentkezni, használja a következőt:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egy adott előfizetési listát, az [az-fióklistával](/cli/azure/account#az-account-list) kapja meg az előfizetési listát, és adja meg [az az fiókkészlettel.](/cli/azure/account#az-account-set)
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: [Az Azure CLI 2.0 – Első lépések.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) egy parancsmagkészletet biztosít, amely az Azure Resource [Manager](/azure/azure-resource-manager/resource-group-overview) modellt használja az Azure-erőforrások kezeléséhez. Használhatja a böngészőben az [Azure Cloud Shell,](/azure/cloud-shell/overview)vagy telepítheti a helyi gépen az Utasítások [telepítése az Azure PowerShell modul.](/powershell/azure/install-az-ps) 

Ha már telepítette helyileg, győződjön meg arról, hogy az Azure PowerShell SDK-verzió legújabb verzióját használja az Azure Disk Encryption konfigurálásához. Töltse le az [Azure PowerShell-kiadás](https://github.com/Azure/azure-powershell/releases)legújabb verzióját.

Ha [be szeretne jelentkezni az Azure PowerShell használatával az Azure-fiókba,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)használja a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmagját.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egyet, a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmag segítségével sorolja fel őket, majd a [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmag:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatása ellenőrzi, hogy a megfelelő előfizetés lett-e kiválasztva.

Az Azure Disk Encryption parancsmagok telepítésének ellenőrzéséhez használja a [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) parancsmab:
     
```powershell
Get-command *diskencryption*
```
További információ: [Az Azure PowerShell – első lépések.](/powershell/azure/get-started-azureps) 