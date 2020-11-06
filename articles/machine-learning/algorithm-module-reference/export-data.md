---
title: 'Adatexportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Designer adatexportálási moduljának használatával mentheti az eredményeket és a köztes adatAzure Machine Learningn kívüli adatmennyiséget.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: c6e3d56958168cd279c98a4ba4c021c2362c2694
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421311"
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

1. Az **adattár** mezőben válasszon ki egy meglévő adattárt a legördülő listából. Létrehozhat egy új adattárt is. Tekintse át, hogyan [érheti el az Azure Storage-szolgáltatásokban tárolt Access-adatbázisokat](../how-to-access-data.md).

    > [!NOTE]
    > Bizonyos adattípusok egy másik adattípusként megadott SQL Database-oszlopba való exportálása nem támogatott. A célként megadott táblának nem kell elsőként létezni.

1. A **kimenet újragenerált** jelölőnégyzete határozza meg, hogy végrehajtja-e a modult a kimenet újralétrehozásához a futási időben. 

    Alapértelmezés szerint nincs kiválasztva, ami azt jelenti, hogy ha a modult korábban ugyanazokkal a paraméterekkel hajtották végre, a rendszer a legutóbbi futtatásból a futási idő csökkentése érdekében újra fogja használni a kimenetet. 

    Ha be van jelölve, a rendszer ismét végrehajtja a modult a kimenet újralétrehozásához.

1. Adja meg az elérési utat abban az adattárban, ahol az adattároló található. Az elérési út relatív elérési út. Az üres elérési utak vagy URL-elérési utak nem engedélyezettek.


1. **Fájlformátum** esetén válassza ki azt a formátumot, amelyben tárolni szeretné az adattárolást.
 
1. A folyamat elküldése.

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
