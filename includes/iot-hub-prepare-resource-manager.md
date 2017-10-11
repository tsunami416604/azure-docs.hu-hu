## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Felkészülés az Azure Resource Manager-kérelmek hitelesítéséhez szükséges
A műveleteket hajt végre eszközzel kell hitelesítenie a [Azure Resource Manager] [ lnk-authenticate-arm] az Azure Active Directory (AD). A legegyszerűbben úgy konfigurálhatja ezt a PowerShell vagy Azure CLI.

Telepítse a [Azure PowerShell-parancsmagok] [ lnk-powershell-install] a folytatás előtt.

A következő lépések bemutatják, hogyan állíthat be egy PowerShell használatával AD-alkalmazást a jelszó-hitelesítés. Ezek a parancsok a szabványos PowerShell-munkamenetben futtathatja.

1. Jelentkezzen be az Azure-előfizetéshez a következő parancsot:

    ```powershell
    Login-AzureRmAccount
    ```

1. Ha több Azure-előfizetéssel rendelkezik, a jelentkezik be az Azure ad hozzáférést az összes Azure-előfizetést a hitelesítő adatok társított. Használja a következő parancsot a rendelkezésre álló használata Azure-előfizetések listázásához:

    ```powershell
    Get-AzureRMSubscription
    ```

    A következő parancs segítségével válassza ki, hogy az IoT hub kezelésére szolgáló parancsok futtatásához használni kívánt előfizetést. Az előfizetés neve vagy azonosítója is használhatja, ha az előző parancs kimenetében:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Jegyezze fel a **TenantId** és **SubscriptionId**. Ezeket később szüksége.
3. Hozzon létre egy új Azure Active Directory-alkalmazás helyett a helyi tartozó felhasználók számára, a következő parancsot:
   
   * **{Név}:** egy megjelenítési nevet az alkalmazásnak, többek között **MySampleApp**
   * **{Kezdőlap URL-cím}:** a kezdőlap, például az alkalmazás URL-CÍMÉT **http://mysampleapp/home**. Az URL-cím nem kell egy valós alkalmazás mutasson.
   * **{Alkalmazásazonosító}:** egyedi azonosítója, mint **http://mysampleapp**. Az URL-cím nem kell egy valós alkalmazás mutasson.
   * **{Jelszó}:** az alkalmazás hitelesítéséhez használt jelszót.
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Jegyezze fel a **ApplicationId** létrehozott alkalmazás. Később szüksége.
5. A következő parancsot, hogy új szolgáltatásnevet létrehozni **{MyApplicationId}** rendelkező a **ApplicationId** az előző lépésben:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Állítsa be a következő parancsot, hogy a szerepkör-hozzárendelés **{MyApplicationId}** rendelkező a **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Most már befejezte az Azure AD-alkalmazás, amely lehetővé teszi, hogy a hitelesítésre, az egyéni C#-alkalmazás létrehozása. Az oktatóanyag későbbi részében szüksége a következő értékeket:

* A TenantId
* SubscriptionId
* ApplicationId
* Jelszó

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
