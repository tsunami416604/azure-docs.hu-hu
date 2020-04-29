---
title: Leképezési adatfolyam létrehozása
description: Azure Data Factory leképezési adatfolyam létrehozása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416516"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory-adatfolyam létrehozása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az ADF-ben lévő adatfolyamatok leképezése lehetővé teszi, hogy az adatforgalom méretezés nélkül, kódolás nélkül legyen átalakítva. Az Adatátalakítási feladatokat az adatáramlás-tervezőben alakíthatja ki átalakítások sorozatának létrehozásával. Az Adatátalakítási lépések után tetszőleges számú forrás-átalakítást indíthat. Ezután fejezze be az adatfolyamot a fogadóval, hogy az eredményeket egy célhelyen lehessen kirakodni.

Első lépésként hozzon létre egy új v2 Data Factory a Azure Portal. Miután létrehozta az új gyárat, kattintson az "author & monitor" csempére az Data Factory felhasználói felületének elindításához.

![Adatfolyam-beállítások](media/data-flow/v2portal.png "adatfolyam létrehozása")

Ha a Data Factory felhasználói felületén van, használhat minta adatfolyamatokat. A minták az ADF-sablon katalógusában érhetők el. Az ADF-ben hozza létre a "folyamat sablonból" lehetőséget, és válassza ki az adatfolyam kategóriát a sablon-katalógusból.

![Adatfolyam-beállítások](media/data-flow/template.png "adatfolyam létrehozása")

A rendszer kérni fogja, hogy adja meg az Azure-Blob Storage fiókjának adatait.

![Adatfolyam-beállítások](media/data-flow/template2.png "adatfolyam létrehozása 2")

[A mintákhoz használt adathalmaz itt található](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Töltse le a mintaadatok adatait, és tárolja a fájlokat az Azure Blob Storage-fiókjaiban, hogy végre tudja hajtani a mintákat.

## <a name="create-new-data-flow"></a>Új adatfolyam létrehozása

Adatfolyamatok létrehozásához használja az ADF felhasználói felületének erőforrás létrehozása "plusz aláírás" gombját.

![Adatfolyam-beállítások](media/data-flow/newresource.png "Új erőforrás")

## <a name="next-steps"></a>További lépések

Kezdje el felépíteni az adatátalakítást [forrás-átalakítással](data-flow-source.md).
