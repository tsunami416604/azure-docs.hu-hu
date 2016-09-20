
## Indítsa el a PowerShell-munkamenetet

Ehhez telepíteni és futtatni kell az [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) legújabb verzióját. Részletes információk: [How to install and configure Azure PowerShell](../articles/powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása).


>[AZURE.NOTE] Az SQL Database sok új funkciója csak az [Azure Resource Manager-alapú üzemi modellben](../articles/resource-group-overview.md) támogatott, így a példák a Resource Manager [Azure SQL Database PowerShell parancsmagjait](https://msdn.microsoft.com/library/azure/mt574084.aspx) veszik alapul. Az létező klasszikus telepítési modell [Azure SQL Database (classic) parancsmagjai](https://msdn.microsoft.com/library/azure/dn546723.aspx) visszafelé kompatibilisen támogatottak, de a Resource Manager parancsmagjainak használata ajánlott.


Futtassa az [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) parancsmagot – ekkor megjelenik egy bejelentkezési képernyő, amely a hitelesítő adatainak megadását kéri. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

    Add-AzureRmAccount

Ha több előfizetéssel rendelkezik, használja a [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) parancsmagot, amellyel kiválaszthatja, hogy melyik előfizetéssel használja a PowerShell-munkamenetet. Ahhoz, hogy megtudja, melyik előfizetést használja a jelenlegi PowerShell-munkamenet, futtassa a [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx) parancsmagot. Az összes előfizetés megtekintéséhez futtassa a [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx) parancsmagot.

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'



<!--HONumber=sep16_HO1-->


