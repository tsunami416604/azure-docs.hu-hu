---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 2eacb55eaf355a4eef17b9e16075d8d12167266d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164989"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Felkészülés az Azure Resource Manager-kérelmek hitelesítéséhez
Használatával erőforrásokon végrehajtott összes műveletet kell hitelesítenie a [Azure Resource Manager] [ lnk-authenticate-arm] az Azure Active Directory (AD). A legegyszerűbben úgy konfigurálhatja ezt, hogy a PowerShell vagy az Azure CLI segítségével.

Telepítse a [Azure PowerShell-parancsmagok] [ lnk-powershell-install] a folytatás előtt.

A következő lépések bemutatják, hogyan állítsa be a jelszó-hitelesítés PowerShell használatával AD-alkalmazáshoz. Ezek a parancsok a standard szintű PowerShell-munkamenetben futtathatja.

1. Jelentkezzen be az Azure-előfizetéshez az alábbi paranccsal:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Ha több Azure-előfizetéssel rendelkezik, az Azure-bA bejelentkezik hozzáférést, az összes Azure-előfizetések a hitelesítő adatokhoz tartozó. Használja a következő parancs használható elérhető Azure-előfizetések listázásához:

    ```powershell
    Get-AzureRMSubscription
    ```

    A következő parancs használata kezelheti az IoT hub-parancsok futtatásához használni kívánt előfizetés kiválasztásához. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Jegyezze fel a **TenantId** és **SubscriptionId**. mert később szüksége lesz rájuk.
3. Hozzon létre egy új Azure Active Directory-alkalmazás a következő parancsot, és cserélje le a hely tulajdonosai:
   
   * **{Név}:** megjelenített neve, mint például az alkalmazás **MySampleApp**
   * **{Kezdőlap URL-címe}:** URL-címe az alkalmazás, mint például a kezdőlap **http://mysampleapp/home**. Az URL-cím nem kell, hogy egy valódi alkalmazás mutasson.
   * **{Alkalmazásazonosító}:** például egy egyedi azonosítót **http://mysampleapp**. Az URL-cím nem kell, hogy egy valódi alkalmazás mutasson.
   * **{Password}:** az alkalmazás hitelesítéséhez használt jelszó.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Jegyezze fel a **ApplicationId** a létrehozott alkalmazás. Később szüksége.
5. Hozzon létre egy új szolgáltatásnevet, a következő parancsot, és cserélje le **{MyApplicationId}** együtt a **ApplicationId** az előző lépésből:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Állítsa be a következő parancsot, és cserélje le a szerepkör-hozzárendelés **{MyApplicationId}** együtt a **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Most már befejezte az Azure AD-alkalmazást, amely lehetővé teszi, hogy az egyéni hitelesítés létrehozása C# alkalmazás. Az oktatóanyag későbbi részében szüksége a következő értékeket:

* TenantId
* SubscriptionId
* Alkalmazásazonosító
* Jelszó

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
