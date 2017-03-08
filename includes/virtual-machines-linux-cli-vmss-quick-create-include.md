## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, beszerezheti az [Azure-előfizetés ingyenes próbaverzióját](https://azure.microsoft.com/pricing/free-trial/), és telepítheti az[Azure CLI 2.0-t](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>A méretezési csoport létrehozása

Először is hozzon létre egy erőforráscsoportot, amelyben a méretezési csoportot üzembe helyezheti:

```azurecli
az group create --location westus --name myResourceGroup
```

Hozzon létre egy méretezési csoport a(z) `az vmss create` parancs használatával. A következő példában létrehozunk egy `myvmss` elnevezésű, Linux rendszerű méretezési csoportot a `myrg` nevű erőforráscsoportban:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

A következő példa egy Windows rendszerű méretezési csoportot hoz létre ugyanazzal a konfigurációval:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Ha másik operációsrendszer-képet kíván választani, a `az vm image list` vagy `az vm image list --all` parancsok használatával megtekintheti az elérhető rendszerképeket. A méretezési csoportban lévő virtuális gépekhez tartozó kapcsolati információk megtekintéséhez használja a `az vmss list_instance_connection_info` parancsot:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```