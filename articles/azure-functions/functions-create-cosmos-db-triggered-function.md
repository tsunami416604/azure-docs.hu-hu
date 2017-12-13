---
title: "Azure Cosmos DB által aktivált függvény létrehozása | Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyek akkor aktiválódnak, ha adatokat ad hozzá az Azure Cosmos DB egyik adatbázisához."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/02/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 1ff4c2e024faba777fc479b3cd5864e097bbfce1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB által aktivált függvény létrehozása

Megismerheti, hogyan hozhat létre egy olyan függvényt, amelyet az aktivál, ha adatokat ad hozzá az Azure Cosmos DB-hez, illetve abban található adatokat módosít. További információ az Azure Cosmos DB-ről: [Azure Cosmos DB: Kiszolgáló nélküli adatbázis-használat az Azure Functions szolgáltatással](..\cosmos-db\serverless-computing-database.md).

![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB-eseményindító létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét.

    ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. Keresse meg és válassza ki a kívánt nyelvű **Azure CosmosDBTrigger** sablont.

    ![Az Azure Cosmos DB-eseményindító létrehozása](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Az új eseményindító létrehozásához használja az ábra alatti táblázatban megadott beállításokat.

    ![Az Azure Cosmos DB-eseményindító létrehozása](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **A függvény neve** | Alapértelmezett | Használja a sablonban javasolt alapértelmezett függvénynevet. |
    | **Adatbázis neve** | Feladatok | A monitorozni kívánt gyűjteményt tartalmazó adatbázis neve. |
    | **Gyűjtemény neve** | Elemek | A monitorozni kívánt gyűjtemény neve. |
    | **A bérletek gyűjteményének létrehozása, ha az nem létezik** | Bejelölve | A gyűjtemény még nem létezik, hozza létre. |

4. Kattintson az **Új** gombra az **Azure Cosmos DB-fiókkapcsolat** címke mellett, és válasszon egy meglévő Cosmos DB-fiókot, vagy kattintson az **+ Új létrehozása** elemre. 
 
    ![Azure Cosmos DB-kapcsolat konfigurálása](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. Új Cosmos DB-fiók létrehozásakor használja a táblázatban megadott, **új fiókra** vonatkozó beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Azonosító** | Az adatbázis neve | Az Azure Cosmos DB adatbázis egyedi azonosítója  |
    | **API** | SQL (DocumentDB) | Ez a témakör a dokumentum-adatbázis API-ját használja.  |
    | **Előfizetés** | Azure-előfizetés | Azure-előfizetés  |
    | **Erőforráscsoport** | myResourceGroup |  Használja a függvényalkalmazást tartalmazó meglévő erőforráscsoportot. |
    | **Hely**  | WestEurope | Olyan helyet válasszon, amely közel van a függvényalkalmazáshoz vagy a tárolt dokumentumokat használó egyéb alkalmazásokhoz.  |

6. Az adatbázis létrehozásához kattintson az **OK** gombra. Az adatbázis létrehozása eltarthat néhány percig. Az adatbázis létrehozása után a rendszer az adatbázis kapcsolati karakterláncát függvényalkalmazás-beállításként tárolja. Ennek az alkalmazásbeállításnak a neve van beszúrva az **Azure Cosmos DB-fiókkapcsolatba**. 

7. Kattintson a **Létrehozás** elemre az Azure Cosmos DB által aktivált függvény létrehozásához. Miután a függvény létrejött, megjelenik a sablonalapú függvénykód.  

    ![Cosmos DB-függvénysablon C# környezetben](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Ez a függvénysablon a naplókba írja a dokumentumok számát és az első dokumentumazonosítót. 

Ezután csatlakozzon az Azure Cosmos DB-fiókjához, és hozza létre a **Feladatok** gyűjteményt az adatbázisban. 

## <a name="create-the-items-collection"></a>Az Elemek gyűjtemény létrehozása

1. Nyissa meg az [Azure Portal](https://portal.azure.com) egy újabb példányát egy új böngészőlapon. 

2. A portál bal oldali menüjében bontsa ki az ikonsort, gépelje be a `cosmos` szöveget a keresőmezőbe, és válassza az **Azure Cosmos DB** lehetőséget.

    ![Az Azure Cosmos DB szolgáltatás megkeresése](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Válassza ki Azure Cosmos DB-fiókját, majd válassza az**Adatkezelő** lehetőséget. 
 
3. A **Gyűjtemények** területen válassza a **taskDatabase**, majd az **Új gyűjtemény** elemet.

    ![Gyűjtemény létrehozása](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. A **Gyűjtemény hozzáadása** területen használja az ábra alatti táblázatban látható beállításokat. 
 
    ![A taskCollection elem meghatározása](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Beállítás|Ajánlott érték|Leírás |
    | ---|---|--- |
    | **Adatbázis-azonosító** | Feladatok |Az új adatbázis neve. A névnek meg kell egyeznie a függvénykötésben meghatározott névvel. |
    | **Gyűjtemény azonosítója** | Elemek | Az új gyűjtemény neve. A névnek meg kell egyeznie a függvénykötésben meghatározott névvel.  |
    | **Tárkapacitás** | Rögzített méretű (10 GB)|Használja az alapértelmezett értéket. Ez az érték az adatbázis tárkapacitása. |
    | **Átviteli sebesség** |400 kérelemegység| Használja az alapértelmezett értéket. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. |
    | **[Partíciókulcs](../cosmos-db/partition-data.md#design-for-partitioning)** | /kategória|Az egyes partíciók között az adatokat egyenletesen elosztó partíciókulcs. A megfelelő partíciókulcs kiválasztása fontos a nagy teljesítményű gyűjtemények létrehozásához. | 

1. A **Feladatok** gyűjtemény létrehozásához kattintson az **OK** gombra. A gyűjtemény létrehozása egy kis időt vehet igénybe.

A függvénykötésben megadott gyűjtemény létrejötte után tesztelheti a függvényt úgy, hogy dokumentumokat ad az új gyűjteményhez.

## <a name="test-the-function"></a>A függvény tesztelése

1. Bontsa ki az új **taskCollection** gyűjteményt az Adatkezelőben, és válassza a **Dokumentumok**, majd az **Új dokumentum** elemet.

    ![Dokumentum létrehozása a taskCollection gyűjteményben](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Cserélje le az új dokumentum tartalmát a következő tartalomra, majd válassza a **Mentés** elemet.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Váltson a böngészőben az első lapra, amely tartalmazza a függvényt a portálon. Bontsa ki a függvénynaplókat, és ellenőrizze, hogy az új dokumentum aktiválta-e a függvényt. Meggyőződhet arról, hogy a `task1` dokumentumazonosító értéke a naplókba lett írva. 

    ![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Nem kötelező) Térjen vissza a dokumentumhoz, hajtson végre egy módosítást, majd kattintson a **Frissítés** elemre. Térjen vissza a függvénynaplókhoz és ellenőrizze, hogy a frissítés is aktiválta-e a függvényt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor dokumentum hozzáadása vagy módosítása történik az Azure Cosmos DB-ben.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információ a tárolási üzenetsor eseményindítóiról: [Azure Functions – a tárolási üzenetsor kötései](functions-bindings-storage-queue.md).
