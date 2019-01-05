---
title: Angular alkalmazás létrehozása az Azure Cosmos DB API a mongodb-hez – egy Cosmos-fiók létrehozása
titleSuffix: Azure Cosmos DB
description: A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 4. része
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: 8320204f75e583dae0449f83e7c38f6638371c2a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035120"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Angular alkalmazás létrehozása az Azure Cosmos DB API a mongodb-hez – egy Cosmos-fiók létrehozása

Ez a többrészes oktatóanyag bemutatja, hogyan hozzon létre egy új alkalmazást az Express, Angular és Node.js nyelven írt, és hogyan csatlakoztathatja az a [Cosmos DB API a mongodb-hez konfigurált Cosmos-fiók](mongodb-introduction.md).

Az oktatóanyag 4. része a [3. részre](tutorial-develop-mongodb-nodejs-part3.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Azure-erőforráscsoport létrehozása az Azure CLI használatával
> * Hozzon létre egy Cosmos-fiókot az Azure CLI használatával

## <a name="video-walkthrough"></a>Bemutató videó

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp végezze el az oktatóanyag [3. részében](tutorial-develop-mongodb-nodejs-part3.md) ismertetett lépéseket. 

Az oktatóanyag ezen szakaszában az Azure Cloud Shellt (a webböngészőben) vagy a helyileg telepített [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli) eszközt használhatja.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos DB-fiókot a(z) [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) paranccsal.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* `<cosmosdb-name>` esetén használja a saját egyedi Azure Cosmos DB-fióknevét, a névnek egyedinek kell lennie az összes Azure-beli Azure Cosmos DB-fióknév között.
* A `--kind MongoDB` beállítás lehetővé teszi, hogy az Azure Cosmos DB MongoDB-ügyfélkapcsolatokkal rendelkezzen.

A parancs végrehajtása egy-két percet is igénybe vehet. Amint befejeződött, a terminálablakban megjelennek az új adatbázissal kapcsolatos információk. 

Amint az Azure Cosmos DB-fiók létrejött:
1. Nyisson meg egy új böngészőablakot, és lépjen a következő oldalra: [https://portal.azure.com](https://portal.azure.com)
1. Kattintson az Azure Cosmos DB emblémára ![Az Azure Cosmos DB ikonja az Azure Portalon](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) a bal oldali sávon, és az megjeleníti az összes rendelkezésre álló Azure Cosmos DB-adatbázist.
1. Kattintson az imént létrehozott Azure Cosmos DB-fiókra, válassza az **Overview** (Áttekintés) lapot, és görgessen le a térképig, amelyen az adatbázis található. 

    ![Új Azure Cosmos DB-fiók az Azure Portalon](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Görgessen le a bal oldali navigációs sávon, majd kattintson az **Replicate data globally** (Adatok globális replikálása) lapra. Ezzel megjelenít egy térképet, amelyen azokat a különböző területeket láthatja, ahová replikálhat. Például Délkelet-Ausztráliára vagy Kelet-Ausztráliára kattintva az adatokat Ausztráliába replikálhatja. A globális replikációval kapcsolatos további információkért lásd: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md). Egyelőre azonban elég lesz egyetlen példány, de majd ha replikálni szeretnénk, már tudjuk, hogyan kell.

    ![Új Azure Cosmos DB-fiók az Azure Portalon](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>További lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy Azure-erőforráscsoportot az Azure CLI használatával
> * Létrehozott egy Azure Cosmos DB-fiókot az Azure CLI használatával

Továbbléphet az oktatóanyag következő részére, amelyben az Azure Cosmos DB-adatbázist az alkalmazásához kapcsolja a Mongoose használatával.

> [!div class="nextstepaction"]
> [A Mongoose használata az Azure Cosmos DB-hez való csatlakozáshoz](tutorial-develop-mongodb-nodejs-part5.md)
