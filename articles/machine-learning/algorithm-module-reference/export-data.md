---
title: 'Adatexportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az adatok exportálása modult a Azure Machine Learning az eredmények, a köztes adatok és a munkafolyamatok munkafolyamatainak a Azure Machine Learningokon kívüli Felhőbeli tárolási célhelyekre való mentéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: 5964ed9773d45b809fc79252eabc754b1b04af84
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907984"
---
# <a name="export-data-module"></a>Adatmodul exportálása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal mentheti az eredményeket, a köztes adatok és a munkafolyamatok adatait a Felhőbeli tárolási célhelyekre. 

Ez a modul támogatja az adatexportálást a következő felhőalapú adatszolgáltatásokban:

- Azure Blob-tároló
- Azure-fájlmegosztás
- 1. generációs Azure Data Lake Storage
- 2. generációs Azure Data Lake Storage
- Azure SQL-adatbázis

Az adatexportálás előtt először regisztrálnia kell egy adattárt a Azure Machine Learning munkaterületen. További információ: [hozzáférés az Azure Storage-szolgáltatásokban tárolt adatokhoz](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Az exportálási beállítások konfigurálása

1. Adja hozzá az **adatexportálási** modult a folyamathoz a tervezőben. Ez a modul a **bemeneti és a kimeneti** kategóriában található.

1. Az exportálni kívánt adategységeket tartalmazó modulba csatlakoztasson **adatexportálást** .

1. Válassza az **adatexportálás** lehetőséget a **Tulajdonságok** ablaktábla megnyitásához.

1. Az **adattár**mezőben válasszon ki egy meglévő adattárt a legördülő listából. Létrehozhat egy új adattárt is. Tekintse át, hogyan [érheti el az Azure Storage-szolgáltatásokban tárolt Access-adatbázisokat](../how-to-access-data.md).

    > [!NOTE]
    > Bizonyos adattípusok egy másik adattípusként megadott SQL Database-oszlopba való exportálása nem támogatott. A célként megadott táblának nem kell elsőként létezni.

1. A **kimenet újragenerált**jelölőnégyzete határozza meg, hogy végrehajtja-e a modult a kimenet újralétrehozásához a futási időben. 

    Alapértelmezés szerint nincs kiválasztva, ami azt jelenti, hogy ha a modult korábban ugyanazokkal a paraméterekkel hajtották végre, a rendszer a legutóbbi futtatásból a futási idő csökkentése érdekében újra fogja használni a kimenetet. 

    Ha be van jelölve, a rendszer ismét végrehajtja a modult a kimenet újralétrehozásához.

1. Adja meg az elérési utat abban az adattárban, ahol az adattároló található. Az elérési út relatív elérési út. Az üres elérési utak vagy URL-elérési utak nem engedélyezettek.


1. **Fájlformátum**esetén válassza ki azt a formátumot, amelyben tárolni szeretné az adattárolást.
 
1. A folyamat elküldése.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
