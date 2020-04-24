---
title: A szinapszis Studio használata (előzetes verzió)
description: Ebből a rövid útmutatóból megtudhatja, hogy milyen egyszerűen lekérdezheti a különböző típusú fájlokat a szinapszis Studio használatával.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a29fd66cf264b09cc5e0db7ac1a329be3f297bb8
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096333"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Rövid útmutató: a szinapszis Studio használata (előzetes verzió)

Ebből a rövid útmutatóból megtudhatja, hogyan lehet fájlokat lekérdezni a szinapszis Studio használatával.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Azure szinapszis-munkaterületet és egy társított Storage-fiókot](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>A szinapszis Studio elindítása

A Azure Portal Azure szinapszis munkaterületén kattintson a **szinapszis Studio elindítása**elemre.

![A szinapszis Studio elindítása](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Azt is megteheti, hogy az [Azure szinapszis Analytics](https://web.azuresynapse.net) elemre kattintva elindítja a szinapszis Studio alkalmazást, és megadja a megfelelő bérlői, előfizetési és munkaterület-értékeket.

## <a name="browse-storage-accounts"></a>Storage-fiókok tallózása

Miután megnyitotta a szinapszis Studio alkalmazást, tallózással keresse meg az **adatvédelmet** , majd bontsa ki a **Storage-fiókok** elemet a Storage-fiók megnyitásához

![Fájlok tallózása a tárhelyen](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Új mappákat hozhat létre, és fájlokat tölthet fel az eszköztárban található hivatkozások használatával a fájlok rendszerezéséhez.

## <a name="query-files-on-storage-account"></a>Fájlok lekérdezése a Storage-fiókban

> [!IMPORTANT]
> Ahhoz, hogy le tudja kérdezni a `Storage Blob Reader` fájlokat, a mögöttes tárolóban lévő szerepkör tagjának kell lennie. Megtudhatja, hogyan [rendelhet hozzá **Storage blob-adatolvasót** vagy **Storage blob-adatközreműködői** RBAC engedélyeket az Azure Storage-](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)ban.

1. Töltsön fel `PARQUET` néhány fájlt.
2. Válasszon ki egy vagy több fájlt, majd hozzon létre egy új SQL-szkriptet vagy egy Spark-jegyzetfüzetet a fájlok tartalmának megtekintéséhez. Ha jegyzetfüzetet szeretne létrehozni, létre kell hoznia [Apache Spark készletet a munkaterületen](quickstart-create-apache-spark-pool.md).

   ![Fájlok lekérdezése a tárhelyen](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Futtassa a generált lekérdezést vagy jegyzetfüzetet a fájl tartalmának megtekintéséhez:

   ![A fájl tartalmának megjelenítése](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. A lekérdezés módosításával szűrheti és rendezheti az eredményeket. Megtalálhatja az SQL igény szerint elérhető nyelvi funkcióit az [SQL-funkciók áttekintésében](sql/overview-features.md).

## <a name="next-steps"></a>További lépések

- Az Azure AD-felhasználók számára lehetővé teszi fájlok lekérdezését [a **Storage blob-Adatolvasó** vagy a **Storage blob adatközreműködői** RBAC engedélyeinek hozzárendelésével az Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role) szolgáltatásban
- [Fájlok lekérdezése az Azure Storage-ban az SQL igény szerinti használatával](sql/on-demand-workspace-overview.md)
- [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool.md)
- [Power BI jelentés létrehozása az Azure Storage-ban tárolt fájlokról](sql/tutorial-connect-power-bi-desktop.md)
