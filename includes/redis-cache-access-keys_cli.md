---
title: fájl belefoglalása
description: fájl belefoglalása
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: d32ea8af6ad8282872572076db4b9eef34e05166
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011863"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Gazdagépnév, portok és hozzáférési kulcsok lekérése az Azure CLI-vel

Az állomásnév és portok használata az Azure CLI segítségével meghívhatja lekéréséhez [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), és meghívhatja a kulcsok lekéréséhez [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). A következő szkript meghívja ezt a két parancsot, majd továbbítja a gazdagépnevet, a portokat és a kulcsokat a konzolnak.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

A szkripttel kapcsolatos további információkért lásd: [a gazdagépnév, portok és kulcsok lekérése az Azure az Azure Cache Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). További információ az Azure CLI-vel: [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
