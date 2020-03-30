---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67133876"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Felkészülés az Azure Resource Manager-kérelmek hitelesítésére
Az Azure Resource Manager és az Azure [Active][lnk-authenticate-arm] Directory (AD) használatával az erőforrásokon végrehajtott összes műveletet hitelesítenie kell. A legegyszerűbb en konfigurálása a PowerShell vagy az Azure CLI használata.

Telepítse az [Azure PowerShell-parancsmagokat][lnk-powershell-install] a folytatás előtt.

A következő lépések bemutatják, hogyan állíthat be jelszó-hitelesítést egy AD-alkalmazás powershell használatával. Ezeket a parancsokat egy szabványos PowerShell-munkamenetben futtathatja.

1. Jelentkezzen be Azure-előfizetésbe a következő paranccsal:

    ```powershell
    Connect-AzAccount
    ```

1. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba való bejelentkezés hozzáférést biztosít a hitelesítő adataihoz társított összes Azure-előfizetéshez. Az alábbi paranccsal felsorolhatja a használható Azure-előfizetéseket:

    ```powershell
    Get-AzSubscription
    ```

    A következő paranccsal válassza ki az okat az előfizetést, amelyet az IoT-központ kezeléséhez szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Jegyezze fel a **TenantId** és **SubscriptionId azonosítóját.** mert később szüksége lesz rájuk.
3. Hozzon létre egy új Azure Active Directory-alkalmazást a következő paranccsal, amely a helyőrzőket helyettesíti:
   
   * **{Display name}:** az alkalmazás megjelenítendő neve, például **a MySampleApp**
   * **{Kezdőlap URL-címe}:** az alkalmazás kezdőlapjának URL-címe, például **http:\//mysampleapp/home**. Ennek az URL-címnek nem kell valódi alkalmazásra mutatnia.
   * **{Alkalmazásazonosító}:** Egyedi azonosító, például **http:\//mysampleapp**. Ennek az URL-címnek nem kell valódi alkalmazásra mutatnia.
   * **{Jelszó}:** Az alkalmazással való hitelesítéshez használt jelszó.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Jegyezze fel a létrehozott alkalmazás **ApplicationId** azonosítóját. Erre később szükséged lesz.
5. Hozzon létre egy új egyszerű szolgáltatást a következő paranccsal, és **a(z) {MyApplicationId}** helyett az előző lépésben az **ApplicationId** azonosítót használja:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Állítson be egy szerepkör-hozzárendelést a következő paranccsal, és cserélje le **a(z) {MyApplicationId}** elemet az **ApplicationId azonosítóra.**
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Ezzel befejezte az Azure AD-alkalmazás létrehozását, amely lehetővé teszi az egyéni C# alkalmazásból történő hitelesítést. Az oktatóanyag későbbi részében a következő értékekre van szüksége:

* TenantId
* SubscriptionId
* ApplicationId
* Jelszó

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
