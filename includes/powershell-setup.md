---
services: virtual-machines
title: "PowerShell beállítása"
author: JoeDavies-MSFT
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
## <a name="setting-up-powershell"></a>PowerShell beállítása
Azure PowerShell használata előtt kövesse az alábbi lépéseket.

### <a name="verify-powershell-versions"></a>Ellenőrizze a PowerShell-verzió
A Windows PowerShell használata előtt rendelkeznie kell a Windows PowerShell, Version 3.0 vagy 4.0-s verzióját. A Windows PowerShell verziója található, írja be ezt a parancsot egy Windows PowerShell parancssorába.

    $PSVersionTable

Megtekintheti az alábbihoz hasonló.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Ellenőrizze, hogy értékének **PSVersion** 3.0 vagy 4.0-s verzióját. Telepítsen egy kompatibilis verziót, lásd: [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) vagy [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Be kell Azure PowerShell 0.8.0 verzió vagy újabb. Ellenőrizheti az Azure PowerShell ezzel a paranccsal, az Azure PowerShell-parancssorba telepített verzióját.

    Get-Module azure | format-table version

Megtekintheti az alábbihoz hasonló.

    Version
    -------
    0.8.16.1

Útmutatás és egy hivatkozást a legújabb verzióra: [telepítése és konfigurálása az Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Az Azure-fiók és -előfizetés beállítása
Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

Nyisson meg egy Azure PowerShell-parancssort, és jelentkezzen be Azure ezt a parancsot.

    Add-AzureAccount

Ha több Azure-előfizetéssel rendelkezik, az Azure-előfizetések ezzel a paranccsal jeleníthet meg.

    Get-AzureSubscription

A következő információtípust kapja:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Beállíthatja a jelenlegi Azure-előfizetés az Azure PowerShell-parancssorba ezek a parancsok futtatásával. Cserélje le a mindent, ami az ajánlatokat, beleértve a < és > karakter, a megfelelő név.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

További információ az Azure-előfizetések és a fiókok: [hogyan: Csatlakozás az előfizetéshez](/powershell/azureps-cmdlets-docs#Connect).

