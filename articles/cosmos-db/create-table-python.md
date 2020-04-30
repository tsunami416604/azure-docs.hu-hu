---
title: 'Gyors útmutató: Table API a Python-Azure Cosmos DB'
description: Ez a gyors útmutató azt ismerteti, hogy hogyan használható az Azure Cosmos DB Table API alkalmazások létrehozására az Azure Portal és a Python használatával
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 673155706c3b7d44b41562c2dc8ce24a94ac6a58
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083631"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Gyors útmutató: Table API-alkalmazás felépítése a Python és az Azure Cosmos DB használatával

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Ebben a rövid útmutatóban egy Azure Cosmos DB Table API fiókot hoz létre és felügyel a Azure Portalból és a Visual studióból a GitHubról klónozott Python-alkalmazással. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is a `https://localhost:8081` és a kulcs `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`URI-ja használatával.
- [Visual Studio 2019](https://www.visualstudio.com/downloads/), a telepítés során kiválasztott **Azure-fejlesztési** és **Python-fejlesztési** munkaterhelésekkel. 
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

> [!IMPORTANT] 
> Létre kell hoznia egy új Table API-fiókot, amely használható az általánosan elérhető Table API SDK-kkal. Az általánosan elérhető SDK-k nem támogatják az előzetes verzióban létrehozott Table API-fiókokat.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Ezután nyissa meg a megoldásfájlt a Visual Studióban. 

> ! Tipp A hasonló kódok részletesebb ismertetése: [Cosmos DB Table API minta](table-storage-how-to-use-python.md) cikk.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kapcsolatok karakterlánc**lehetőséget. 

    ![A kapcsolatok karakterláncának megtekintése és másolása a kapcsolatok karakterláncának beállításaiban](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Másolja az ACCOUNT NAME (Fiók neve) értékét a jobb oldalon található gombbal.

3. Nyissa meg a *config.py* fájlt, és illessze be a fiók nevét a portálról a 19. sorban lévő STORAGE_ACCOUNT_NAME értékre.

4. Lépjen vissza a portálra, és másolja a vágólapra a PRIMARY KEY (Elsődleges kulcs) értékét.

5. Illessze be a PRIMARY KEY értékét a portálról a STORAGE_ACCOUNT_KEY mezőbe a 20. sorban.

6. Mentse a *config.py* fájlt.

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A Visual Studióban kattintson a jobb gombbal a projektre **megoldáskezelő**.

2. Válassza ki az aktuális Python-környezetet, majd kattintson a jobb gombbal.

2. Válassza a **Python-csomag telepítése**lehetőséget, majd adja meg az *Azure-Storage-Table*értéket.

3. Az alkalmazás futtatásához nyomja le az F5 billentyűt. Az alkalmazás megjelenik a böngészőben. 

Most visszatérhet a Adatkezelőra, és megtekintheti, lekérdezheti, módosíthatja és dolgozhat az új adattal. 

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB fiókot, hogyan hozhat létre táblát a Adatkezelő használatával, és hogyan futtathat Python-alkalmazást a Visual Studióban a tábla adatai hozzáadásához.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Táblaadatok importálása a Table API-ba](table-import.md)
