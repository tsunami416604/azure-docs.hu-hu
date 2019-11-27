---
title: Knowledge Store (előzetes verzió) megtekintése Storage Explorer
titleSuffix: Azure Cognitive Search
description: Megtekintheti és elemezheti az Azure Cognitive Search Knowledge Store-t a Azure Portal Storage Explorerával. A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2df05cf20ef51b2d5ca866f22bd9450dd6acaf
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406553"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Tudásbázis megtekintése Storage Explorer

> [!IMPORTANT] 
> A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) előzetes funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Ebből a cikkből megtudhatja, hogyan csatlakozhat a tudásbázishoz, és hogyan derítheti fel a Storage Explorer használatával a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

+ Kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit, vagy [hozzon létre egy Azure Cognitive Search Knowledge Store](knowledge-store-create-rest.md) -t a jelen útmutatóban használt minta TUDÁSBÁZIS létrehozásához a REST használatával.

+ Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Egy Tudásbázis megtekintése, szerkesztése és lekérdezése Storage Explorer

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

## <a name="next-steps"></a>Következő lépések

A Knowledge Store-t összekapcsolhatjuk a mélyebb elemzés érdekében, vagy a REST API és a Poster használatával egy másik Tudásbázis létrehozásával Power BI.

> [!div class="nextstepaction"]
> [Kapcsolódjon Power BI](knowledge-store-connect-power-bi.md)
> [hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-howto.md)
