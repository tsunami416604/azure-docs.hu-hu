---
author: sdwheeler
ms.service: azure-powershell
ms.topic: include
ms.date: 11/09/2018
ms.author: sewhee
ms.openlocfilehash: 5f224ab3b0040bcba6b99d8db744e9dbde4a63c9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572264"
---
## <a name="setting-up-powershell-for-resource-manager-templates"></a>PowerShell beállítása a Resource Manager-sablonok
Azure PowerShell Resource Managerrel használható, mielőtt szüksége lesz a jobb Windows PowerShell és az Azure PowerShell-verziókat.

### <a name="verify-powershell-versions"></a>PowerShell-verzió ellenőrzése
Ellenőrizze, hogy rendelkezik-e Windows PowerShell 3.0 vagy 4.0-s verziója. A Windows PowerShell-verzió megkereséséhez írja be ezt a parancsot egy Windows PowerShell parancssorába.

    $PSVersionTable

A következő információtípust kapja:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Ellenőrizze, hogy értékét **PSVersion** 3.0 vagy 4.0-s verzióját. Ha nincs engedélyezve, [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) vagy [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Az Azure-fiók és -előfizetés beállítása
Ha még nem rendelkezik Azure-előfizetése, aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) vagy regisztrálhat egy [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).

Nyisson meg egy Azure PowerShell-parancssort, és jelentkezzen be az Azure-bA az alábbi paranccsal.

    Connect-AzureRmAccount

Ha több Azure-előfizetéssel rendelkezik, az Azure-előfizetést a következő paranccsal listázhatja.

    Get-AzureRmSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

További információ az Azure-előfizetések és fiókok: [módjáról: Csatlakozás az előfizetéshez](/powershell/azureps-cmdlets-docs#step-3-connect).

