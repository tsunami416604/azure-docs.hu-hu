---
title: Tudástároló megtekintése (előzetes verzió) a Tároló kezelővel
titleSuffix: Azure Cognitive Search
description: Tekintse meg és elemezze az Azure Cognitive Search tudástárolóját az Azure Portal Storage Explorer használatával. tudástároló jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754070"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Tudástároló megtekintése a Tároló kezelővel

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Ebben a cikkben például megtudhatja, hogyan csatlakozhat egy tudástárolóhoz az Azure Portalon a Storage Explorer használatával.

## <a name="prerequisites"></a>Előfeltételek

+ Kövesse a [Tudástároló létrehozása](knowledge-store-create-portal.md) az Azure Portalon című, a forgatókönyvben használt mintatudástár létrehozásának lépéseit.

+ Szüksége lesz a tudástároló létrehozásához használt Azure storage-fiók nevére, valamint az Azure Portalon található hozzáférési kulcsra is.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Tudástároló megtekintése, szerkesztése és lekérdezése a Tárolókezelőben

1. Az Azure Portalon [nyissa meg a tudástároló](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) létrehozásához használt tárfiókot.

1. A tárfiók bal oldali navigációs ablakában kattintson a **Tárolókezelő**elemre.

1. **Bontsa** ki a TABLES listát az Azure-táblázat vetületeinek listájának megjelenítéséhez, amelyek az **Adatok importálása** varázsló nak a szállodai véleményezési mintaadatokon való futtatásához létrejöttek.

Bármelyik táblázatot kijelölve megtekintheti a bővített adatokat, beleértve a legfontosabb kifejezéseket és a véleménypontszámokat.

   ![Táblázatok megtekintése a Tárolókezelőben](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Táblázatok megtekintése a Tárolókezelőben")

Bármely táblaérték adattípusának módosításához vagy a táblázat egyes értékeinek módosításához kattintson a **Szerkesztés**gombra. Ha egy táblázatsor bármely oszlopának adattípusát módosítja, az az összes sorra vonatkozik.

   ![Táblázat szerkesztése a Tárolókezelőben](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Táblázat szerkesztése a Tárolókezelőben")

A lekérdezések futtatásához kattintson a parancssáv **Lekérdezés** gombjára, és adja meg a feltételeket.  

   ![Lekérdezési tábla a Tárolókezelőben](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Lekérdezési tábla a Tárolókezelőben")

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>További lépések

Csatlakoztassa ezt a tudástárolót a Power BI-hoz mélyebb elemzéshez, vagy lépjen előre a kódokkal a REST API és a Postman segítségével egy másik tudástároló létrehozásához.

> [!div class="nextstepaction"]
> [Csatlakozás a Power BI-val](knowledge-store-connect-power-bi.md)
> [Tudástár létrehozása rest-en](knowledge-store-create-rest.md)
