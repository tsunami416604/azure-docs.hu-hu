---
title: Azure Cosmos DB által aktivált függvény létrehozása
description: Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyek akkor aktiválódnak, ha adatokat ad hozzá az Azure Cosmos DB egyik adatbázisához.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: quickstart
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 830c7cdee247118ed24fc9b3a2a9efe8609c75d0
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863281"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB által aktivált függvény létrehozása

Megismerheti, hogyan hozhat létre egy olyan függvényt, amelyet az aktivál, ha adatokat ad hozzá az Azure Cosmos DB-hez, illetve abban található adatokat módosít. További információ az Azure Cosmos DB-ről: [Azure Cosmos DB: Kiszolgáló nélküli adatbázis-használat az Azure Functions szolgáltatással](../cosmos-db/serverless-computing-database.md).

![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

A trigger létrehozásához rendelkeznie kell egy SQL API-t használó Azure Cosmos DB-fiókkal.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB-eseményindító létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ha nem ez az első, folytassa a harmadik lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. A keresőmezőbe írja be a `cosmos` kifejezést, majd válassza ki az **Azure Cosmos DB trigger** sablont.

1. Ha a rendszer kéri, válassza a **telepítés** lehetőséget a Azure Cosmos db bővítmény telepítéséhez a Function alkalmazásban. A telepítést követően válassza a **Folytatás** gombot.

    ![Kötési bővítmények telepítése](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Az új eseményindító létrehozásához használja az ábra alatti táblázatban megadott beállításokat.

    ![Az Azure Cosmos DB-eseményindító létrehozása](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Name (Név)** | Alapértelmezett | Használja a sablonban javasolt alapértelmezett függvénynevet.|
    | **Azure Cosmos DB-fiók kapcsolata** | Új beállítás | Válassza ki az **Új** gombot, majd válassza ki az **előfizetését**, a korábban létrehozott **adatbázisfiókot**, és a **Kiválasztás** lehetőséget. Ezzel létrehoz egy alkalmazásbeállítást a fiókkapcsolathoz. Ezt a beállítást használja a kötés, hogy kapcsolódjon az adatbázishoz. |
    | **Tároló neve** | Elemek | A figyelni kívánt tároló neve. |
    | **Bérleti tároló létrehozása, ha nem létezik** | Bejelölve | A tároló még nem létezik, ezért hozza létre. |
    | **Adatbázis neve** | Feladatok | A figyelni kívánt tárolót tartalmazó adatbázis neve. |

1. Kattintson a **Létrehozás** elemre az Azure Cosmos DB által aktivált függvény létrehozásához. Miután a függvény létrejött, megjelenik a sablonalapú függvénykód.  

    ![Cosmos DB-függvénysablon C# környezetben](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Ez a függvénysablon a naplókba írja a dokumentumok számát és az első dokumentumazonosítót.

Ezután kapcsolódjon a Azure Cosmos DB-fiókjához, és hozza létre a `Items` tárolót a `Tasks` adatbázisban.

## <a name="create-the-items-container"></a>Az Items tároló létrehozása

1. Nyissa meg az [Azure Portal](https://portal.azure.com) egy újabb példányát egy új böngészőlapon.

1. A portál bal oldali menüjében bontsa ki az ikonsort, gépelje be a `cosmos` szöveget a keresőmezőbe, és válassza az **Azure Cosmos DB** lehetőséget.

    ![Az Azure Cosmos DB szolgáltatás megkeresése](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Válassza ki Azure Cosmos DB-fiókját, majd válassza az**Adatkezelő** lehetőséget. 

1. Az **SQL API**alatt válassza a **feladatok** adatbázis lehetőséget, és válassza az **új tároló**elemet.

    ![Tároló létrehozása](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. A **tároló hozzáadása**területen használja a rendszerkép alatti táblázatban látható beállításokat. 

    ![A feladatok tárolójának megadása](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Beállítás|Ajánlott érték|Leírás |
    | ---|---|--- |
    | **Adatbázis-azonosító** | Feladatok |Az új adatbázis neve. A névnek meg kell egyeznie a függvénykötésben meghatározott névvel. |
    | **Tároló azonosítója** | Elemek | Az új tároló neve. A névnek meg kell egyeznie a függvénykötésben meghatározott névvel.  |
    | **[Partíciókulcs](../cosmos-db/partition-data.md)** | /kategória|Az egyes partíciók között az adatokat egyenletesen elosztó partíciókulcs. A megfelelő partíciós kulcs kiválasztásával fontos szerepet játszik a teljesítményű tárolók létrehozásában. | 
    | **Átviteli sebesség** |400 kérelemegység| Használja az alapértelmezett értéket. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. |    

1. Az elemek tároló létrehozásához kattintson **az OK** gombra. Eltarthat egy rövid ideig, amíg a tároló létre nem kerül.

Ha a függvény kötésében megadott tároló létezik, akkor tesztelheti a függvényt, ha elemeket ad hozzá az új tárolóhoz.

## <a name="test-the-function"></a>A függvény tesztelése

1. Bontsa ki az új **elemek** tárolót Adatkezelőban, majd válassza az **elemek**, majd az **új elem**lehetőséget.

    ![Elem létrehozása az elemek tárolóban](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. Cserélje le az új elem tartalmát a következő tartalomra, majd válassza a **Mentés**lehetőséget.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Váltson a böngészőben az első lapra, amely tartalmazza a függvényt a portálon. Bontsa ki a függvénynaplókat, és ellenőrizze, hogy az új dokumentum aktiválta-e a függvényt. Meggyőződhet arról, hogy a `task1` dokumentumazonosító értéke a naplókba lett írva. 

    ![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Nem kötelező) Térjen vissza a dokumentumhoz, hajtson végre egy módosítást, majd kattintson a **Frissítés** elemre. Térjen vissza a függvénynaplókhoz és ellenőrizze, hogy a frissítés is aktiválta-e a függvényt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor dokumentum hozzáadása vagy módosítása történik az Azure Cosmos DB-ben. További információ az Azure Cosmos DB-eseményindítókról: [Azure Cosmos DB – Azure Functions kötések](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
