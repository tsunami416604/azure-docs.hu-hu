Mielőtt hozzálát a művelethez, be kell jelentkeznie az Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára. További információ: [A Windows PowerShell használata a Resource Managerrel](../articles/powershell-azure-resource-manager.md).

A bejelentkezéshez nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Login-AzureRmAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```powershell
Get-AzureRmSubscription
```

Válassza ki a használni kívánt előfizetést.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```