## <a name="set-up-azure-powershell-for-azure-dns"></a>Az Azure DNS az Azure PowerShell beállítása

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse a legújabb verziót az Azure Resource Manager PowerShell-parancsmagokat kell. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azureps-cmdlets-docs) foglalkozó témakörben talál.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. További információkért lásd: [PowerShell használata a Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést
 
Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Az Azure DNS szolgáltatást a Microsoft.Network erőforrás-szolgáltató kezeli. Az Azure DNS használatbavétele előtt az Azure-előfizetést regisztrálni kell ennek az erőforrás-szolgáltatónak a használatához. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```