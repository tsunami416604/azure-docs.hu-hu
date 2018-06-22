## <a name="set-up-azure-cli-for-azure-dns"></a>Az Azure parancssori felület (CLI) beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse az Azure parancssori felület (CLI) legújabb verzióját, amely Windows, Linux és Mac platformon is elérhető. További információt az [Azure parancssori felület (CLI) telepítése](../articles/cli-install-nodejs.md) című cikkben olvashat.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyisson meg egy konzolablakot, adja meg a saját hitelesítő adatait. További információkért lásd: [jelentkezzen be az Azure-bA az Azure parancssori felületen](/cli/azure/authenticate-azure-cli)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Kapcsolja át a parancssori felület működési módját

Az Azure DNS az Azure Resource Managert használja. Váltson át a parancssori felület megfelelő módjára az Azure Resource Manager-parancsok használatához.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést

Keresse meg a fiókot az előfizetésekben.

```azurecli
azure account list
```

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Az Azure DNS szolgáltatást a Microsoft.Network erőforrás-szolgáltató kezeli. Az Azure DNS használatbavétele előtt az Azure-előfizetést regisztrálni kell ennek az erőforrás-szolgáltatónak a használatához. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```azurecli
azure provider register --namespace Microsoft.Network
```

