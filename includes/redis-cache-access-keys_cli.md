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
ms.openlocfilehash: 0289604cce7f956406d65743d5b058ec92111724
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Gazdagépnév, portok és hozzáférési kulcsok lekérése az Azure CLI-vel
A gazdagépnév és a portok Azure CLI 2.0-val való lekéréséhez hívja meg az [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), a kulcsok lekéréséhez pedig az [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) parancsot. A következő szkript meghívja ezt a két parancsot, majd továbbítja a gazdagépnevet, a portokat és a kulcsokat a konzolnak.

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
