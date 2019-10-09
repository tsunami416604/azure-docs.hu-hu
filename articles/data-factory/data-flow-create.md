---
title: Azure Data Factory leképezési adatfolyam létrehozása
description: Azure Data Factory leképezési adatfolyam létrehozása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: d8ebc026add9dfd85d7cbe312a8cfffd3e365f82
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026883"
---
# <a name="create-azure-data-factory-data-flow"></a>Azure Data Factory adatfolyam létrehozása



Az ADF-ben lévő adatfolyamatok leképezése lehetővé teszi, hogy az adatforgalom méretezés nélkül, kódolás nélkül legyen átalakítva. Az Adatátalakítási feladatokat az adatáramlás-tervezőben alakíthatja ki átalakítások sorozatának létrehozásával. Az Adatátalakítási lépések után tetszőleges számú forrás-átalakítást indíthat. Ezután fejezze be az adatfolyamot a fogadóval, hogy az eredményeket egy célhelyen lehessen kirakodni.

Első lépésként hozzon létre egy új v2 Data Factory a Azure Portal. Miután létrehozta az új gyárat, kattintson az "author & monitor" csempére az Data Factory felhasználói felületének elindításához.

Adatfolyam- ![Beállítások](media/data-flow/v2portal.png "adatfolyam-létrehozási folyamata")

Ha a Data Factory felhasználói felületén van, használhat minta adatfolyamatokat. A minták az ADF-sablon katalógusában érhetők el. Az ADF-ben hozza létre a "folyamat sablonból" lehetőséget, és válassza ki az adatfolyam kategóriát a sablon-katalógusból.

Adatfolyam- ![Beállítások](media/data-flow/template.png "adatfolyam-létrehozási folyamata")

A rendszer kérni fogja, hogy adja meg az Azure-Blob Storage fiókjának adatait.

Adatfolyam- ![Beállítások](media/data-flow/template2.png "adatfolyamának létrehozása 2")

[A mintákhoz használt adathalmaz itt található](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Töltse le a mintaadatok adatait, és tárolja a fájlokat az Azure Blob Storage-fiókjaiban, hogy végre tudja hajtani a mintákat.

## <a name="create-new-data-flow"></a>Új adatfolyam létrehozása

Adatfolyamatok létrehozásához használja az ADF felhasználói felületének erőforrás létrehozása "plusz aláírás" gombját.

![Adatfolyam-beállítások](media/data-flow/newresource.png "új erőforrás")

## <a name="next-steps"></a>További lépések

Kezdje el felépíteni az adatátalakítást [forrás-átalakítással](data-flow-source.md).
