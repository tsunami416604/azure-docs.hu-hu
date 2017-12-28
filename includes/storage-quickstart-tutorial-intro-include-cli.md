## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Create a storage account

Az [az storage account create](/cli/azure/storage/account#create) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Tárfiók hitelesítő adatainak megadása

Az Azure CLI az oktatóanyagban szereplő parancsok legtöbbje esetén elkéri a tárfiók hitelesítő adatait. Ennek végrehajtására több lehetőség is rendelkezésre áll, amelyek közül az egyik legegyszerűbb az `AZURE_STORAGE_ACCOUNT` és az `AZURE_STORAGE_ACCESS_KEY` környezeti változó beállítása.

Elsőként jelenítse meg a tárfiókkulcsokat az [az storage account keys list](/cli/azure/storage/account/keys#list) parancs segítségével:

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Ez után adja meg az `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_ACCESS_KEY` környezeti változókat. Ezt a Bash-felületen az `export` parancs használatával is megteheti:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```