## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ez a példa **eastus**. Tárolja el ezt egy változóban, és használja azt, így szükség esetén csak egy helyen kell módosítania az adatokat.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy szabványos általános célú tárfiókot az LRS-replikáció használatával [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), majd lekérheti a tárfiók környezetét, amely definiálja a tárfiókot használni. A tárfiók eljárva hivatkozik a környezet helyett ismételten adja meg a hitelesítő adatokat. Ez a példa nevű tárfiók létrehozása *mystorageaccount* titkosítással helyileg redundáns storage(LRS) és blob (alapértelmezés szerint engedélyezve van).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `

$ctx = $storageAccount.Context
```
