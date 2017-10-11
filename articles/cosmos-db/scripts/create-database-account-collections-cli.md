---
title: "Az Azure CLI parancsfájl-Azure Cosmos DB DocumentDB API-fiók létrehozása, az adatbázis és a gyűjtemény |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – egy Azure Cosmos DB DocumentDB API-fiók, adatbázis és gyűjtemény létrehozása"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/06/2017
ms.author: mimig
ms.openlocfilehash: 28f99d56404e47adcd375d9f3106cc234469cbfd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cosmos-db-create-an-documentdb-api-account-using-cli"></a>Az Azure Cosmos DB: Parancssori felület használatával DocumentDB API-fiók létrehozása

A parancsfájlpéldát CLI Azure Cosmos DB DocumentDB API fiók, adatbázis és gyűjtemény létrehozása  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[fő](../../../cli_scripts/cosmosdb/create-cosmosdb-account-database/create-cosmosdb-account-database.sh?highlight=15-35 "egy Azure Cosmos DB DocumentDB API-fiók, adatbázis és gyűjtemény létrehozása")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az cosmosdb létrehozása](/cli/azure/cosmosdb#create) | Azure Cosmos DB fiókot hoz létre. |
| [az csoport törlése](/cli/azure/resource#delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Cosmos DB CLI parancsfájl minták megtalálhatók a [Azure Cosmos DB CLI dokumentáció](../cli-samples.md).
