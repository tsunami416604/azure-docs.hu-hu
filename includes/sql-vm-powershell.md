
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
Először szüksége lesz a legújabb [Azure PowerShell-lel](http://msdn.microsoft.com/library/mt619274.aspx) telepíteni és futtatni. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Ez a témakör használatban példák [Azure Resource Manager üzemi modell](../articles/azure-resource-manager/resource-group-overview.md), így a példák a [Azure Resource Manager parancsmagjainak](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Futtassa a [ **Connect-AzureRmAccount** ](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot, és megjelenik egy bejelentkezési képernyő, amely a hitelesítő adatainak megadását. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

    Connect-AzureRmAccount

Ha több előfizetés használata az [ **Set-AzureRmContext** ](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) parancsmagot, amellyel kiválaszthatja, hogy melyik előfizetéssel, a PowerShell-munkamenetet használjon. Ahhoz, hogy megtudja, melyik előfizetést használja a jelenlegi PowerShell-munkamenet, futtassa a [**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext) parancsmagot. Az összes előfizetés megtekintéséhez futtassa a [**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription) parancsmagot.

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

