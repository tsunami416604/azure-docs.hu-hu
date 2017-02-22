
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
Ehhez telepítenie és futtatnia kell az Azure PowerShell legújabb verzióját. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Az SQL Database sok új funkciója csak az [Azure Resource Manager-alapú üzemi modellben](../articles/azure-resource-manager/resource-group-overview.md) támogatott, így a példák a Resource Manager [Azure SQL Database PowerShell parancsmagjait](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) veszik alapul. A szolgáltatásfelügyeleti (klasszikus) üzemi modell [Azure SQL Database szolgáltatásfelügyeleti parancsmagjai](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) visszafelé kompatibilisen támogatottak, de a Resource Manager parancsmagjainak használata ajánlott.
> 
> 

Futtassa az [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) parancsmagot – ekkor megjelenik egy bejelentkezési képernyő, amely a hitelesítő adatainak megadását kéri. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

    Add-AzureRmAccount

Ha több előfizetéssel rendelkezik, használja a [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) parancsmagot, amellyel kiválaszthatja, hogy melyik előfizetéssel használja a PowerShell-munkamenetet. Ahhoz, hogy megtudja, melyik előfizetést használja a jelenlegi PowerShell-munkamenet, futtassa a [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx) parancsmagot. Az összes előfizetés megtekintéséhez futtassa a [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx) parancsmagot.

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Feb17_HO3-->


