---
title: Tudásbázis megtekintése Storage Explorer
titleSuffix: Azure Cognitive Search
description: Megtekintheti és elemezheti az Azure Cognitive Search Knowledge Store-t a Azure Portal Storage Explorerával.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9ba11b69a51a5bc563764a7e75189bed67cb2ac6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484995"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Tudásbázis megtekintése Storage Explorer

> [!Note]
> A Knowledge Store előzetes verzióban érhető el, és nem használható éles környezetben. A portál és a [Search REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>
Ebből a cikkből megtudhatja, hogyan csatlakozhat a tudásbázishoz, és hogyan derítheti fel a Storage Explorer használatával a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

+ A jelen útmutatóban használt mintaűrlapok létrehozásához kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit.

+ Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Tudásbázis megtekintése, szerkesztése és lekérdezése Storage Explorer

1. A Azure Portal [nyissa meg](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) azt a Storage-fiókot, amelyet a Knowledge Store létrehozásához használt.

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

## <a name="next-steps"></a>További lépések

A Knowledge Store-t összekapcsolhatjuk a mélyebb elemzés érdekében, vagy a REST API és a Poster használatával egy másik Tudásbázis létrehozásával Power BI.

> [!div class="nextstepaction"]
> [Kapcsolódjon Power BI](knowledge-store-connect-power-bi.md)
> [hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-howto.md)
