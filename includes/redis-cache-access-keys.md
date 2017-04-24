Ha Azure Redis Cache gyorsítótárhoz szeretne csatlakozni, a gyorsítótárügyfeleknek szüksége lesz az állomásnévre, a portokra és a gyorsítótár kulcsaira. Egyes ügyfelek különböző neveken hivatkozhatnak ezekre az elemekre. Ezt az információt az Azure Portalon vagy parancssori eszközök, például az Azure CLI segítségével kérheti le.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Gazdagépnév, portok és hozzáférési kulcsok lekérése az Azure Portalon
A gazdagépnév, a portok és a hozzáférési kulcsok Azure portalon történő lekéréséhez [tallózzon](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) a saját gyorsítótárához az [Azure Portalon](https://portal.azure.com), és kattintson a **Hozzáférési kulcsok** és **Tulajdonságok** elemekre az **Erőforrás menüben**. 

![A Redis Cache-gyorsítótár beállításai](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Gazdagépnév, portok és hozzáférési kulcsok lekérése az Azure CLI-vel
A gazdagépnév és a portok Azure CLI 2.0-val való lekéréséhez hívja meg az [az redis show](https://docs.microsoft.com/cli/azure/redis#show), a kulcsok lekéréséhez pedig az [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys) parancsot. A következő szkript meghívja ezt a két parancsot, majd továbbítja a gazdagépnevet, a portokat és a kulcsokat a konzolnak.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

A szkripttel kapcsolatos további információkért lásd [az Azure Redis Cache gazdagépnevének, portjainak és kulcsainak lekérésével](../articles/redis-cache/scripts/cache-keys-ports.md) foglalkozó cikket. Az Azure CLI 2.0-val kapcsolatos további információkat [az Azure CLI 2.0 telepítésével](https://docs.microsoft.com/cli/azure/install-azure-cli) foglalkozó cikkben és [az Azure CLI 2.0-s verziójának használatát ismertető](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) bevezetőben talál.
