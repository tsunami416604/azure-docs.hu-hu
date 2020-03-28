---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659872"
---
>[!IMPORTANT]
>Használhatja a létrehozott erőforrások at előfeltételei más Azure Machine Learning-oktatóanyagok és útmutató cikkek.

### <a name="delete-everything"></a>Mindent töröljön

Ha nem tervezi, hogy bármit is létrehoz, törölje a teljes erőforráscsoportot, hogy ne kelljen fizetnie.

1. Az Azure Portalon válassza **erőforráscsoportok** az ablak bal oldalán.
 
   ![Erőforráscsoport törlése az Azure Portalon](./media/aml-ui-cleanup/delete-resources.png)

1. A listában jelölje ki a létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

Az erőforráscsoport törlése a tervezőben létrehozott összes erőforrást is törli. 

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

Abban a tervezőben, ahol létrehozta a kísérletet, törölje az egyes kellékeket, jelölje ki őket, majd válassza a **Törlés** gombot.

Az itt létrehozott számítási cél *automatikusan automatikusan skálázza* a nulla csomópontok, ha nem használja. Ez a művelet a díjak minimalizálása érdekében történik.Ha törölni szeretné a számítási célt, tegye az alábbi lépéseket:

![Eszközök törlése](./media/aml-ui-cleanup/delete-asset.png)

Az adatkészletek regisztrációját a munkaterületről úgy oldhatja meg, hogy kijelöli az egyes adatkészleteket, majd a Regisztráció megszüntetése lehetőséget **választja.**

![Adatkészlet regisztrációjának megszüntetése](./media/aml-ui-cleanup/unregister-dataset1225.png)

Adatkészlet törléséhez nyissa meg a tárfiókot az Azure Portalon vagy az Azure Storage Explorer használatával, és manuálisan törölje ezeket az eszközöket.


