---
services: virtual-machines
title: PowerShell beállítása
author: JoeDavies-MSFT
solutions: ''
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: b96e8e6e31817f6d261f41dbf3b3047dd49c29ba
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258243"
---
## <a name="setting-up-powershell"></a>PowerShell beállítása
Azure PowerShell használata előtt kövesse az alábbi lépéseket.

### <a name="verify-powershell-versions"></a>PowerShell-verzió ellenőrzése
Windows PowerShell használata előtt a Windows PowerShell, Version 3.0 vagy 4.0-s verzióját kell rendelkeznie. A Windows PowerShell-verzió megkereséséhez írja be ezt a parancsot egy Windows PowerShell parancssorába.

    $PSVersionTable

Megtekintheti az ehhez hasonló.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Ellenőrizze, hogy értékét **PSVersion** 3.0 vagy 4.0-s verzióját. Telepítenie egy kompatibilis verziót, tekintse meg a [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) vagy [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Továbbá ismernie kell az Azure PowerShell 0.8.0 verzió vagy újabb. Ellenőrizheti az Azure PowerShell, amelyen telepítve van a következő paranccsal, az Azure PowerShell-parancssorba verzióját.

    Get-Module azure | format-table version

Megtekintheti az ehhez hasonló.

    Version
    -------
    0.8.16.1

Útmutatás és a egy hivatkozást a legújabb verzióra: [telepítése és konfigurálása az Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Az Azure-fiók és -előfizetés beállítása
Ha még nem rendelkezik Azure-előfizetése, aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy regisztrálhat egy [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).

Nyisson meg egy Azure PowerShell-parancssort, és jelentkezzen be az Azure-bA az alábbi paranccsal.

    Add-AzureAccount

Ha több Azure-előfizetéssel rendelkezik, az Azure-előfizetést a következő paranccsal listázhatja.

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

Az aktuális Azure-előfizetést is megadhat a parancsok futtatása az Azure PowerShell-parancssorba. Cserélje le a mindent, ami az ajánlatokat, többek között a < és > karakter, helyes nevét.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

További információ az Azure-előfizetések és fiókok: [módjáról: Csatlakozás az előfizetéshez](/powershell/azureps-cmdlets-docs#Connect).

