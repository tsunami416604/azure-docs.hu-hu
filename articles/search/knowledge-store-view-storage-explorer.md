---
title: Knowledge Store (előzetes verzió) megtekintése Storage Explorer
titleSuffix: Azure Cognitive Search
description: Megtekintheti és elemezheti az Azure Cognitive Search Knowledge Store-t a Azure Portal Storage Explorerával. A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754070"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Tudásbázis megtekintése Storage Explorer

> [!IMPORTANT] 
> A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) előzetes funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Ebből a cikkből megtudhatja, hogyan csatlakozhat a tudásbázishoz, és hogyan derítheti fel a Storage Explorer használatával a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

+ A jelen útmutatóban használt mintaűrlapok létrehozásához kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit.

+ Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Egy Tudásbázis megtekintése, szerkesztése és lekérdezése Storage Explorer

1. A Azure Portal [nyissa meg](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) azt a Storage-fiókot, amelyet a Knowledge Store létrehozásához használt.

1. A Storage-fiók bal oldali navigációs paneljén kattintson a **Storage Explorer**elemre.

1. A **Tables (táblák** ) lista kibontásával megjelenítheti az Azure Table-előrejelzések listáját, amelyeket a rendszer a mintaadatok **importálása** varázsló futtatásakor hozott létre.

Bármelyik táblát kiválasztva megtekintheti a dúsított adatelemeket, beleértve a legfontosabb kifejezéseket és a hangulati pontszámokat.

   ![Táblázatok megtekintése Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Táblázatok megtekintése Storage Explorer")

Ha módosítani szeretné bármely tábla értékének adattípusát, vagy módosítani szeretné a tábla egyes értékeit, kattintson a **Szerkesztés**gombra. Ha egy tábla bármely oszlopának adattípusát megváltoztatja, az összes sorra lesz alkalmazva.

   ![Táblázat szerkesztése Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Táblázat szerkesztése Storage Explorer")

A lekérdezések futtatásához kattintson a parancssorban a **lekérdezés** elemre, és adja meg a feltételeit.  

   ![Lekérdezési táblázat Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Lekérdezési táblázat Storage Explorer")

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások díjfizetéssel járhatnak. Az erőforrásokat törölheti egyesével, vagy egyszerre, az erőforráscsoport törlésével.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

A Knowledge Store-t összekapcsolhatjuk a mélyebb elemzés érdekében, vagy a REST API és a Poster használatával egy másik Tudásbázis létrehozásával Power BI.

> [!div class="nextstepaction"]
> [Kapcsolódjon Power BI](knowledge-store-connect-power-bi.md)
> [hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-create-rest.md)
