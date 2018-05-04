
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
Először kell rendelkeznie a legújabb [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) telepíteni és futtatni. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Ez a témakör használatban példák [Azure Resource Manager üzembe helyezési modellben](../articles/azure-resource-manager/resource-group-overview.md), így a példák a [Azure Resource Manager parancsmagjainak](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Futtassa a [ **Connect-AzureRmAccount** ](http://msdn.microsoft.com/library/mt619267.aspx) parancsmagot – ekkor megjelenik egy bejelentkezési képernyő hitelesítő adatait. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

    Connect-AzureRmAccount

Ha több előfizetéssel használja a [ **Set-AzureRmContext** ](http://msdn.microsoft.com/library/mt619263.aspx) parancsmag segítségével válassza ki a PowerShell-munkamenetet használjon melyik előfizetést. Ahhoz, hogy megtudja, melyik előfizetést használja a jelenlegi PowerShell-munkamenet, futtassa a [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx) parancsmagot. Az összes előfizetés megtekintéséhez futtassa a [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx) parancsmagot.

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

