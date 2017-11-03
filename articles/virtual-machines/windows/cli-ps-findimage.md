---
title: "Windows Virtuálisgép-rendszerképek kiválasztása az Azure-ban |} Microsoft Docs"
description: "Megtudhatja, hogyan használhatja az Azure Powershellt közzétevő, az ajánlat, SKU, és verziója a piactér Virtuálisgép-rendszerképek meghatározásához."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: c9b35ff5f3fbd33639805b5a4f105df32562a691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Az Azure piactéren az Azure PowerShell Windows Virtuálisgép-lemezképek megkeresése

Ez a témakör ismerteti az Azure PowerShell használata az Azure piactéren Virtuálisgép-rendszerképek kereséséhez. Ezen információk használatával adja meg a Piactéri lemezkép a Windows virtuális gépek létrehozásakor.

Győződjön meg arról, hogy telepített és konfigurált legújabb [Azure PowerShell modul](/powershell/azure/install-azurerm-ps).



## <a name="table-of-commonly-used-windows-images"></a>A gyakran használt Windows-lemezképek tábla
| Közzétevő neve | Ajánlat | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-adatközpont-kiszolgálómag |
| MicrosoftWindowsServer |WindowsServer |2016-adatközpont-az-tárolók |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-kiszolgáló |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008 R2 SP1 CSOMAG |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Adott rendszerképek keresése


Amikor új virtuális gépet hoz létre az Azure Resource Managerrel, néhány esetben meg kell határoznia egy rendszerképet a következő tulajdonságok kombinációja alapján:

* Közzétevő
* Ajánlat
* SKU

Például használja ezeket az értékeket a a [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell-parancsmagot, vagy egy erőforrás-sablon, amelyben meg kell adni a virtuális gép létrehozni.

Ha meg kell határoznia ezeket az értékeket, futtathatja a [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), és [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancsmag megnyitása a képek. Meghatározhatja, hogy ezeket az értékeket:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Először listázza a közzétevőket a következő parancsokkal:

```azurepowershell-interactive
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Adja meg a kiválasztott közzétevő nevét, és futtassa a következő parancsokat:

```azurepowershell-interactive
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Adja meg a kiválasztott ajánlat nevét, és futtassa a következő parancsokat:

```azurepowershell-interactive
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

A kimenetét a `Get-AzureRMVMImageSku` parancsot minden olyan információt, meg kell adnia a kép egy új virtuális gép van.

Az alábbi egy teljes példa:

```azurepowershell-interactive
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Kimenet:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

A „MicrosoftWindowsServer” közzétevő esetében:

```azurepowershell-interactive
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Kimenet:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

A „WindowsServer” ajánlat esetében:

```azurepowershell-interactive
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Kimenet:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

Ha a listából kimásolja a kiválasztott termékváltozat nevét, akkor rendelkezésére áll a `Set-AzureRMVMSourceImage` PowerShell-parancsmaghoz vagy egy erőforráscsoport sablonjához szükséges összes információ.

## <a name="next-steps"></a>Következő lépések
Most már kiválaszthatja azt az adott rendszerképet, amelyet használni szeretne. A virtuális gépek gyors létrehozása csak talált, kép információk segítségével: [Windows virtuális gép létrehozása a PowerShell-lel](quick-create-powershell.md).
