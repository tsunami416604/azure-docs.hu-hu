---
title: az Azure DNS PowerShell beágyazott fájlja
description: az Azure DNS PowerShell beágyazott fájlja
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: e96b312f03069256396261bd6efe2f2586cdadea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Az Azure DNS az Azure PowerShell beállítása

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse a legújabb verziót az Azure Resource Manager PowerShell-parancsmagokat kell. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azureps-cmdlets-docs) foglalkozó témakörben talál.

Emellett saját zónák (nyilvános előzetes verzió) használatához szüksége, hogy az alábbi PowerShell-modulok és verziók. 
* AzureRM.Dns - [verzió 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) vagy újabb
* AzureRM.Network - [verzió 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) vagy újabb

Letöltheti a fenti modulok a PowerShell-galériából hivatkozásokkal fent mellett a modul verziója. Ezután telepítheti őket használatával az alábbi parancsok. Mindkét a modulok szükségesek, és teljesen visszafelé kompatibilis. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. További információkért lásd: [PowerShell használata a Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést
 
Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Az Azure DNS szolgáltatást a Microsoft.Network erőforrás-szolgáltató kezeli. Az Azure DNS használatbavétele előtt az Azure-előfizetést regisztrálni kell ennek az erőforrás-szolgáltatónak a használatához. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```