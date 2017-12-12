### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>A PowerShell telepítése
Ha még nincs a gépén, telepítse a PowerShell legújabb verzióját. 

1. A webböngészőben lépjen az [Azure letöltések](https://azure.microsoft.com/downloads/) lapra. 
2. A **Parancssori eszközök** -> **PowerShell** szakaszban kattintson a **Windows-telepítés** elemre. 
3. Futtassa az **MSI**-fájlt a PowerShell telepítéséhez. 

Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Bejelentkezés a PowerShellbe

1. Indítsa el a gépén a **PowerShellt**. A PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.

    ![A PowerShell indítása](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználónevét és jelszavát:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```powershell
    Get-AzureRmSubscription
    ```
3. Ha több előfizetés is tartozik a fiókjához, futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
