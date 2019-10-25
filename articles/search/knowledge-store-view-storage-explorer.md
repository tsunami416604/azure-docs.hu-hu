---
title: Tudásbázis megtekintése Storage Explorer
titleSuffix: Azure Cognitive Search
description: Megtekintheti és elemezheti az Azure Cognitive Search Knowledge Store-t a Azure Portal Storage Explorerával.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: cfa85e61059e27cd39a9701a835a725e16e5bc0a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789979"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Tudásbázis megtekintése Storage Explorer

> [!Note]
> A Knowledge Store előzetes verzióban érhető el, és nem használható éles környezetben. Az [Azure Cognitive Search REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>
Ebből a cikkből megtudhatja, hogyan csatlakozhat és vizsgálhat meg egy Knowledge Store-t a Azure Portal Storage Explorer használatával. Az útmutatóban használt Knowledge Store-minta létrehozásával kapcsolatban tekintse meg a [Knowledge Store létrehozása a Azure Portalban](knowledge-store-create-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

+ A jelen útmutatóban használt mintaűrlapok létrehozásához kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit.

+ Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Tudásbázis megtekintése, szerkesztése és lekérdezése Storage Explorer

1. A Azure Portal nyissa meg azt a Storage-fiókot, amelyet a Knowledge Store létrehozásához használt.

1. A Storage-fiók bal oldali navigációs paneljén kattintson a **Storage Explorer**elemre.

1. A **Tables (táblák** ) lista kibontásával megjelenítheti az Azure Table-előrejelzések listáját, amelyeket a rendszer a mintaadatok **importálása** varázsló futtatásakor hozott létre.

Bármelyik táblát kiválasztva megtekintheti a dúsított adatelemeket, beleértve a főbb kifejezéseket is, és így tovább.

   ![Táblázatok megtekintése Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Táblázatok megtekintése Storage Explorer")

Ha módosítani szeretné bármely tábla értékének adattípusát, vagy módosítani szeretné a tábla egyes értékeit, kattintson a **Szerkesztés**gombra. Ha egy tábla bármely oszlopának adattípusát megváltoztatja, az összes sorra lesz alkalmazva.

   ![Táblázat szerkesztése Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Táblázat szerkesztése Storage Explorer")

A lekérdezések futtatásához kattintson a parancssorban a **lekérdezés** elemre, és adja meg a feltételeit.  

   ![Lekérdezési táblázat Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Lekérdezési táblázat Storage Explorer")

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan csatlakoztatható a Power BI a Knowledge Store-hoz.

> [!div class="nextstepaction"]
> [Kapcsolódás PowerBI-jal](knowledge-store-connect-power-bi.md)

A következő cikkből megtudhatja, hogyan hozhat létre egy tudásbázist a REST API-k és a Poster használatával.  

> [!div class="nextstepaction"]
> [Hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-howto.md)
