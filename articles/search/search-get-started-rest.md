---
title: 'Gyors útmutató: keresési index létrehozása REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebből a REST API útmutatóból megtudhatja, hogyan hívhatja meg az Azure Cognitive Search REST API-kat a Poster vagy a Visual Studio Code használatával.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714167"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Rövid útmutató: Azure Cognitive Search index létrehozása REST API-k használatával

Ez a cikk azt ismerteti, hogyan lehet interaktív módon kialakítani REST API kérelmeket az [Azure Cognitive Search REST API](/rest/api/searchservice) -kkal és egy API-ügyféllel a kérelmek küldéséhez és fogadásához. Az API-ügyféllel és ezekkel az utasításokkal bármilyen kód írása előtt elküldheti a kérelmeket, és megtekintheti a válaszokat.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hajthat végre alapszintű feladatokat, további REST API hívásokat is megadhat további speciális funkciókhoz, például indexelő elemekhez, vagy [egy olyan dúsítási folyamat beállításához](cognitive-search-tutorial-blob.md) , amely tartalom-átalakításokat ad át az indexeléshez. A következő lépésként a következő hivatkozást javasoljuk:

> [!div class="nextstepaction"]
> [Oktatóanyag: az Azure-Blobok kereshető tartalmának létrehozásához használja a REST és a AI használatát](cognitive-search-tutorial-blob.md)